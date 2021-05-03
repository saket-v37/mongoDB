## SORT

- sort movies in desending order
  `db.movies.find({}).sort({year:-1})`

## COUNT

- count total no of document present in collection
  ` db.movies.find().count()`

---

# Comparison Operator:-

## $gt:

`db.movies.find({year:{$gt:2000}})`

## $lt:

`db.movies.find({year:{$lt:2000}})`

## gte: and $lte:

`db.movies.find({year:{$gte:1996,$lte:2019}})`

---

# Logical Operator:-

## $in:

`db.posts.find({age:{$in:[29,25]}})`

## $OR:

`db.movies.find({$or: [{score:{$lt:7}},{score:{$gt:8}}]})`

## $NOR:

`db.movies.find({$nor: [{score:{$lt:7}},{score:{$gt:8}}]})`

## $AND:

- By default if use(,) in mongo it will run query as AND operator
- Both querie below work same

`db.movies.find({{year:{$gt:2000}},{rating:"RT-2"}})`

`db.movies.find({$and:[{year:{$gt:2000}},{rating:"RT-2"}]})`

## $NOT:

- $not is not very useful,we can do same thing with $ne: (it maybe useful for some specfic condition)

  `db.movies.find({score:{$not:{$eq:7.9}}})`

## $NE:

` db.movies.find({year:{$ne:1985}})`

## $EQ:

`db.movies.find({rating:{$eq:'R'}})`

---

# Element Opertor : $exists:

- it checks if the any particular filds is present or not in the documents

` db.movies.find({score:{$exists:true,$ne:null}})`

- it will return only those documents which contains score field and score fild not contain NULL values

---

# Distinct

- find how many products are there in purches collection without repeating any product

`db.purchase_orders.distinct("product")`

# Aggregation:-

### Match & Group Pipeline

- Find how much has been spent on each product and sort it by price

```
db.purchase_orders.aggregate(
[
{$match: {} },
{$group: {_id: "$product", total: { $sum: "$total"} } },
{$sort: {total: -1}}
]
)
```

- Find how much money each customer has spent on toothbrushes and pizza

```
db.purchase_orders.aggregate(
[
{$match: {product: {$in: ["toothbrush", "pizza"]} } },
{$group: {_id: "$product", total: { $sum: "$total"} } },
]
)
```

### Project Pipline

`db.movies.aggregate([{$project:{_id:0,UpperCase:{$toUpper:"$title"},year:1,rating:1}}])`

---

# Creating index:-

`db.stores.createIndex( { name: "text", description: "text" } )`

### Text Searching

` db.stores.find({ $text: {$search: "Coffee" } })`

`db.stores.find({ $text: {$search: "Java Hut Coffee" } })`

```
db.stores.find(
   { $text: { $search: "java hut coffee" } },
   { score: { $meta: "textScore" } }
).sort( { score: { $meta: "textScore" } } )
```

## Drop All Indexes

=>db.COLLECTION_NAME.dropIndexes()

## Show All Indexes

=> db.COLLECTION_NAME.getIndexes()

---

# $regex: "Regular Expresion"

- Used for searhing and finding patterns

`db.movies.find({title:{$regex:/ma/i}})`

- _Note:_ Here, (i) is a "option" which help us to check both lower and upper case characters
