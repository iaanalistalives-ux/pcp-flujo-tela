# CLAUDE.md — Flujo de tela de PCP (pcp-flujo-tela)

Las reglas generales de trabajo están en el repo privado `lives-instrucciones` (CLAUDE.md). Este archivo solo describe esta herramienta.

## Qué hace
Cinco tableros de PCP para ver dónde se traba la producción, del corte al despacho. **Ninguno usa la base de datos**: el usuario arrastra un Excel, que se lee en el navegador (SheetJS), y no se guarda nada. Son dos familias de código; los cinco archivos son distintos.
- `flujo-corte/index.html` — "Flujo de Corte". Carga **dos Excel**:
  - "Pedidos Situación Actual", hoja `DATA`: lee por posición fija de columna (grupo, estilo, categoría, minutos por cortar, fecha hito, semana y año del hito). Es frágil si cambia el diseño del Excel.
  - "Flujo Producción Diaria": envíos a corte, prendas cortadas propias y terceros, minutos propios y de servicios, y habilitado.
  - Vista semanal o mensual; filtros de mes, semana, categoría (Semi Moda, Moda, Box Moda, Especial, Otros), solo urgentes (hito en 15 días o menos) y búsqueda.
  - Pestañas: Carga pendiente, Producción ejecutada y "Dónde atacar primero" (3 rankings por grupo, estilo y categoría).
  - KPIs: carga pendiente (minutos por cortar), minutos de corte ejecutados contra el período anterior, kilos enviados, prendas cortadas y % tercerizado (terceros ÷ propias + terceros, sumando antes de dividir).
- `flujo-costura/index.html` — "Flujo Mínimo de Costura". Un Excel `PEDIDOS_SITUACION_ACTUAL` con las hojas `DATA`, `detalle` y `Hoja2`; cruza la OP con la fecha de hito de la otra hoja.
  - Parámetros manuales: capacidad de planta por semana, eficiencia, minutos por taller y base de la semana (fecha de exportación, hito de costura o hito de corte).
  - Minutos por coser = minutos de costura × (requeridas − confeccionadas).
  - Déficit = lo que pasa de la capacidad efectiva de cada semana. Pestañas: Resumen semanal, Mix y ranking, Tablas y "Contraste con PCP" (pegar lo del correo y comparar; marca diferencias de más de 1 %).
  - Descarga el detalle en CSV.
- `flujo-movil-acabados/index.html` — "Flujo Móvil de Acabados". Un Excel con la hoja `Produccion Diaria`.
  - Flujo móvil = suma del encajado de los últimos N días (6 por defecto) ÷ suma del factor de esos días (1 de lunes a viernes, 0,5 sábado y domingo). El flujo mínimo se toma de la última cifra del Excel.
  - KPIs: flujo móvil actual, brecha contra el mínimo, % de días sobre el mínimo, prendas encajadas (propias y servicios) y encajado menos despachado. Semáforo: verde si cumple, ámbar hasta 20 % por debajo, rojo si es peor.
- `tela-teñida/index.html` — "Stocks de tela teñida por despachar" (carpeta con ñ: en la web la dirección lleva `tela-te%C3%B1ida`).
  - Una foto del almacén, una fila por partida: el Excel con la hoja `Rpt`. Filtros de grupo, aprobación de corte y lavado de paños; tabla ordenable con regla 500.
  - KPIs: stock por despachar en kilos, partidas, % aprobadas para corte y pendientes de aprobación comercial.
  - Es la única con su Excel propio; el bloque LIVES lo pasa al formato LIVES.
- `torre-control/index.html` — "Torre de Control PCP" (corte → costura → acabados). El mismo Excel de pedidos (hoja `DATA`, más `PROVEEDORES DE COSTURA` si se sube).
  - Parámetros manuales: capacidad de corte (minutos por semana), de costura (minutos por semana), de acabados (prendas por semana) y eficiencia.
  - Por etapa: carga, capacidad efectiva, semanas de carga = carga ÷ capacidad efectiva y OP en riesgo. Semáforo: verde hasta 1,5 semanas, ámbar hasta 3, rojo más. El cuello de botella es la etapa con más semanas de carga.
  - Pestañas: Carga por semana y Talleres de costura (enviadas, realizadas, stock en taller y % de avance por proveedor).
- Todos pueden "Exportar para observadores" o "Exportar dashboard interactivo": un HTML autónomo con los datos adentro, solo lectura. Imprimir A4.
- Ninguno tiene una meta propia de la empresa: las capacidades y eficiencias son parámetros que se escriben a mano.

## Base de datos
Ninguna. No hay tablas, funciones ni Storage de estas pantallas.

## Quién la usa
No aparece en el registro de entradas del portal de los últimos 60 días (al 21/10/2026).

## Quién puede registrar
Nadie registra: no hay guardado. Cualquiera que abra la página puede subir un Excel y verlo.

## Pendientes vistos al escribir esto (21/10/2026)
- Siguen dependiendo de Excel: el reporte "Pedidos Situación Actual" debería salir directo de los sistemas de LIVES, con tablas y funciones de lectura (lo que se necesita: pedidos con minutos por cortar y por coser, hitos, producción diaria y stock de tela teñida). Choca con la regla "todo sale de la base".
- Las capacidades y la eficiencia deberían quedar en una tabla de parámetros, no escritas en pantalla.
- El detalle de torre de control habla de un CSV que esa pantalla no tiene (el CSV es el de costura).
