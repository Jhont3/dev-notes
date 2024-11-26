# Etapas de Pipelines en MongoDB

### Filtrado y Búsqueda

- __$match:__ Filtra documentos según un criterio

```terminal
db.collection.aggregate([{ $match: { age: { $gt: 25 } } }]);
```

###  Proyección de Campos

- __$project:__ Selecciona o calcula campos específicos para incluir o excluir en los resultados.

```terminal
db.collection.aggregate([
  { $project: { name: 1, age: 1, isAdult: { $gte: ["$age", 18] } } }
]);
```
Incluye los campos name y age, y calcula un nuevo campo isAdult

### Agrupación

- __$group:__ Agrupa documentos por un campo y aplica operaciones de agregación como sum, avg, max, min, etc.

```terminal
db.collection.aggregate([
  { $group: { _id: "$category", total: { $sum: "$price" } } }
]);
```
Agrupa documentos por category y suma el valor de price.

### Ordenación

- __$sort:__ Ordena los documentos por uno o más campos.

```terminal
db.collection.aggregate([
  { $sort: { age: -1 } } // Ordena por `age` en orden descendente
]);
```

### Limitación y Salto de Documentos

- __$limit:__ Limita el número de documentos en la salida.

```terminal
db.collection.aggregate([{ $limit: 5 }]);
```

- __$skip:__ Omite un número específico de documentos.

```terminal
db.collection.aggregate([{ $skip: 10 }]);
```

### Descomposición

- __$unwind:__ Divide documentos que tienen arrays en múltiples documentos, uno por cada elemento del array.

```terminal
db.collection.aggregate([{ $unwind: "$tags" }]);
```

Si un documento tiene un campo tags: ["A", "B"], produce dos documentos separados.

### Búsquedas en Documentos Asociados

- __$lookup:__ Realiza un "join" con otra colección.


```terminal
db.orders.aggregate([
  {
    $lookup: {
      from: "customers",        // Colección a unir
      localField: "customerId", // Campo en la colección actual
      foreignField: "_id",      // Campo en la colección asociada
      as: "customerDetails"     // Nombre del nuevo campo
    }
  }
]);
```

### Agregación en Series Temporales

- __$bucket:__ Agrupa documentos en rangos definidos manualmente.

```terminal
db.collection.aggregate([
  {
    $bucket: {
      groupBy: "$age",
      boundaries: [0, 18, 30, 50, 100],
      default: "Other"
    }
  }
]);
```

- __$bucketAuto:__ Similar a $bucket, pero define automáticamente los rangos.

```terminal
db.collection.aggregate([{ $bucketAuto: { groupBy: "$price", buckets: 3 } }]);
```

### Procesamiento de Muestras

- __$sample:__ Selecciona documentos aleatorios.

```terminal
db.collection.aggregate([{ $sample: { size: 5 } }]);
```

### Manipulación de Datos

- __$addFields:__ Agrega nuevos campos calculados a los documentos.

```terminal
db.collection.aggregate([
  { $addFields: { totalPrice: { $multiply: ["$price", "$quantity"] } } }
]);
```

- __$set:__ Similar a $addFields, pero también puede actualizar campos existentes.

### Fusión y Salida

- __$merge:__ Fusiona los resultados con otra colección.

```terminal
db.collection.aggregate([
  { $merge: { into: "otherCollection", on: "_id", whenMatched: "merge", whenNotMatched: "insert" } }
]);
```

- __$out:__ Escribe los resultados en una nueva colección.

```terminal
db.collection.aggregate([{ $out: "newCollection" }]);
```

### Ejemplo:

Supongamos que tienes una colección sales y quieres:

Filtrar ventas del año 2023.
Agrupar por product y sumar las cantidades vendidas.
Ordenar los resultados en orden descendente.

```terminal
db.sales.aggregate([
  { $match: { year: 2023 } },                     // Filtrar por año
  { $group: { _id: "$product", total: { $sum: "$quantity" } } }, // Agrupar y sumar
  { $sort: { total: -1 } }                        // Ordenar por total en descendente
]);
```

### Otros:

- __$geoNear__

Procesa documentos basados en proximidad geoespacial.
Restricción: Debe ser la primera etapa del pipeline.
Uso común: Consultar documentos cerca de un punto geográfico.

```terminal
db.places.aggregate([
  {
    $geoNear: {
      near: { type: "Point", coordinates: [ -73.97, 40.77 ] },
      distanceField: "distance",
      spherical: true
    }
  },
  { $limit: 5 } // Limitar a los 5 lugares más cercanos
]);
```