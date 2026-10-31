# Fase de Inception (Inicio) — AgroCuadrilla

## 1. Visión y Caso de Negocio
* **Problema:** En el trabajo agrícola de campo (como en Chaparrosa, Villa de Cos), los encargados de cuadrilla administran asistencias, labores (por día o destajo), préstamos/vales y gastos de traslado en libreta de papel. Esto genera errores en la liquidación sabatina y pérdidas financieras al no contemplar los costos operativos del transporte.
* **Propósito del Sistema:** Proporcionar una aplicación móvil nativa/híbrida que automatice el control de asistencias, registro de avances, deducción de préstamos y gastos de transporte, calculando la ganancia neta real del cuadrillero.
* **Viabilidad Técnica y Operativa:** 
  * **Dispositivo:** Smartphone Android (portátil y accesible en el campo).
  * **Estrategia de Conectividad:** *Offline-First* (almacenamiento en base de datos local SQLite/Room y sincronización asíncrona a la nube cuando haya cobertura o conexión Wi-Fi).

---

## 2. Modelo de Casos de Uso (Inicial ~10%)

### Lista de Casos de Uso Identificados:
1. CU01: Registrar Asistencia y Labor Diaria *(Analizado en detalle)*
2. CU02: Registrar Préstamo/Vale a Trabajador
3. CU03: Registrar Gasto de Traslado (Combustible/Vehículo)
4. CU04: Calcular y Liquidar Nómina Sabatina
5. CU05: Sincronizar Datos Locales con el Servidor Nube
6. CU06: Gestionar Catálogo de Trabajadores y Predios

---

### Detalle del Caso de Uso Crítico (10% de Análisis)

#### **CU01: Registrar Asistencia y Labor Diaria**
* **Actor Principal:** Encargado de Cuadrilla (Mayordomo).
* **Precondición:** El encargado ha iniciado sesión localmente en la aplicación móvil.
* **Flujo Principal:**
  1. El encargado selecciona la fecha, el predio/parcela y la labor a realizar (ej. corte, deshierbe).
  2. El sistema despliega la lista de trabajadores registrados en la cuadrilla.
  3. El encargado marca la asistencia de cada trabajador.
  4. Para pagos a destajo (botes/surcos), el encargado ingresa la cantidad recolectada/trabajada por persona.
  5. El sistema calcula automáticamente el importe ganado en el día por cada trabajador.
  6. El encargado confirma el registro y el sistema lo almacena en la **Base de Datos Local (Offline)**.
* **Flujo Alternativo (Sin Red):**
  * Si no hay conexión a internet, el sistema almacena la transacción localmente con estado `Pendiente_Sincronizacion` y notifica que se actualizará al recuperar señal.

---

## 3. Especificación Suplementaria (Requisitos No Funcionales Críticos)
* **Usabilidad:** Interfaz táctil simplificada con botones grandes para facilitar la captura rápida de datos en campo.
* **Conectividad / Disponibilidad:** Operatividad 100% offline para registros diarios en parcelas sin señal de red celular.
* **Rendimiento:** Tiempos de respuesta menores a 1 segundo en el guardado de registros locales.
* **Persistencia:** Almacenamiento estructurado local en el dispositivo móvil con sincronización segura mediante API REST/JSON.

---

## 4. Glosario del Dominio
* **Cuadrilla:** Grupo de trabajadores agrícolas a cargo de un supervisor o encargado.
* **Cuadrillero / Mayordomo:** Encargado de la logística, transporte y pago de la cuadrilla.
* **Destajo:** Modalidad de pago basada en la cantidad de trabajo realizado (ej. botes de chile recolectados, metros de surco) en lugar de un sueldo fijo por día.
* **Vale / Préstamo:** Dinero adelantado al trabajador durante la semana que debe ser descontado en la raya o liquidación sabatina.
* **Troca / Vehículo de Traslado:** Transporte utilizado para mover a la cuadrilla hacia los predios agrícolas.
* **Raya / Liquidación Sabatina:** Pago final semanal realizado cada sábado tras descontar adelantos y agregar compensaciones.

---

## 5. Lista de Riesgos y Mitigación
1. **Pérdida de señal celular prolongada en campo:**
   * *Mitigación:* Arquitectura *Offline-First* con persistencia local en SQLite.
2. **Resistencia al uso de tecnología por parte del cuadrillero:**
   * *Mitigación:* Diseño UI centrado en flujos rápidos de pocos toques.
3. **Pérdida de datos por descarga o daño del dispositivo móvil:**
   * *Mitigación:* Respaldos automáticos en almacenamiento local y sincronización inmediata al detectar red Wi-Fi o datos móviles.

---

## 6. Plan de la Primera Iteración (Fase de Elaboración)
* **Objetivo:** Diseñar el diagrama de clases del dominio, estructurar el esquema relacional de la base de datos local y crear un prototipo funcional (MVP) de la interfaz de captura de asistencia en Android.



---

## Especificación Suplementaria (Categorización FURPS+)

### 1. F — Functionality (Funcionalidades y Capacidades)
* **Control de Asistencia y Avances:** Registro matutino de asistencias por trabajador y captura diaria de avances por destajo (botes, surcos, rejas).
* **Gestión Financiera de Cuadrilla:** Cálculo automatizado de la raya sabatina descontando los préstamos/vales emitidos en la semana.
* **Control Logístico:** Registro de gastos operativos del transporte (combustible, mantenimiento de vehículo) para determinar la ganancia neta real del encargado.

### 2. U — Usability (Usabilidad y Factores Humanos)
* **Interfaz de Campo:** Diseño visual táctil con botones amplios y alto contraste, optimizado para ser operado con una sola mano bajo luz solar directa.
* **Flujo Eficiente:** Captura de asistencia y avance diario parametrizado a un máximo de 3 toques por trabajador.

### 3. R — Reliability (Fiabilidad y Tolerancia a Fallos)
* **Almacenamiento Seguro:** Persistencia local mediante SQLite/Room para prevenir pérdida de registros de nómina por descarga de batería o reinicios.
* **Tolerancia a Desconexión:** Transacciones locales atómicas que garantizan consistencia en los datos aunque no exista cobertura de red celular.

### 4. P — Performance (Rendimiento)
* **Tiempos de Respuesta:** Confirmación y guardado de transacciones locales en menos de 1 segundo.
* **Eficiencia Energética:** Uso optimizado de memoria y procesos en segundo plano para permitir jornadas completas en campo (8-10 horas) sin recarga del dispositivo.

### 5. S — Supportability (Soportabilidad y Mantenibilidad)
* **Compatibilidad:** Soporte para versiones de Android 8.0 (API 26) o superior en smartphones de gama baja/media.
* **Arquitectura Modular:** Separación clara entre capas de lógica de negocio (cálculo de nómina) y lógica de almacenamiento local para facilitar mantenimiento futuro.

### 6. + — Factores Auxiliares y Restricciones
* **Implementación:** Desarrollo móvil en entorno Android nativo o híbrido con estrategia *Offline-First*.
* **Interfaz:** Conexión asíncrona mediante API REST/JSON con el servidor backend en la nube al detectar conexión Wi-Fi o datos móviles.
* **Operativo y Legal:** Cumplimiento con las políticas de privacidad en la gestión de datos de trabajadores y adecuación a los esquemas de pago tradicionales de la región.