# Roadmap de telemetría — GardenLog

Este documento registra las decisiones tomadas para añadir monitoreo de plantas con sensores a GardenLog. Su objetivo es que una futura sesión de implementación pueda comenzar aquí, sin tener que reconsiderar desde cero la base de datos, la arquitectura o el orden del trabajo.

## Decisión principal

La implementación se divide en dos etapas:

1. **Primera etapa: Supabase Free.** Es la opción recomendada para construir y validar el sistema con rapidez, sin administrar servidores y sin costo inicial.
2. **Largo plazo: servicio local.** Cuando el volumen, la dependencia de la nube o los requisitos de privacidad lo justifiquen, la telemetría se trasladará a una Raspberry Pi u otro equipo local.

No se recomienda Airtable como almacén de telemetría. Podría servir como editor manual de contenido, pero no está pensado para series de tiempo ni para la ingestión continua de lecturas. GardenLog tampoco necesita mantener simultáneamente Airtable y Supabase.

## Estado actual

- Astro 5 genera un sitio estático.
- Hay 7 fichas en `src/data/plants.json`.
- Las páginas se generan en `src/pages/plants/[slug].astro`.
- Las imágenes se sirven desde `public/images/`.
- Aún no existen cuentas de usuario, API, base de datos, sensores ni panel de telemetría.
- Los datos editoriales pueden continuar en el JSON durante la primera implementación. Migrarlos a PostgreSQL no es requisito para conectar sensores.

## Alcance inicial

El primer sistema deberá:

- asociar cada dispositivo con una planta;
- recibir humedad del suelo, temperatura, humedad ambiental y luz;
- mostrar la última lectura y cuándo fue recibida;
- actualizar un panel abierto sin recargar la página;
- conservar históricos para generar gráficas;
- señalar datos atrasados y valores fuera de los umbrales configurados;
- seguir mostrando las fichas estáticas si Supabase no está disponible.

No forman parte del MVP: riego automático, aplicación móvil nativa, predicciones con IA, autenticación pública, notificaciones push ni migrar todas las fichas editoriales a la base de datos.

## Arquitectura recomendada para la primera etapa

```text
Sensores
   │
   ▼
ESP32 ── HTTPS ──► función de ingestión ──► Supabase/PostgreSQL
                                                   │
                                      consulta + Realtime
                                                   │
                                                   ▼
                                             GardenLog/Astro
```

### Decisiones técnicas

- El ESP32 enviará datos mediante HTTPS. MQTT queda reservado para la solución local, donde aporta más valor.
- Una lectura contendrá todas las métricas tomadas en el mismo instante. No se creará una fila separada por cada métrica.
- La frecuencia inicial será **una lectura cada 5 minutos**. Debe ser configurable en el firmware.
- La ingestión pasará por una función o endpoint protegido; el ESP32 no tendrá credenciales administrativas de PostgreSQL.
- El navegador solo tendrá una clave pública y permisos de lectura limitados mediante Row Level Security (RLS).
- Las fichas y fotografías actuales seguirán siendo estáticas. La telemetría será una capa dinámica y tolerante a fallos.
- Las fechas se almacenarán en UTC y se presentarán en la zona horaria del visitante.

## Modelo de datos inicial

### `plants`

Relaciona las fichas actuales con la telemetría.

| Campo | Tipo | Notas |
|---|---|---|
| `id` | UUID | Clave primaria |
| `slug` | texto único | Debe coincidir con `plants.json` |
| `name` | texto | Nombre visible |
| `moisture_min` | número nullable | Umbral inferior |
| `moisture_max` | número nullable | Umbral superior |
| `temperature_min` | número nullable | Grados Celsius |
| `temperature_max` | número nullable | Grados Celsius |
| `created_at` | timestamptz | Generado por la base de datos |

### `devices`

| Campo | Tipo | Notas |
|---|---|---|
| `id` | UUID | Clave primaria |
| `plant_id` | UUID | Referencia a `plants` |
| `name` | texto | Nombre legible del dispositivo |
| `hardware_id` | texto único | Identificador del ESP32 |
| `enabled` | booleano | Permite revocar un dispositivo |
| `last_seen_at` | timestamptz nullable | Facilita detectar desconexiones |
| `created_at` | timestamptz | Generado por la base de datos |

### `sensor_readings`

| Campo | Tipo | Notas |
|---|---|---|
| `id` | bigint identity | Clave primaria eficiente |
| `device_id` | UUID | Referencia a `devices` |
| `recorded_at` | timestamptz | Momento de la medición |
| `received_at` | timestamptz | Momento de llegada al servidor |
| `soil_moisture` | número nullable | Guardar también cómo se calibró |
| `temperature_c` | número nullable | Temperatura ambiental |
| `humidity_pct` | número nullable | Humedad relativa |
| `light_lux` | número nullable | Iluminación |
| `battery_v` | número nullable | Útil si el dispositivo usa batería |
| `firmware_version` | texto nullable | Ayuda a diagnosticar cambios |

Índices iniciales:

- `(device_id, recorded_at desc)` para obtener la lectura reciente y los históricos;
- `recorded_at` para las tareas de retención.

No se debe almacenar una clave secreta del dispositivo en texto plano. Se guardará un hash o se validará un token desde los secretos del endpoint de ingestión.

## Fases de implementación

### Fase 0 — Definir el comportamiento

- [ ] Confirmar qué sensores físicos se usarán y sus unidades.
- [ ] Definir la calibración de humedad: valor seco, valor en agua y rango mostrado.
- [ ] Elegir si el ESP32 tendrá alimentación continua o batería.
- [ ] Establecer los umbrales iniciales por planta.
- [ ] Decidir cuándo una lectura se considera atrasada. Valor inicial sugerido: 15 minutos.

**Termina cuando:** existe una tabla breve con el modelo de sensor, pines, unidades, calibración y planta asignada.

### Fase 1 — Preparar Supabase Free

- [ ] Crear un único proyecto en el plan gratuito.
- [ ] Añadir migraciones SQL versionadas al repositorio.
- [ ] Crear `plants`, `devices` y `sensor_readings` con sus restricciones e índices.
- [ ] Insertar las 7 plantas actuales usando el `slug` como vínculo estable.
- [ ] Activar RLS en todas las tablas expuestas.
- [ ] Permitir lectura pública únicamente de los campos necesarios para el panel.
- [ ] Mantener escrituras públicas deshabilitadas.
- [ ] Documentar las variables de entorno en `.env.example`; nunca versionar secretos reales.

**Termina cuando:** una migración reproducible crea el esquema y una consulta anónima puede leer datos permitidos, pero no insertar ni modificar registros.

### Fase 2 — Construir la ingestión antes del hardware

- [ ] Crear un endpoint de ingestión autenticado.
- [ ] Validar identificador del dispositivo, token, tipos, rangos y fecha.
- [ ] Rechazar campos desconocidos y cargas demasiado grandes.
- [ ] Registrar una lectura y actualizar `devices.last_seen_at`.
- [ ] Hacer la operación idempotente o tolerante a reintentos.
- [ ] Crear un pequeño script simulador para enviar lecturas cada 5 minutos.
- [ ] Probar respuestas de éxito, token inválido, dispositivo deshabilitado y datos fuera de formato.

Ejemplo conceptual del cuerpo enviado:

```json
{
  "deviceId": "esp32-cilantro-01",
  "recordedAt": "2026-07-17T16:00:00Z",
  "soilMoisture": 47.2,
  "temperatureC": 29.1,
  "humidityPct": 72.4,
  "lightLux": 8400,
  "batteryV": 4.08,
  "firmwareVersion": "0.1.0"
}
```

**Termina cuando:** el simulador produce un histórico válido durante al menos 24 horas sin intervención manual.

### Fase 3 — Integrar el panel en Astro

- [ ] Instalar y configurar el cliente de Supabase con variables públicas limitadas.
- [ ] Crear un componente de estado actual para cada ficha de planta.
- [ ] Mostrar valores, unidades, hora de la lectura y estado de conexión.
- [ ] Suscribirse a nuevas lecturas con Realtime solo mientras el panel esté abierto.
- [ ] Incorporar estados de carga, sin datos, datos atrasados y error de red.
- [ ] No bloquear el contenido estático si falla la consulta dinámica.
- [ ] Añadir una vista histórica inicial de 24 horas.
- [ ] Respetar accesibilidad, foco visible y `prefers-reduced-motion`.

**Termina cuando:** `/plants/<slug>` muestra la lectura más reciente, se actualiza al llegar otra y conserva la ficha actual aunque la base de datos no responda.

### Fase 4 — Conectar el primer ESP32

- [ ] Leer los sensores con intervalos no bloqueantes.
- [ ] Conectar a Wi-Fi y sincronizar la hora.
- [ ] Enviar una lectura cada 5 minutos mediante HTTPS.
- [ ] Implementar reintentos con espera creciente, sin crear una tormenta de solicitudes.
- [ ] Almacenar temporalmente lecturas si se pierde internet, dentro de la capacidad del dispositivo.
- [ ] No imprimir tokens en logs de producción.
- [ ] Verificar calibración comparando lecturas con una referencia conocida.

**Termina cuando:** un dispositivo real opera durante 7 días y el panel distingue correctamente entre conectado, atrasado y desconectado.

### Fase 5 — Alertas y calidad de datos

- [ ] Aplicar umbrales por planta, no un único umbral global.
- [ ] Evitar alertas por una lectura aislada; requerir varias lecturas consecutivas.
- [ ] Detectar valores imposibles, saltos abruptos y sensores sin comunicación.
- [ ] Añadir un registro de eventos para riego, fertilización, trasplante y mantenimiento.
- [ ] Comparar eventos de riego con la respuesta de humedad del suelo.
- [ ] Definir alertas visibles en el sitio antes de añadir correo o notificaciones.

**Termina cuando:** el sistema diferencia un problema persistente de ruido normal del sensor.

### Fase 6 — Retención, respaldo y operación

- [ ] Medir mensualmente filas, tamaño de base de datos y mensajes Realtime.
- [ ] Mantener datos detallados recientes y crear resúmenes horarios o diarios para históricos largos.
- [ ] Definir una política inicial de retención; por ejemplo, lecturas de 5 minutos durante 90 días.
- [ ] Exportar periódicamente esquema y datos importantes, porque el plan gratuito no incluye respaldos automáticos.
- [ ] Añadir una página o sección de salud con última lectura por dispositivo.
- [ ] Documentar cómo rotar un token, reemplazar un sensor y recuperar la ingestión.

**Termina cuando:** el crecimiento del almacenamiento es predecible y existe una copia recuperable fuera de Supabase.

### Fase 7 — Expandir gradualmente

- [ ] Añadir nuevos dispositivos uno por uno.
- [ ] Evaluar consumo de energía y conectividad antes de desplegar en todas las plantas.
- [ ] Añadir rangos históricos de 7, 30 y 90 días.
- [ ] Incorporar autenticación solo si se publican datos privados o controles administrativos.
- [ ] Considerar automatización de riego únicamente después de validar sensores, alertas y comportamiento ante fallos.

## Uso esperado del plan gratuito

A julio de 2026, el plan gratuito de Supabase anuncia 500 MB de base de datos, 2 millones de mensajes Realtime al mes, 500,000 invocaciones de funciones y hasta dos proyectos activos. Los excesos del plan gratuito no se facturan automáticamente; el servicio puede restringirse al alcanzar sus límites. Las condiciones deben comprobarse de nuevo antes de implementar, porque pueden cambiar.

Referencias oficiales:

- [Supabase Pricing](https://supabase.com/pricing)
- [Supabase Realtime Pricing](https://supabase.com/docs/guides/realtime/pricing)
- [Supabase Cost Control](https://supabase.com/docs/guides/platform/cost-control)
- [Supabase Free Project Pausing](https://supabase.com/docs/guides/platform/free-project-pausing)

Una muestra cada 5 minutos produce 288 filas diarias por dispositivo. Con 7 dispositivos serían 2,016 filas al día, antes de aplicar retención o agregación. El tamaño real debe medirse después del piloto; no se debe estimar solo por el contenido visible de cada fila, porque PostgreSQL también usa espacio para índices y metadatos.

La actividad diaria de los sensores debería impedir una pausa por inactividad, pero esto no sustituye el monitoreo. El panel no necesita mantener una suscripción Realtime abierta cuando nadie lo está viendo.

## Cuándo pasar a una solución local

Supabase Free sigue siendo la recomendación mientras cumpla el MVP sin mantenimiento excesivo. La migración local se inicia cuando ocurra al menos una de estas condiciones:

- el almacenamiento se acerca de forma sostenida al 70 % del límite;
- la retención necesaria no cabe sin borrar datos útiles;
- se necesita operar durante interrupciones de internet;
- se requiere control total sobre respaldos y privacidad;
- hay suficientes dispositivos o frecuencia para que la nube deje de ser práctica;
- se necesita MQTT para más dispositivos, automatizaciones o comunicación local;
- mantener el sistema local cuesta menos esfuerzo que administrar los límites de la nube.

No se migrará únicamente porque la solución local parezca más completa. Primero se validará el sistema con Supabase y un sensor real.

## Arquitectura local de largo plazo

```text
ESP32 ── MQTT ──► Mosquitto ──► servicio de ingestión
                                      │
                                      ▼
                          PostgreSQL o base de series temporales
                                      │
                       ┌──────────────┴──────────────┐
                       ▼                             ▼
                API de GardenLog               respaldos
```

La instalación puede vivir en una Raspberry Pi, mini PC o equipo doméstico siempre encendido. Componentes candidatos:

- Mosquitto como broker MQTT;
- un servicio pequeño en Node.js para validar e insertar lecturas;
- PostgreSQL para minimizar cambios respecto a Supabase;
- almacenamiento externo o segunda máquina para respaldos;
- acceso remoto mediante una conexión segura, nunca exponiendo directamente PostgreSQL o MQTT a internet.

PostgreSQL es la ruta de migración más sencilla porque Supabase ya lo utiliza. Una base especializada en series temporales solo se justifica si las consultas o el volumen lo requieren.

## Estrategia de migración

1. Mantener nombres y tipos del esquema independientes de funciones propietarias cuando sea razonable.
2. Versionar todas las migraciones SQL.
3. Encapsular las consultas del frontend en un módulo de acceso a datos.
4. Exportar PostgreSQL desde Supabase e importarlo en el servidor local.
5. Cambiar el endpoint de ingestión y la URL de lectura mediante variables de entorno.
6. Ejecutar ambos destinos temporalmente o importar un corte final controlado.
7. Validar conteos, últimas lecturas, históricos y zonas horarias antes de retirar Supabase.

## Seguridad mínima obligatoria

- Nunca incluir la `service_role` de Supabase en código enviado al navegador o firmware público.
- Aplicar RLS a todas las tablas expuestas.
- Usar un token diferente por dispositivo para poder revocarlo individualmente.
- Rotar tokens comprometidos y permitir deshabilitar dispositivos.
- Validar en el servidor todos los campos y rangos.
- Aplicar límites de frecuencia al endpoint.
- Usar HTTPS en la nube y TLS o una red local aislada para MQTT.
- Versionar `.env.example`, pero mantener `.env` y secretos fuera de Git.

## Orden recomendado para la próxima sesión

La próxima implementación debe comenzar con **Fase 0 y Fase 1**. El primer cambio de código debería incluir:

1. migraciones SQL para las tres tablas;
2. políticas RLS;
3. datos iniciales de las 7 plantas;
4. `.env.example`;
5. instrucciones de configuración local.

Después se construirá el simulador y la ingestión. No conviene comprar o integrar todos los sensores antes de demostrar el flujo completo con datos simulados y un solo ESP32.
