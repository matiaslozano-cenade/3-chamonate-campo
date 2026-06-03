# 3-chamonate-campo

Dashboard de rentabilidad de **Campo** de Chamonate. Ingresos vs costos contables desde el Libro Mayor, de lo más general a lo más específico, con **Estado de Resultado (EERR) por cuenta** en cada nivel.

## Stack

- HTML estático + Tailwind (CDN) + Chart.js + Supabase JS
- Sin build · deploy en Vercel
- URL producción: `https://3-chamonate-kpi-campo.vercel.app`

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

## Pestañas

General · Mensual · Por Sector · Por Cuartel. Cada una termina con un **Estado de Resultado (EERR) por cuenta** que respeta el filtro activo (año · mes · sector · cuartel): Ingresos → costos desglosados por cuenta → Total costos → Resultado → Margen %.

## Reglas de negocio (contables)

- **Ingresos:** cuentas `4%` → `sum(haber − debe)`.
- **Costos:** cuentas `3%` con `debe > 0` → `sum(debe)`.
- El EERR (`v_3_campo_cuenta`) usa exactamente la misma lógica que `v_3_campo_cc`, por lo que los totales cuadran al peso.

## Desarrollo

Abrir `index.html` directamente en el navegador (lee Supabase con la anon key embebida).
