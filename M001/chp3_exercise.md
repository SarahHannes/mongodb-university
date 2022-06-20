### Inserting New Documents - insert() and errors:

1. Get a random document from a collection
>
>``` 
>// connect to Atlas (Copy connection string from >Atlas -> Connect -> Connect to MongoDB Shell)
> mongosh "mongodb+srv://sandbox.<XXX>.mongodb.net/admin" --apiVersion 1 --username <YOUR USERNAME> --password <YOUR PASSWORD>
>
> // choose db
> use sample_training
>
>// get random document from sample_training.grades
> db.grades.findOne()

2. Copy this random document, and insert it back to the collection. Do you get a "Duplicate Key" error?

>Yes. `MongoServerError: E11000 duplicate key error collection: sample_training.grades index: _id_ dup key: { _id: ObjectId('56d5f7eb604eb380b0d8d8ce') }`
> ```
> db.grades.insertOne({ _id: ObjectId("56d5f7eb604eb380b0d8d8ce"), student_id: 0, scores: [ { type: 'exam', score: 78.40446309504266 }, { type: 'quiz', score: 73.36224783231339 }, { type: 'homework', score: 46.980982486720535 }, { type: 'homework', score: 76.67556138656222 }], class_id: 339 })


3. Insert that document into the collection without the _id field to get a successfull insert. Did it work?

> Yes. `{
  acknowledged: true,
  insertedId: ObjectId("62b013c36e67fe1bcfac7016")
}`
> ```
>db.grades.insertOne({ student_id: 0, scores: [ { type: 'exam', score: 78.40446309504266 }, { type: 'quiz', score: 73.36224783231339 }, { type: 'homework', score: 46.980982486720535 }, { type: 'homework', score: 76.67556138656222 }], class_id: 339 })


### Inserting New Documents - insert() order:

1. Insert three test documents into the inspections collection
   `db.inspections.insert([{"test":1},{"test":2},{"test":3}])`
2. Insert the same three documents into the inspections collection. Did it work? Why?
> Yes. Because there is no _id specified.
3. Insert these three test documents into the inspections collection. Did it work? Why?
   `db.inspections.insert([{"_id": 1, "test": 1},{"_id": 1, "test": 2},{"_id": 3,"test": 3}])`
> Only 1 got inserted: `{ _id: 1, test: 1 }`. Insertion operation halts after duplicate key error is found on the second document: `{"_id": 1, "test": 2}`.
4. Try the same insert as above but make it unordered.
> Only 1 got inserted: `{"_id": 3,"test": 3}`. This is because, we have already inserted `{"_id": 1, "test": 1}` in the previous insert operation.
> ```
> db.inspections.insert([{"_id": 1, "test": 1},{"_id": 1, "test": 2},{"_id": 3,"test": 3}], {"ordered": false}) ```

5. Try this command. Did it work? Why?
   `db.inspection.insert([{ "_id":1, "test": 1 },{ "_id": 3,"test": 3 }])`
>Yes. Because we have no `inspection` collection (but, we do have an `inspections` collection). MongoDB created a new collection called `inspection` and inserted these documents in the collection.

Updating Documents  - mongo shell

1. Find all documents in the zips collection where the zip field is equal to 12434.

> ```
> db.zips.find({"zip": "12434"})
>// output:
> [
>  {
>    _id: ObjectId("5c8eccc1caa187d17ca731f6"),
>    city: 'GRAND GORGE',
>    zip: '12434',
>    loc: { y: 42.387358, x: 74.531173 },
>    pop: 13,
>    state: 'NY'
>  }
>] 

2. Find all documents in the zips collection where the city field is equal to "HUDSON".

> `db.zips.find({"city": "HUDSON"})`
3. Find how many documents in the zips collection have the city field is equal to "HUDSON".

> 16. `db.zips.find({"city": "HUDSON"}).count()`
4. Update all documents in the zips collection where the city field is equal to "HUDSON" by adding 10 to the current value of the "pop" field.
> ```
>db.zips.updateMany({"city": "HUDSON"}, {$inc: {"pop": 10}})
>// output:
>{
>  acknowledged: true,
>  insertedId: null,
>  matchedCount: 16,
>  modifiedCount: 16,
>  upsertedCount: 0
>} ```
5. Update a single document in the zips collection where the zip field is equal to 12534 by setting the value of the "pop" field to 17630.

> ```
> db.zips.updateOne({"zip": "12534"}, {$set: {"pop": 17630}})
> // output:
>{
>  acknowledged: true,
>  insertedId: null,
>  matchedCount: 1,
>  modifiedCount: 1,
>  upsertedCount: 0
>}

6. Update a single document in the zips collection where the zip field is equal to 12534 by setting the value of the "population" field to 17630.

> ```
>db.zips.updateOne({"zip": "12534"}, {$set: {"population": 17630}})
> // output:
>{
>  acknowledged: true,
>  insertedId: null,
>  matchedCount: 1,
>  modifiedCount: 1,
>  upsertedCount: 0
>}

7. Find all documents in the grades collection where the student_id is 151, and the class_id field is 339.

> ```
>db.grades.find({"student_id": 151, "class_id": 339})
> // output:
>[
>  {
>    _id: ObjectId("56d5f7eb604eb380b0d8deb4"),
>    student_id: 151,
>    scores: [
>      { type: 'exam', score: 39.44538383489339 },
>      { type: 'quiz', score: 64.12864683143684 },
>      { type: 'homework', score: 46.49129069302115 },
>      { type: 'homework', score: 1.504565288457116 }
>    ],
>    class_id: 339
>  }
>]

8. Find all documents in the grades collection where the student_id is 250, and the class_id field is 339.

> ```
> db.grades.find({"student_id": 250, "class_id": 339})
> // output:
> [
>  {
>    _id: ObjectId("56d5f7eb604eb380b0d8e292"),
>    student_id: 250,
>    scores: [
>      { type: 'exam', score: 3.6641013617826124 },
>      { type: 'quiz', score: 16.099760154050923 },
>      { type: 'homework', score: 18.069138737846245 },
>      { type: 'homework', score: 66.16407292421133 }
>    ],
>    class_id: 339
>  }
>]

9. Update one document in the grades collection where the student_id is 250, and the class_id field is 339, by adding a document element to the "scores" array.
> ```
> db.grades.updateOne({"student_id": 250, "class_id": 339}, {$push: {"scores": {"type": "extra credit", "score": 100}}})
> // output: 
> {
>  acknowledged: true,
>  insertedId: null,
>  matchedCount: 1,
>  modifiedCount: 1,
>  upsertedCount: 0
>}
> // check update
> db.grades.find({"student_id": 250, "class_id": 339})
> // output:
> [
>  {
>    _id: ObjectId("56d5f7eb604eb380b0d8e292"),
>    student_id: 250,
>    scores: [
>      { type: 'exam', score: 3.6641013617826124 },
>      { type: 'quiz', score: 16.099760154050923 },
>      { type: 'homework', score: 18.069138737846245 },
>      { type: 'homework', score: 66.16407292421133 },
>      { type: 'extra credit', score: 100 }
>    ],
>    class_id: 339
>  }
>]

### Deleting Documents and Collections

1. Look at all the documents in the inspections collection that have test field equal to 1.
> ```
> db.inspections.find({"test": 1})
> // output:
> [
>  { _id: ObjectId("62b014016e67fe1bcfac7017"), test: 1 },
>  { _id: ObjectId("62b014066e67fe1bcfac701a"), test: 1 },
>  { _id: 1, test: 1 }
>]
2. Look at all the documents in the inspections collection that have test field equal to 3.
> ```
> db.inspections.find({"test": 3})
> // output:
> [
>  { _id: ObjectId("62b014016e67fe1bcfac7019"), test: 3 },
>  { _id: ObjectId("62b014066e67fe1bcfac701c"),, test: 3 },
>  { _id: 3, test: 3 }
>]
3. Delete all the documents from the inspections collection that have test field equal to 1.
> ```
>db.inspections.deleteMany({"test": 1})
> // output:
>{ acknowledged: true, deletedCount: 3 }

4. Delete one document from the inspections collection that has test field equal to 3.

> ```
> db.inspections.deleteMany({"test": 3})
> // output:
>{ acknowledged: true, deletedCount: 3 }
5. Inspect what is left of the inspection collection.
> ```
>db.inspection.find()
> // output:
>[ { _id: 1, test: 1 }, { _id: 3, test: 3 } ]
6. View what collections are present in the sample_training database.

> ```
> show collections
> // output:
>companies
>grades
>inspection
>inspections
>posts
>routes
>trips
>zips

7. Drop the inspection collection.
> ```
> db.inspection.drop()
> // output:
> true
> show collections
> // output:
>companies
>grades
>inspections
>posts
>routes
>trips
>zips
