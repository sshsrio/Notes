# LearnMongo

[Visualize Mongodb](https://www.knowi.com/mongodb-analytics)

# Resources

- [Mongoose Documentation](https://mongoosejs.com/)
- [LWS-Mongo Tutorial](https://www.youtube.com/watch?v=BtCGiEHzdfk)
- [Tutorialspoint: Koa-js](https://www.tutorialspoint.com/koajs/koajs_response_object.htm)

From Santo Bhai [Practicing Site]:

- https://nosqlbooster.com/
- https://studio3t.com/

# Important Concepts

## Mongoose Model Instance Method

```js
const todoSchema = new mongoose.Schema({title: String...});
const Todo = new mongoose.model('Todo',todoSchema);

const todo = new Todo({title: 'Learning Instance Method'...});

todo.save();
```

**Explanation**

- Create a schema from `mongoose.Schema` class
- Create a model (which is an another class) from `mongoose.model` class & pass schema to it
- Create a document using model class
- Call necessary model instance method using `document`

## Static Method

```js
todoSchema.statics = {};

// we can access the property directly via Todo model
```

Note: Arrow function will not work in `statics` and `query helper`. The reason is arrow function can't bind `this`

# Aggregation

- [Aggregation Tutorial](https://www.youtube.com/watch?v=uNwktK0qNXM&list=PLWkguCWKqN9OwcbdYm4nUIXnA2IoXX0LI&index=1)

**Format:**

`{ $ <stageOperator>:{}}`

**Example:**

```js
{
  $match: {
    age: {
      $gt: 20;
    }
  }
}
{
  $group: {
    _id: "$age";
    // aggregation exp should be in double quotation otherwise it will be treated as a variable
  }
}

{$group: { _id: "$company.location.country"}}

{$group: {_id: "$name", total: {$sum: "$price"}}}

```

`$sum` is called cumulative operator

- `Match` specific documents using query
  `{ $match: { <query> }}`

  ```js
  db.test.aggregate([{ $match: { age: { $gt: 25 } } }]);
  ```

## Some Working Examples

Find Mock Data [here](https://github.com/bstashchuk/MongoDB-Aggregation-Tutorial/blob/master/Persons.json)

```js
db.test.aggregate([]);
db.test.find({});

// above two return all of the data
// meaning that find and aggregate will work alike

// find

// Example 1: match
db.test.aggregate([{ $match: { age: { $gt: 25, $lte: 40 } } }]);

db.test.aggregate([
  // stage 1
  { $match: { tags: { $size: 3 } } },
]);
// db.getCollection('test').find({tags: {$size: 3}})

// Example 2: group
db.test.aggregate([{ $group: { _id: "$eyeColor" } }]);

// Example 3: Group by nested fields
db.test.aggregate([{ $group: { _id: "$company.location.country" } }]);
```

```js
// Example 4: Group by multiple fields
db.test.aggregate([{ $group: { _id: { age: "$age", gender: "$gender" } } }]);

db.test.aggregate([
  // stage 1
  {
    $group: {
      _id: {
        eyeColor: "$eyeColor",
        fruit: "$favoriteFruit",
        age: "$age",
      },
    },
  },
]);
```

```js
// Example 5: $match and $group
db.test.aggregate([
  // stage 1
  { $match: { favoriteFruit: "banana" } },
  // stage 2
  {
    $group: { _id: { age: "$age", eyeColor: "$eyeColor", gender: "$gender" } },
  },
]);
```

```js
// Example 6: $match and $group (changing Order)
db.test.aggregate([
  // stage 1
  {
    $group: { _id: { age: "$age", eyeColor: "$eyeColor", gender: "$gender" } },
  },
  // stage 2
  { $match: { favoriteFruit: "banana" } },
]);

// output Empty!!
```

```js
// Example 7: $match and $group (changing Order & Correct Syntax)
db.test.aggregate([
  // stage 1
  {
    $group: {
      _id: {
        age: "$age",
        eyeColor: "$eyeColor",
        gender: "$gender",
        favoriteFruit: "$favoriteFruit",
      },
    },
  },
  // stage 2
  {
    $match: {
      "_id.favoriteFruit": "banana",
      "_id.gender": "female",
      "_id.eyeColor": "brown",
    },
  },
]);
```

```js
// Example 8: Effective way of example 7
db.test.aggregate([
  // stage 1
  { $match: { favoriteFruit: "banana", gender: "female", eyeColor: "brown" } },
  // stage 2
  {
    $group: {
      _id: {
        age: "$age",
        eyeColor: "$eyeColor",
        gender: "$gender",
        favoriteFruit: "$favoriteFruit",
      },
    },
  },
]);

// Example 7 & 8 both will give same output
```

```js
// Example 9: $count Stage (1st client side)
db.test.aggregate([{ $count: "allDocumentsCount" }]); // 0.053s
// 2nd (same as 1)
db.test.aggregate([]).toArray().length; // 0.094s
// 3rd (server side)
db.test.aggregate([{ $count: "total" }]); // 0.036s
```

**Note:** Find `count()` is the wrapper of Aggregate `$count`
While performing count always use count() stage it is the most effective way.

```js
// Example 10: $group and $count

db.test.aggregate([
  //stage 1
  { $match: { age: { $gte: 25 } } },
  //stage 2
  { $group: { _id: { eyeColor: "$eyeColor", age: "$age" } } },
  // stage 3
  { $count: "eyeColorAndAge" },
]);
```

`$sort` stage is used to sort the documents. Usually, this stage is placed after the `$match` and `$group` stage.

Syntax: `{ $sort: {<field>: <-1|1>, <field2>: <-1|1> ...}}`

```js
// Example 11: $sort
db.test.aggregate([{ $sort: { name: 1 } }]); // sort by name is ascending order

db.test.aggregate([
  // sorting via multiple columns
  { $sort: { age: -1, gender: -1, eyecolor: 1 } },
]);
```

```js
// Example 12: $group and $sort

db.test.aggregate([
  {
    $group: { _id: { eyeColor: "$eyeColor", favoriteFruit: "$favoriteFruit" } },
  },
  { $sort: { "_id.eyeColor": 1, "_id.favoriteFruit": -1 } },
]);
```
