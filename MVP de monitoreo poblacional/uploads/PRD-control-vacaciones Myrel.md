# PRD — Aplicación de Control de Vacaciones (Semáforo)

| | |
|---|---|
| **Producto** | Control de Vacaciones — Semáforo de días pendientes |
| **Ámbito** | Región 1 Medicinas · ~300 colaboradores |
| **Versión del documento** | 1.0 (borrador) |
| **Fecha** | 6 de septiembre de 2026 |
| **Estado** | En definición · MVP construido |

---

## 1. Resumen ejecutivo

Herramienta analítica de RRHH/Operaciones que **clasifica mediante un semáforo (🟢🟡🔴) a los colaboradores según sus días de vacaciones pendientes** y sus períodos vencidos, para una población aproximada de 300 personas. Permite ver de un vistazo cuánta gente está al día, en alerta o en estado crítico, hacer drill-down por área y priorizar la planificación del disfrute para reducir la contingencia laboral por acumulación.

El MVP ya existe como dashboard web autocontenido con datos de ejemplo, carga por CSV y umbrales configurables.

---

## 2. Problema y contexto

La acumulación de vacaciones no disfrutadas genera **contingencia laboral y financiera** (pasivo por vacaciones y riesgo de incumplimiento de la LOTTT). Hoy el seguimiento suele hacerse en hojas de cálculo dispersas, sin una vista consolidada ni una señal clara de quién requiere acción. No existe un indicador único que responda, en segundos, "¿cuántas personas tienen días pendientes y cuán grave es?".

---

## 3. Objetivos y métricas de éxito

**Objetivos**
- Dar visibilidad inmediata del estado de vacaciones de toda la población.
- Priorizar la acción sobre los casos críticos (rojo) antes de cierres de período.
- Estandarizar el criterio de clasificación (semáforo) en un solo lugar.

**Métricas de éxito**
- Reducción del número de colaboradores en 🔴 rojo trimestre a trimestre.
- Reducción de días pendientes acumulados totales.
- Tiempo para obtener el estado consolidado: de horas (Excel manual) a segundos.
- Adopción por parte de RRHH y líderes de área como fuente única.

---

## 4. Usuarios y roles

| Rol | Necesidad principal |
|---|---|
| **Analista BI / RRHH** | Cargar datos, revisar el consolidado, exportar reportes. |
| **Director / Gerente de Operaciones** | Ver el semáforo poblacional y por área; identificar riesgos. |
| **Líder de área** | Consultar a su equipo y priorizar disfrutes. |

---

## 5. Alcance

**Dentro del alcance (MVP)**
- Semáforo poblacional con conteos y porcentajes.
- Indicadores generales (evaluados, días pendientes, períodos vencidos, promedio).
- Gráfico de distribución y desglose por área.
- Tabla con búsqueda, filtros (área y color) y ordenamiento.
- Carga de datos por CSV + plantilla descargable + exportación de la vista.
- Umbrales del semáforo configurables.
- Persistencia local de datos y configuración.

**Fuera del alcance (por ahora)**
- Integración automática con bases de datos corporativas.
- Gestión de solicitudes/aprobaciones de vacaciones (no es un flujo transaccional).
- Cálculo de nómina o liquidaciones.
- Autenticación y control de acceso por usuario.

---

## 6. Requisitos funcionales

**RF-01 · Semáforo poblacional.** Mostrar el número y porcentaje de colaboradores en verde, amarillo y rojo sobre el total de registros válidos.

**RF-02 · Clasificación individual.** Asignar a cada colaborador un color según las reglas de negocio (sección 8), con precedencia del color más severo.

**RF-03 · Indicadores generales.** Total evaluado, días pendientes acumulados, períodos vencidos totales y promedio de días pendientes por persona.

**RF-04 · Desglose por área.** Distribución del semáforo por área (Área 1, 2, 3, 6, 15…), ordenada por cantidad de casos críticos.

**RF-05 · Tabla operativa.** Listado con ID, nombre, área, antigüedad, días pendientes, períodos vencidos, último disfrute y estado; con búsqueda por nombre/ID, filtro por área y color, y columnas ordenables.

**RF-06 · Carga de datos (CSV).** Importar un archivo con reconocimiento flexible de columnas; calcular días pendientes como *generados − disfrutados* si no vienen explícitos.

**RF-07 · Validación de datos.** Excluir del conteo las filas con datos incompletos o inconsistentes (p. ej. pendientes negativos) e **informar** cuántas se excluyeron.

**RF-08 · Plantilla y exportación.** Descargar la plantilla CSV con el formato esperado y exportar la vista filtrada a CSV.

**RF-09 · Umbrales configurables.** Ajustar los límites de amarillo/rojo (días y períodos) y recalcular todo en tiempo real.

**RF-10 · Persistencia.** Conservar los datos cargados y la configuración entre sesiones; permitir restablecer los datos de ejemplo.

---

## 7. Requisitos no funcionales

- **Usabilidad:** el estado poblacional debe entenderse en menos de 5 segundos.
- **Rendimiento:** fluido con al menos 500 registros.
- **Responsivo:** utilizable en escritorio y móvil.
- **Accesibilidad:** contraste adecuado; el color nunca es el único portador de significado (siempre acompaña una etiqueta).
- **Confidencialidad:** los datos de personas se tratan como sensibles; sin exposición innecesaria.
- **Portabilidad:** un solo archivo, sin instalación.

---

## 8. Reglas de negocio

**Marco legal (Venezuela — LOTTT):** 15 días hábiles base por año de servicio, más 1 día adicional por cada año de antigüedad, hasta 15 días adicionales. Acumular períodos representa contingencia; la herramienta la visibiliza, no emite dictámenes legales.

**Cálculo:** `Días pendientes = Días generados − Días disfrutados`.

**Semáforo (criterio por defecto, configurable):**

| Color | Criterio | Interpretación |
|---|---|---|
| 🟢 Verde | 0–15 días pendientes **y** 0 períodos vencidos | Al día. |
| 🟡 Amarillo | 16–30 días **o** 1 período vencido | Alerta; planificar disfrute. |
| 🔴 Rojo | >30 días **o** ≥2 períodos vencidos | Crítico; acción inmediata. |

**Precedencia:** si un colaborador cumple más de un criterio, prevalece el más severo (rojo > amarillo > verde).

> ⚠️ Los umbrales por defecto son una propuesta y deben validarse con RRHH antes de uso oficial.

---

## 9. Modelo de datos (por colaborador)

| Campo | Tipo | Notas |
|---|---|---|
| id / cédula | texto | Identificador único |
| nombre | texto | |
| área | texto | Área / gerencia / región |
| fecha_ingreso | fecha | Para calcular antigüedad |
| dias_generados | número | Según antigüedad |
| dias_disfrutados | número | |
| dias_pendientes | número | Calculado si no se provee |
| periodos_vencidos | entero | Años completos sin disfrutar |
| ultimo_disfrute | fecha | Opcional |
| estado | derivado | verde / amarillo / rojo |

---

## 10. Flujos principales

1. **Consulta rápida:** el usuario abre la app → ve el semáforo poblacional e indicadores.
2. **Drill-down:** filtra por área o color → revisa la tabla priorizada.
3. **Carga de datos reales:** descarga la plantilla → completa → importa CSV → validación y recálculo.
4. **Acción:** exporta la lista de casos rojos para gestionar el disfrute.
5. **Ajuste de criterio:** modifica umbrales → todo se recalcula.

---

## 11. Roadmap propuesto

**Fase 1 — MVP (completada):** dashboard, semáforo, áreas, tabla, CSV, umbrales, persistencia.

**Fase 2 — Datos vivos:** conexión a la fuente corporativa (Databricks / Oracle / Excel en Drive) para actualización automática.

**Fase 3 — Análisis avanzado:** módulo de **simulación** ("¿cuántos pasan a verde si el grupo amarillo disfruta X días?"), tendencias históricas y alertas.

**Fase 4 — Distribución ejecutiva:** exportación del resumen a PowerPoint/PDF y envío programado a líderes.

---

## 12. Riesgos y supuestos

- **Calidad de datos:** el valor depende de datos completos y consistentes (mitigado con validación y reporte de exclusiones).
- **Umbrales:** deben ser validados por RRHH/Legal para tener carácter oficial.
- **Alcance:** no sustituye un sistema transaccional de solicitudes; es una capa de visibilidad y control.
- **Privacidad:** el manejo de datos personales debe alinearse con las políticas internas.

---

## 13. Preguntas abiertas

1. ¿Cuáles son los umbrales oficiales de RRHH para amarillo/rojo?
2. ¿Cuál será la fuente de datos definitiva y con qué frecuencia se actualiza?
3. ¿Se requiere control de acceso por rol/área?
4. ¿Debe incluir histórico y tendencia, o basta la foto actual?
