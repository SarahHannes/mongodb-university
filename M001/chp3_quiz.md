Practice Question:

People often confuse NEW YORK City as the capital of New York state, when in reality the capital of New York state is ALBANY.

In the sample_training.zips collection add a boolean field "capital?" to all documents pertaining to ALBANY NY, and NEW YORK, NY. The value of the field should be true for all ALBANY documents and false for all NEW YORK documents.

```
// Get counts
db.zips.find({"city": "ALBANY", "state": "NY"}).count()
// output: 
7

db.zips.find({"city": "NEW YORK", "state": "NY"}).count()
// output: 
40

// Updates
db.zips.updateMany({"city": "ALBANY", "state": "NY"}, {$set: {"capital?": true}})  

// output: 
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 7,
  modifiedCount: 7,
  upsertedCount: 0
}

db.zips.updateMany({"city": "NEW YORK", "state": "NY"}, {$set: {"capital?": false}})

// output: 
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 40,
  modifiedCount: 40,
  upsertedCount: 0
}

```