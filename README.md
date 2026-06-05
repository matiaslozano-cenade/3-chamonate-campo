# 3-chamonate-campo

Dashboard de rentabilidad de **Campo** de Chamonate. Ingresos vs costos contables desde el Libro Mayor, de lo más general a lo más específico, con **Estado de Resultado (EERR) por cuenta** en cada nivel.

## Stack

- HTML estático + Tailwind (CDN) + Chart.js + chartjs-plugin-datalabels + Supabase JS
- Sin build · deploy en Vercel
- URL producción: `https://3-chamonate-kpi-campo.vercel.app`

## Visualizaciones

Mismo sistema visual que el dashboard de Maquinaria: gráficos en contenedores de altura fija (`chart-box`), **etiquetas de datos siempre visibles** sobre barras/puntos, combo **barras (ingresos) + línea (costos)**, gráfico **"Costos por cuenta"** (barras por cuenta contable), KPIs con borde de color y el **EERR al lado del gráfico**.

## Datos (Supabase `wjbwccacjdkuejcriode`)

| Vista / Tabla | Uso |
| --- | --- |
| `v_3_campo_anual` / `v_3_campo_mensual` | Totales por año / mes |
| `v_3_campo_grupo` | Totales por sector (grupo) |
| `v_3_campo_cc` | Totales por cuartel (centro de costo) · año + mes |
| `v_3_campo_cuenta` | Ingresos/costos **por cuenta contable** — alimenta el EERR (cuadra exacto con `v_3_campo_cc`) |
| `v_3_campo_detalle` | Detalle línea a línea del Libro Mayor — alimenta drill-down y descarga CSV |
| `3_chamonate_centros_costo` (`linea_negocio='campo'`) | Catálogo de cuarteles + sector |
| `3_chamonate_libro_mayor` | Movimientos contables (drill-down) |

## Temporada agrícola (jun → may) — particularidad clave de Campo

Campo **no** se analiza por año calendario (ene–dic) sino por **temporada agrícola de 12 meses, del 1 de junio al 31 de mayo**. La temporada T va de **junio del año T a mayo del año T+1** y se etiqueta `Temp. YY-YY` (ej: `Temp. 24-25` = jun 2024 → may 2025).

- Asignación: `temporada = (mes >= 6) ? año : año - 1` (junio inicia la nueva temporada).
- Todo el eje temporal del dashboard (pills, comparativo, mensual, EERR, histórico, drill-down y CSV) razona en temporadas; los meses se ordenan **jun → may**.
- El drill-down al Libro Mayor y la descarga CSV traducen la temporada a los dos años contables reales que cruza.

## Pestañas

General · Mensual · **Por Grupo CC** · **Por Sucursal** · Por Cuartel.

- **Por Grupo CC**: resumen global (todas las sucursales) por **cultivo**, con drill **Cultivo → Tipo de cultivo**; respeta los filtros de temporada/mes. (Equivale a la fila "GRUPO CCOSTO" del Power BI.)
- **Por Sucursal**: drill jerárquico **Sucursal (predio) → Cultivo → Tipo de cultivo**. Cada nivel termina con un **Estado de Resultado (EERR) por cuenta** y un gráfico "Costos por cuenta" que respetan el filtro activo: Ingresos → costos desglosados por cuenta → Total costos → Resultado → Margen %.

## Dimensiones: campo / cultivo / tipo de cultivo

Cada centro de costo está clasificado en el catálogo (`3_chamonate_centros_costo`):
- **`grupo`** = **campo / predio** (Pichiguao, El Cerro, San Carlos, San Bernardo-Retamos, Rosario, Nos).
- **`cultivo`** = especie en forma fruta (Manzana, Cereza, Ciruela, Durazno, Nectarín, Caqui, Hortalizas) o, para overhead, la función (Administración, Riego/Infraestructura, Producción general, Maquinaria, Inversión).
- **`variedad`**: variedad (Manzana → Gala / Pink Lady / Granny Smith / Rosy Glow / Brookfield / Buckeye / Red Delicious; Cereza → Santina / Regina-Skeena / Lapins / Frisco / Gartayro). El **"tipo de cultivo"** que muestra la UI = `cultivo + variedad` (ej. *Manzana Gala*).

La clasificación se hizo por patrón sobre el nombre del cuartel y vive en el catálogo: corregir una fila reclasifica todo el dashboard. Las columnas `cultivo`/`variedad` se exponen en la vista `v_3_campo_cc`.

## Reglas de negocio (contables)

- **Ingresos:** cuentas `4%` → `sum(haber − debe)`.
- **Costos:** cuentas `3%` con `debe > 0` → `sum(debe)`.
- El EERR (`v_3_campo_cuenta`) usa exactamente la misma lógica que `v_3_campo_cc`, por lo que los totales cuadran al peso.

## Desarrollo

Abrir `index.html` directamente en el navegador (lee Supabase con la anon key embebida).
