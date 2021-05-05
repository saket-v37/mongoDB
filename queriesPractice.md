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

- Find how much money each customer has spent on toothbrushes and pizza

```
db.purchase_orders.aggregate(
[
{$match: {product: {$in: ["toothbrush", "pizza"]} } },
{$group: {_id: "$product", total: { $sum: "$total"} } },
]
)
```

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

### Project Pipline

`db.movies.aggregate([{$project:{_id:0,UpperCase:{$toUpper:"$title"},year:1,rating:1}}])`

- Using Projection With Arrays
  `db.Friends.aggregate([{$project:{_id:0,examScore:{$slice:["$examScores",1]}}}])`

`db.Friends.aggregate([{$project:{_id:0,examScore:{$slice:["$examScores",-2]}}}])`

`db.Friends.aggregate([{$project:{_id:0,examScore:{$slice:["$examScores",2,1]}}}])`

- Getting length of an array {$size:}
`db.Friends.aggregate([{$project:{\_id:0,numScore:{$size:"$examScores"}} }])`

- Using the $filter operator
`db.Friends.aggregate([{$project:{\_id:0,scores:{$filter:{input:"$examScores",as:"sc",cond:{$gt:["$$sc.score",60]}}}}}])`

---

### Unwind pipline

- grouping by hobbies array(Pusing elements into newly created array)
  `db.Friends.aggregate([{$group:{_id:{age:"$age"},allHobies:{$push:"$hobbies"}}}])`

- Unwind takes one documnet and splits out multiple documents
  `db.Friends.aggregate([{$unwind:"$hobbies"},{$group:{_id:{age:"$age"},allHobies:{$push:"$hobbies"}}}])`

- Eleminating Duplicate values {$addToSet:}
  `db.Friends.aggregate([{$unwind:"$hobbies"},{$group:{\_id:{age:"$age"},allHobies:{$addToSet:"$hobbies"}}}]) `

---

## Multiple Piplines

```
db.Friends.aggregate([
  {$unwind:"$examScores"},
  {$project:{name:1,score:"$examScores.score"}},
  {$sort:{score:-1}},
  {$group:{_id:"$_id",name:{$first:"$name"},maxScore:{$max:"$score"}}},
  {$sort:{maxScore:-1}}
])
])
```

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

- _Note:_ Here, (i) is a "option" which also help us to check both lower and upper case characters

---

## Bulk Actions

- Gives ability to perform write operations in bulk.

```
db.students.bulkWrite(
      [
         { insertOne :
            {
               "document" :
               {
                  name: "Andrew", major: "Architecture", gpa: 3.2
               }
            }
         },
         { insertOne :
            {
               "document" :
               {
                  name: "Terry", major: "Math", gpa: 3.8
               }
            }
         },
         { updateOne :
            {
               filter : { name : "Terry" },
               update : { $set : { gpa : 4.0 } }
            }
         },
         { deleteOne :
            { filter : { name : "Kate"} }
         },
         { replaceOne :
            {
               filter : { name : "Claire" },
               replacement : { name: "Genny", major: "Counsling", gpa: 2.4 }
            }
         }
    ],
    {ordered: false}
   );
```
