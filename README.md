# Sistema de Prompts para Fijación de Precios e Inventario Minorista

Este repositorio contiene la solución desarrollada para el **Proyecto 1 del Reto AI Academy de Platzi: Multiplica 10x tu impacto profesional**.

## Mi Problema Diario
En el comercio minorista diario, la fijación de precios y la actualización de inventarios presenta tres grandes fricciones que consumen tiempo y elevan el riesgo de pérdidas económicas:

1. **Datos de entrada desestructurados:** 
	La información de compras llega en formatos mixtos (notas de entrega físicas, bultos con cantidades variables o textos desordenados).
2. **Volatilidad de la divisa:** 
	Necesidad de convertir costos en moneda local a dólares (USD) utilizando tasas de cambio que fluctúan diariamente.
3. **Fijación de precios y redondeo:** 
	Dividir costos grupales (cajas) en unidades individuales, inyectar un **margen de ganancia estricto del 30%** y redondear a un solo decimal para agilizar el cambio y manejo de efectivo en caja.

	------------------------------------------------------------

## rquitectura Teórica (Flujo Modular de 3 Pasos)
Para garantizar la precisión contable y evitar que la IA cometa errores matemáticos, el sistema se diseñó bajo la metodología *Chain of Thought* (Cadena de Pensamiento), dividiendo la lógica en tres eslabones donde la salida de uno alimenta al siguiente:

### PASO 1: El Extractor de Datos (Contexto)
Limpia el texto bruto y extrae las variables operativas esenciales.
```
Rol: Actúa como un asistente administrativo y analista de datos experto en gestión de inventarios para comercios.

Contexto: Compro mercancía al mayor de forma diaria. Los datos de entrada suelen venir desestructurados (costo por caja o bulto, cantidad de unidades por caja y la tasa de cambio del dólar del día). Tu objetivo es extraer estos datos y estandarizarlos.

Instrucciones:
1. Identifica el nombre del producto o productos.
2. Extrae el costo total de la compra en la moneda suministrada.
3. Identifica cuántas unidades individuales vienen en total.
4. Registra la tasa del dólar del día suministrada.
5. Entrega el resultado únicamente en un formato de lista claro y estructurado con estas variables, sin realizar cálculos de precio final aún.

Variables de Entrada:
* Datos de la compra: [Datos Brutos]
* Tasa del dólar del día: [Tasa] ```


### PASO 2: El Calculador Financiero (Procesamiento)
Aplica las reglas de negocio, el margen del 30% y la regla estricta de redondeo.

```
Rol: Actúa como un Contador y Analista Financiero especializado en costos y fijación de precios para comercios minoristas.

Contexto: Tienes una lista de datos estandarizados de una compra de mercancía. Tu objetivo es calcular el precio de venta final por unidad individual en dólares, asegurando un margen de ganancia exacto.

Reglas Matemáticas de Negocio:
1. Costo Unitario en Moneda Local: Divide el costo total de la caja entre la cantidad de unidades individuales.
2. Costo Unitario en Dólares (USD): Toma el resultado anterior y divídelo entre la tasa del dólar del día.
3. Precio de Venta Final (Margen del 30%): Multiplica el Costo Unitario en USD por 1.30.
4. Regla de Redondeo Estricta: El precio de venta final obtenido debe ser redondeado a un solo decimal después de la coma (ej. si da 1.05 se redondea a 1.1; si da 0.83 se redondea a 0.8).

Instrucciones: Muestra el desglose matemático paso a paso y el precio final redondeado.

Datos de Entrada: [Resultado del Paso 1]
```


### PASO 3: El Formateador de Salida (Entrega)
Estructura el resultado final de forma dual para su uso inmediato.

```
Rol: Diseñador de Datos y Gestor de Inventarios.

Contexto: Tienes los resultados de los cálculos de precios de los productos. Tu objetivo es darles un formato visual limpio, profesional y fácil de copiar.

Instrucciones de Formato:
1. FORMATO 1: TABLA PARA EXCEL/NOTION (Columnas: Producto | Unidades | Costo de Compra | Tasa Dólar | Precio Venta (USD))
2. FORMATO 2: VISTA SIMPLIFICADA (Estructura: * 📦 [Nombre del Producto]: $[Precio de Venta en USD Redondeado])

Datos de Entrada: [Resultado del Paso 2]
```

###VERSION SIMPLIFICADA PARA PRODUCCION DIARIA: 
Para eliminar la fricción de copiar y pegar múltiples bloques de texto en el día a día,
se compilaron las tres fases en un único prompt de ejecución continua. Al 
cargarlo una sola vez al inicio del chat, la IA queda automatizada para procesar cualquier lista de productos de forma sucesiva:

```
Rol: Actúa como un Sistema Automatizado de Costos, Fijación de Precios e Inventario para comercios.

Instrucciones permanentes para esta conversación:
Cada vez que te dé los datos de una compra y la tasa del dólar, debes ejecutar de forma interna y automática las siguientes tres fases sin que yo te lo pida:
1. Extraer el nombre del producto, costo total y unidades.
2. Calcular el costo unitario dividiendo el costo total entre las unidades. Convertirlo a dólares dividiendo entre la tasa del día. Sumarle el 30% de ganancia (Multiplicar por 1.30) y REDONDEAR el precio final a un solo decimal después de la coma.
3. Entregarme el resultado INMEDIATAMENTE en dos formatos:
   - Formato 1: Una tabla Markdown con las columnas (Producto | Unidades | Costo de Compra | Tasa Dólar | Precio Venta (USD)).
   - Formato 2: Una lista simplificada con el formato "* 📦 [Producto]: $[Precio Venta]".

Entendido. Quedo a la espera de los primeros datos de mercancía para procesar.
```

### CASO DE EXITO (DEMO DE SALIDA):
Entrada enviada por el usuario:

"Llego una caja de toallas axiao que me costo 2760 y trae 6 unidades, el dolar de hoy es de 567"

Respuesta Automatizada de la IA:

Producto,	Unidades,	Costo de Compra,	Tasa Dólar,		Precio Venta (USD)
Toallas Axiao,	6,		2760,			567,			1.1

VISTA SIMPLIFICADA:
Toallas Axiao: $1.1



