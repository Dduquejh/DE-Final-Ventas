# **En la carpeta de evidencias están las capturas de las ejecuciones y las evidencias**

1. ¿Por qué usar _loaded_at y no fecha_venta para este control?
Porque el control busca saber si la carga llegó a tiempo, no cuándo ocurrieron las ventas. _loaded_at registra el instante en que cada registro entró a raw.ventas; fecha_venta es la fecha del negocio en que se hizo la venta.
Las dos fechas no tienen por qué coincidir. Una venta antigua puede cargarse hoy, y un día sin ventas no significa que la carga se haya retrasado. En este ejercicio las ventas son de enero de 2026: si el control usara fecha_venta, fallaría siempre, incluso en el escenario actualizado con una carga de hace 2 horas. En cambio, con _loaded_at el resultado cambió según la carga: pasó con 2 horas y falló con 30 horas, aunque los datos de negocio eran idénticos.

2. ¿Por qué las pruebas de integridad no garantizan actualidad?
Porque las pruebas de unicidad, campos obligatorios y valores válidos revisan la forma de los datos que ya están en la tabla, no cuándo llegaron. Un conjunto de datos viejo pero bien formado las supera todas.
Eso se vio en el ejercicio: en el escenario desactualizado los datos eran los mismos, así que dbt build habría pasado sus pruebas y reconstruido el resumen con información de hace 30 horas. Además, dbt build no ejecuta freshness automáticamente. Por eso se agregó la tarea comprobar_freshness antes de construir_resumen: al fallar, el build quedó en upstream_failed y se conservó el resumen anterior.
Aun así, freshness tiene un límite: solo comprueba la carga más reciente. No demuestra que hayan llegado todas las ventas esperadas, para lo cual harían falta otros controles, como conteos esperados por día.