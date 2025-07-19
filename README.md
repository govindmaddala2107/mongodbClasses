# Mongodb:

## DB Connection using **mongodb & node**:
    ```
    const { MongoClient } = require('mongodb');
    const url = "mongodb://127.0.0.1:27017"

    const client = new MongoClient(url);

    exports.databaseConnect = async (dbName,collectionName) => {
        await client.connect();
        console.log("Database is connected");
        const db = client.db(dbName);
        const collection = db.collection(collectionName);
        return {
            collection, client
        };
    }
    ```
## Find Operations:
### Logical Operators:
#### $or:
- data: 
  ```
  [
    { name: "govind", sname: "maddala" },
    { name: "jaya shakar", sname: "maddala" },
    { name: "akhil", sname: "nagumalle" },
    { name: "ganesh", sname: "nagumalle" },
    { name: "akhil", sname: "koduri" },
  ]
  ```
- Get name = "govind" or sname = "maddala";
  - Query:
    ```
    db.collection.find({
        $or: [
            { name: "govind" },
            { sname: "maddala" }
        ]
    })
    ```
  - Output:
    ```
      [  
        {
            _id: new ObjectId("641c6997e9336df300596ad5"),
            name: 'govind',
            sname: 'maddala'
        },
        {
            _id: new ObjectId("641c6997e9336df300596ad6"),
            name: 'jaya shakar',
            sname: 'maddala'
        }
    ]
    ```

#### $and:
- data: 
  ```
  [
    { name: "govind", sname: "maddala" },
    { name: "jaya shakar", sname: "maddala" },
    { name: "akhil", sname: "nagumalle" },
    { name: "ganesh", sname: "nagumalle" },
    { name: "akhil", sname: "koduri" },
  ]
  ```
- Get name = "govind" or sname = "maddala";
  - Query-1:
    ```
    db.collection.find({
        $and: [
            { name: "govind" },
            { sname: "maddala" }
        ]
    })
    ```
  - Output:
    ```
      [  
        {
            _id: new ObjectId("641c6997e9336df300596ad5"),
            name: 'govind',
            sname: 'maddala'
        }
    ]
    ```
  - Query-2:
    - Query-1 is as same as ```db.collection.find({ name: "govind", sname: "maddala" })```
  - Query-3: Actual usuage of $and is another way. Now get the people who aged between 17 and 30.
    ```
    [
        { name: "govind", age: 27 },
        { name: "ram", age: 47 },
        { name: "syam", age: 37 },
        { name: "ganesh", age: 17 },
        { name: "akhil", age: 57 },
    ] 
    ``` 
    ```
    db.collection.find({
        $and: [
            { age: { $gte: 17 } },
            { age: { $lte: 30 } }
        ]
    })
    ```
    ```
    [
        {
            name: "ganesh", age: 17
        },
        {
            name: "govind", age: 27 
        }
    ]
    ```

#### $nor:
- data: 
  ```
  [
    { name: "govind", sname: "maddala" },
    { name: "jaya shakar", sname: "maddala" },
    { name: "akhil", sname: "nagumalle" },
    { name: "ganesh", sname: "nagumalle" },
    { name: "akhil", sname: "koduri" },
  ]
  ```
- Get data where name != "govind" or sname != "maddala";
  - Query:
    ```
    db.collection.find({
        $nor: [
            { name: "govind" },
            { sname: "maddala" }
        ]
    })
    ```
  - Output:
    ```
    [  
        { name: "akhil", sname: "nagumalle" },
        { name: "ganesh", sname: "nagumalle" },
        { name: "akhil", sname: "koduri" },
    ]
    ```
#### $not + $eq (or) $ne:
- data: 
  ```
  [
    { name: "govind", sname: "maddala" },
    { name: "jaya shakar", sname: "maddala" },
    { name: "akhil", sname: "nagumalle" },
    { name: "ganesh", sname: "nagumalle" },
    { name: "akhil", sname: "koduri" },
  ]
  ```
- Get data where name != "govind"
  - Query-1:
    ```
    db.collection.find({ 
        name: {
            $not: { $ne: "govind" }
        }
    })
    ```
  - Query-2:
    ```
    db.collection.find({
        name: {
            $ne: "govind"
        }
    })
    ```
  - Output:
    ```
    [  
        { name: "akhil", sname: "nagumalle" },
        { name: "ganesh", sname: "nagumalle" },
        { name: "akhil", sname: "koduri" },
    ]
    ```

### Comparison Operators:
#### $gt, $gte, $lt, $lte, $eq, $ne
- data:
  ```
  [
    {
        item: [10, 20, 30, 40],
    },
    {
        item: [40, 50, 60, 70]
    }
    , {
        item: [10, 50, 70, 80]
    }
  ]
  ```
-  query: { item: { $gt: 10 } } return item if any one has > 10 and so on...


### Evaluation Operators: 
| Name          | Description                                                                                            |
| ------------- | ------------------------------------------------------------------------------------------------------ |
| `$expr`       | Allows use of aggregation expressions within the query language.                                       |
| `$jsonSchema` | Validate documents against the given JSON Schema.                                                      |
| `$mod`        | Performs a modulo (%) operation on the value of a field and selects documents with a specified result. |
| `$regex`      | Selects documents where values match a specified regular expression.                                   |
| `$text`       | Performs text search.                                                                                  |
| `$where`      | Matches documents th                                                                                   |

#### $regex:
- data:
  ```
  [  
    { name: "akhil", sname: "nagumalle" },
    { name: "ganesh", sname: "nagumaLLe" },
    { name: "akhil", sname: "koduri" },
  ]
  ```
- Query: To get data where sname is having malle [case insensitive [add i at the end of regex]]
  - db.collection.find({
    sname: {
        $regex: /malle/i
    }
  })
- Output:
  ```
  [
    { name: "akhil", sname: "nagumalle" },
    { name: "ganesh", sname: "nagumaLLe" },
  ]
  ```

#### $expr:
- data:
  ```
  [
    { name: "govind", salary: 29000, expenditure: 30000 },
    { name: "Ganesh", salary: 99000, expenditure: 69000 },
    { name: "akhil", salary: 69000, expenditure: 29000 },
    { name: "Aravind", salary: 59000, expenditure: 45000 },
  ]
  ```
- Query: Get data where salary < expenditure
  ```
  db.collection.find({
    $expr: {
        $lt: [ "$salary", "$expenditure" ]
    }
  })
  ```
- Output:
  ```
  [
    { name: "govind", salary: 29000, expenditure: 30000 },
  ]
  ```
- Query-2: If salary is < 30,000, give hike 100 % and for others give 25% and get the data where hike is equivalent to current salary.
  ```
  const hikes = {
        if: { '$lt': [ '$salary', 30000 ] },
        then: { '$multiply': [ '$salary', 1 ] },
        else: { '$multiply': [ '$salary', 0.25 ] }
  }

  db.collection.find({
    $expr: {
        $eq: ["$salary", hikes]
    }
  })
  ```
- Output:
  ```
  [
    { name: "govind", salary: 29000, expenditure: 30000 },
  ]
  ```
- dsjgdfsj

#### $mod:
- data:
  [
    { "_id": 1, "item": "a", "qty": 0 },
    { "_id": 2, "item": "b", "qty": 5 },
    { "_id": 3, "item": "c", "qty": 12 },
    { "_id": 4, "item": "d", "qty": 20 },
  ]
- Query: get all the data if qty is divisible by 4.
  ```
  db.collection.find({
    qty: {
        $mod: [4, 0]
    }
  })
  ```
- Output:
  ```
  [
    { _id: 1, item: 'a', qty: 0 }, 
    { _id: 3, item: 'c', qty: 12 },
    { _id: 4, item: 'd', qty: 20 } 
  ]
  ```

#### $text + $search:
- data:
  ```
  [
    { _id: 1, subject: "coffee xyz", author: "xyz", views: 50 },
    { _id: 2, subject: "Coffee Shopping", author: "efg", views: 5 },
    { _id: 3, subject: "Baking a cake", author: "abc", views: 90 },
    { _id: 4, subject: "baking", author: "xyz", views: 100 },
    { _id: 5, subject: "Café Con Leche", author: "abc", views: 200 },
    { _id: 6, subject: "Сырники", author: "jkl", views: 80 },
    { _id: 7, subject: "coffee and cream", author: "efg", views: 10 },
    { _id: 8, subject: "Cafe con Leche", author: "xyz", views: 10 }
  ]
  ```
- Query: Search through the collection and find if subject or author have word "xyz":
  - Before that, one pre-requisite is to create index:
  ```
  db.collection.createIndex({
    subject: "text",
    author: "text"
  });
  ```
  ```
  db.collection.find({
    $text: {
        $search: "xyz"  [if you want to search for exact word like for Cafe, then give like "\"Cafe\""]
    }
  })
  ```
- Output:
  ```
  [
    { _id: 1, subject: "coffee xyz", author: "xyz", views: 50 },
    { _id: 4, subject: "baking", author: "xyz", views: 100 },
    { _id: 8, subject: "Cafe con Leche", author: "xyz", views: 10 }
  ]
  ```

#### $where:
- $where operator allows you to run custom JavaScript expressions to filter documents. While powerful, it's generally discouraged for performance and security reasons unless absolutely necessary.
- data:
  [
    { "_id": 1, "subject": "coffee", "views": 50 }
    { "_id": 2, "subject": "baking", "views": 100 } 
  ]
- Query-1: Get data where subject is banking and views is > 50
  ```
  db.collection.find({
    $where: "this.subject === 'banking' && this.views > 50"
  })
  ```
- Query-2:
  ```
  db.collection.find({
    $where: function(){
        return this.subject === "banking" && this.views > 50
    }
  })
  ```
- Output: 
  ```
  [
    { "_id": 2, "subject": "baking", "views": 100 } 
  ]
  ```
- How to get the same output in safe way:
  ```
  db.collection.find({
    subject: "banking",
    views: {
        $gt: 50
    }
  })
  ```

### Embedded documents:
- data:
  ```
  [
    {
        name: "govind",
        companies: [
            { name:"JSW", Location: "Mumbai"},
            { name:"Enmovil", Location: "Hyderabad"},
            { name:"ProvenTech", Location: "Hyderabad"},
            { name:"Iorta", Location: "Hyderabad"},
        ]
    }
  ]
  ```
- Query: Get data where company name is JSW:
  ```
  db.collection.find({
    "companies.name": "JSW"
  })
  ```
- Output:
  ```
  [
    {
        name: "govind",
        companies: [
            { name:"JSW", Location: "Mumbai"},
            { name:"Enmovil", Location: "Hyderabad"},
            { name:"ProvenTech", Location: "Hyderabad"},
            { name:"Iorta", Location: "Hyderabad"},
        ]
    }
  ]
  ```

### Misc:
#### $exists:
- data:
  ```
  [
    { name: "govind", age: 27 },
    { fullname: "manaswini vedula", age: "26" }
  ]
  ```
- Query-1: Get data where name field exists
  ```
  db.collection.find({
    name: {
        $exists: true
    }
  })
  ```
- Output-1:
  ```
  [
    { name: "govind", age: 27 }
  ]
  ```
- Query-2: Get data where name field not exists
  ```
  db.collection.find({
    name: {
        $exists: false
    }
  })
  ```
- Output-1:
  ```
  [
    { fullname: "manaswini vedula", age: "26" }
  ]
  ```

### Operations on Arrays:
- data:
  ```
  [
    {
        name: "Govind Maddala",
        jobs: [
            { comp1: "JSW", salary: 45000 },
            { comp1: "Enmovil", salary: 28600 },
        ],
        hobbies: [
            "cooking", "reading", "coding"
        ]
    },
    {
        name: "Akhil Koduri",
        jobs: [
            { comp1: "Vedanta", salary: 85000 },
            { comp1: "Deloitee", salary: 50000 },
        ],
        hobbies: [
            "chatting", "reading", "coding"
        ]
    },
    {
        name: "Akhil Nagumalle",
        jobs: [
            { comp1: "JSW", salary: 60000 }
        ],
        hobbies: [
            ["sleeping", "eating"], "reading", ["talking"]
        ]
    }
  ]
  ```
#### $size:
- Query: get date where a person worked in one company only i.e array size of jobs should be equals to 1.
  ```
  db.collection.find({
    jobs: {
        $size: 1
    }
  })
  ```
- Output:
  ```
  [
    {
        name: "Akhil Nagumalle",
        jobs: [
            { comp1: "JSW", salary: 60000 }
        ],
        hobbies: [
            ["sleeping", "coding"], "reading", ["talking"]
        ]
    }
  ]
  ```

#### $all:
- Query-1: get data where hobbies are "reading" and "coding".
  ```
  db.collection.find({
    hobbies: {
        $all: ["reading", "coding"]
    }
  })
  ```

  Another way using **$and**: 
  ```
  db.collection.find({
    $and: [
        { hobbies: "reading" },
        { hobbies: "coding" },
    ]
  })
  ```
- Output:
  ```
  [
    {
        name: "Govind Maddala",
        jobs: [
            { comp1: "JSW", salary: 45000 },
            { comp1: "Enmovil", salary: 28600 },
        ],
        hobbies: [
            "cooking", "reading", "coding" // "reading", "coding" are there
        ]
    },
    {
        name: "Akhil Koduri",
        jobs: [
            { comp1: "Vedanta", salary: 85000 },
            { comp1: "Deloitee", salary: 50000 },
        ],
        hobbies: [
            "chatting", "reading", "coding"   // "reading", "coding" there
        ]
    }
  ]
  ```
  - PS: for Akhil Nagumalle: "coding", "reading",are there but coding is in nested array. To get the exact match, we have to pass [] in [] again like below:
- Query: get data where hobies are exactly sleeping and coding.
  ```
  db.collection.find({
    hobbies: {
        $all: [["sleeping", "coding"]]
    }
  })
  ```
- Output:
  ```
  [
    {
        name: "Akhil Nagumalle",
        jobs: [
            { comp1: "JSW", salary: 60000 }
        ],
        hobbies: [
            ["sleeping", "coding"], "reading", ["talking"]
        ]
    }
  ]
  ```

#### elemMatch:
- data-1:
  ```
  [
        { results: [82, 88, 89] },
        { results: [75, 90, 87] },
  ]
  ```
- Query-1: get data where results are >= 80 and =< 85:
  - Using $and query, we get wrong data
  ```
  db.collection.find({
    $and: [
        { results: { $gte: 80 } },
        { results: { $lte: 85 } },
    ]
  })
  ```
  - Output-1:
  ```
  [
    { results: [82, 88, 89] },
    { results: [75, 90, 87] },
  ]
  ```
  - Using $elemMatch, we can get exact data:
  ```
  db.collection.find({
    results: {
        $elemMatch: {
            $gte: 80,
            $lte: 85
        }
    }
  })
  ```
  - Output:
  ```
  [
    { results: [82, 88, 89] }
  ]
  ```
- data-2:
  ```
  [
        {
            _id: 1,
            results: [
                { subject: "Maths", score: 100 },
                { subject: "Science", score: 95 },
            ]
        },
        {
            _id: 2,
            results: [
                { subject: "Maths", score: 95 },
                { subject: "Science", score: 100 },
            ]
        },
        {
            _id: 3,
            results: [
                { subject: "Maths", score: 75 },
                { subject: "Science", score: 85 },
            ]
        },

  ]
  ```
- Query: get data where subject is "Maths" and score is 100;
  ```
  db.collection.find({
    "results.subject": "Maths",
    "results.score": 100,
  })
  ```
  - Output: Because here subject: Maths && score == 100 are there but in different objects.
  ```
  [
    {
        _id: 1,
        results: [
            { subject: "Maths", score: 100 },
            { subject: "Science", score: 95 },
        ]
        },
        {
            _id: 2,
            results: [
                { subject: "Maths", score: 95 },
                { subject: "Science", score: 100 },
            ]
        },
  ]
  ```
  - Using $elemMatch, we can get exact data:
  ```
  db.collection.find({
    results: {
        $elemMatch: {
            subject: "Maths",
            score: 100,
        }
    }
  })
  ```
  - Output:
  ```
  [
    {
        _id: 1,
        results: [
            { subject: "Maths", score: 100 }, // exact match
            { subject: "Science", score: 95 },
        ]
    }
  ]
  ```


## Aggregations:
### Pipeline Stages:
#### $addFields:
- Notes:
  - Adds new fields to documents. $addFields outputs documents that contain all existing fields from the input documents and newly added fields.
  - The $addFields stage is equivalent to a $project stage that explicitly specifies all existing fields in the input documents and adds the new fields.
  - You can include one or more $addFields stages in an aggregation operation.
  - To add an element to an existing array field with $addFields, use with $concatArrays.
- data:
  ```
  [
    {
        _id: 1,
        student: "Maya",
        homework: [10, 5, 10],
        quiz: [10, 8],
        extraCredit: 0,
        address: {
          dNo: 4-45,
          place: "Hyderabad",
          state: "Telanagana"
        },
        hobbies: ["Gym"],
        passion: ["acting"]
    },
    {
        _id: 2,
        student: "Ryan",
        homework: [5, 6, 5],
        quiz: [8, 8],
        extraCredit: 8,
        address: {
          dNo: 3-45,
          place: "Hyderabad",
          state: "Telanagana"
        },
        hobbies: ["cycling"],
        passion: ["blogging"]
    }
  ]
  ```
- Query: Add all the homework marks and quiz marks separately and again add these both and extraCredit.
  ```
  const pipeline = [
    {
      $match: {
        _id: 2
      }
    },
    {
      $addFields: {
        homeworkmarks: { $sum: "$homework" },
        quizmarks: { $sum: "$quiz" },
        // to add extra fields in object
        "address.landmark": "Wipro Circle",
        "address.pinCode": "500032",
        // to add into array
        hobbies: {
          $concatArrays: ["$hobbies", ["painting"], "$passion" ]
        }
      }
    },
    {
      $addFields: {
        totalMarks: {
          $sum: [ "$homeworkmarks", "$quizmarks", "$extraCredit" ]
        }
      }
    }
  ]
  ```
- Output:
  ```
  [
    {
        _id: 2,
        student: "Ryan",
        homework: [5, 6, 5],
        quiz: [8, 8],
        extraCredit: 8,
        homeworkmarks: 16,
        quizmarks: 16,
        totalMarks: 40,
        address: {
          dNo: -42,
          place: 'Hyderabad',
          state: 'Telanagana',
          landmark: 'Wipro Circle',
          pinCode: '500032'
        },
        hobbies: [ 'cycling', 'painting', 'blogging' ],
        passion: [ 'blogging' ],
    }
  ]
  ```
#### $bucket:
- Notes:
  - Categorizes incoming documents into groups, called buckets, based on a specified expression and bucket boundaries and outputs a document per each bucket. Each output document contains an _id field whose value specifies the inclusive lower bound of the bucket. The output option specifies the fields included in each output document.
  - $bucket  only produces output documents for buckets that contain at least one input document.
- Data:
  ```
  [
    { "_id": 1, "last_name": "Bernard", "first_name": "Emil", "year_born": 1868, "year_died": 1941, "nationality": "France" },
    { "_id": 2, "last_name": "Rippl-Ronai", "first_name": "Joszef", "year_born": 1861, "year_died": 1927, "nationality": "Hungary" },
    { "_id": 3, "last_name": "Ostroumova", "first_name": "Anna", "year_born": 1871, "year_died": 1955, "nationality": "Russia" },
    { "_id": 4, "last_name": "Van Gogh", "first_name": "Vincent", "year_born": 1853, "year_died": 1890, "nationality": "Holland" },
    { "_id": 5, "last_name": "Maurer", "first_name": "Alfred", "year_born": 1868, "year_died": 1932, "nationality": "USA" },
    { "_id": 6, "last_name": "Munch", "first_name": "Edvard", "year_born": 1863, "year_died": 1944, "nationality": "Norway" },
    { "_id": 7, "last_name": "Redon", "first_name": "Odilon", "year_born": 1840, "year_died": 1916, "nationality": "France" },
    { "_id": 8, "last_name": "Diriks", "first_name": "Edvard", "year_born": 1855, "year_died": 1930, "nationality": "Norway" }
  ]
  ```
- Query:
  ```
  const pipeline = [
    {
      $bucket: {
        groupBy: "$year_born",                           // Field to group by
        boundaries: [1840, 1850, 1860, 1870, 1880],      // Boundaries for the buckets
        default: "Others",                               // Bucket ID for documents which do not fall into a bucket
        output: {                                        // Output for each bucket
          total: { $sum: 1},
          artists: {
            $push: {
              name: { $concat: [ "$first_name", " ", "$last_name"] },
              year_born: "$year_born"
            }
          }
        }
      }
    }
  ]
  ```
- Output:
  ```
  [
    {
      _id: 1840, // boundary as _id
      total: 1,  // $sum is added by 1 for each record.
      artists: [
        { name: 'Odilon Redon', year_born: 1840 }
      ]
    },
    {
      _id: 1850,
      total: 2,
      artists: [
          { name: 'Vincent Van Gogh', year_born: 1853 },
          { name: 'Edvard Diriks', year_born: 1855 }
      ]
    },
    {
      _id: 1860,
      total: 4,
      artists: [
          { name: 'Emil Bernard', year_born: 1868 },
          { name: 'Joszef Rippl-Ronai', year_born: 1861 },
          { name: 'Alfred Maurer', year_born: 1868 },
          { name: 'Edvard Munch', year_born: 1863 }
      ]
    },
    {
      _id: 1870,
      total: 1,
      artists: [
          { name: 'Anna Ostroumova', year_born: 1871 }
      ]
    }
  ]
  ```

#### $count:
- data:
  ```
  [
    {
        fname: "maddala",
        middlename: "Jai",
        lname: "Shankar"
    },
    {
        fname: "maddala",
        middlename: "veera nooka",
        lname: "Govind"
   }
  ]
  ```
- Query: Return the count of ppl whose fname is maddala.
  ```
  const pipeline = [
    {
      $match: {
        fname: "maddala"
      }
    },
    {
        $count: "length"
    }
  ]
  db.collection.aggregate(pipeline);
  ```
- Output:
  ```
  {
    length: 2
  }
  ```

#### $group:
- data:
  ```
  [
    { "_id": 1, "item": "abc", "price": parseFloat("10"), "quantity": parseInt("2"), },
    { "_id": 2, "item": "jkl", "price": parseFloat("20"), "quantity": parseInt("1"), },
    { "_id": 3, "item": "xyz", "price": parseFloat("5"), "quantity": parseInt("10"), },
    { "_id": 4, "item": "xyz", "price": parseFloat("5"), "quantity": parseInt("20"), },
    { "_id": 5, "item": "abc", "price": parseFloat("10"), "quantity": parseInt("10"), },
    { "_id": 6, "item": "def", "price": parseFloat("7.5"), "quantity": parseInt("5"), },
    { "_id": 7, "item": "def", "price": parseFloat("7.5"), "quantity": parseInt("10"), },
    { "_id": 8, "item": "abc", "price": parseFloat("10"), "quantity": parseInt("5"), },
  ]
  ```
- Query:
  ```
  const pipeline = [
    {
      $group: {
        _id: "$item",
        total_quantity: { $push: "$quantity" },
        each_cost: { $first: "$price" },
        total_cost: { $sum: { $multiply:  [ "$price", "$quantity"] } },
        min: { $min: "$quantity" },
        max: { $max: "$quantity" },
        all_details: {
          $push: "$$ROOT"
        }
      }
    }
  ]
  ```
- Output:
  ```
  [
    {
      _id: 'abc',
      total_quantity: [ 2, 10, 5],
      each_cost: 10,
      total_cost: 170,
      min: 2,
      max: 10,
      all_details: [
        {
          _id: 1,
          item: 'abc',
          price: 10,
          quantity: 2
        },
        {
          _id: 5,
          item: 'abc',
          price: 10,
          quantity: 10
        },
        {
          _id: 8,
          item: 'abc',
          price: 10,
          quantity: 5
        }
      ]
    },
    .....

    {
      _id: 'def',
      total_quantity: [ 5, 10 ],
      each_cost: 7.5,
      total_cost: 112.5,
      min: 5,
      max: 10,
      all_details: [
        {
          _id: 6,
          item: 'def',
          price: 7.5,
          quantity: 5
        },
        {
          _id: 7,
          item: 'def',
          price: 7.5,
          quantity: 10
        }
      ]
    }
  ]
  ```

#### $limit:
- data:
  ```
  [
        { "_id": 1, "item": "abc", "price": parseFloat("10"), "quantity": parseInt("2"), },
        { "_id": 2, "item": "jkl", "price": parseFloat("20"), "quantity": parseInt("1"), },
        { "_id": 3, "item": "xyz", "price": parseFloat("5"), "quantity": parseInt("10"), },
        { "_id": 4, "item": "xyz", "price": parseFloat("5"), "quantity": parseInt("20"), },
        { "_id": 5, "item": "abc", "price": parseFloat("10"), "quantity": parseInt("10"), },
        { "_id": 6, "item": "def", "price": parseFloat("7.5"), "quantity": parseInt("5"), },
        { "_id": 7, "item": "def", "price": parseFloat("7.5"), "quantity": parseInt("10"), },
        { "_id": 8, "item": "abc", "price": parseFloat("10"), "quantity": parseInt("5"), },
  ]
  ```
- Query:
  ```
  const pipeline = [
    {
      $limit: 1
    }
  ]
  ```
- Output:
  ```
  [
    { "_id": 1, "item": "abc", "price": parseFloat("10"), "quantity": parseInt("2") }
  ]
  ```

#### $sort:
- Query: For above data, sort by _id in descending order.
  ```
  const pipeline = [
    {
      $limit: 5
    },
    {
      $sort: { _id: -1 } // -1 for descending, 1 for ascending
    }
  ]
  ```
- Output:
  ```
  [
    { "_id": 5, "item": "abc", "price": parseFloat("10"), "quantity": parseInt("10"), },
    { "_id": 4, "item": "xyz", "price": parseFloat("5"), "quantity": parseInt("20"), },
    { "_id": 3, "item": "xyz", "price": parseFloat("5"), "quantity": parseInt("10"), },
    { "_id": 2, "item": "jkl", "price": parseFloat("20"), "quantity": parseInt("1"), },
    { "_id": 1, "item": "abc", "price": parseFloat("10"), "quantity": parseInt("2"), },
  ]
  ```

#### $lookup:
- data-1: [collection: "names"]
  ```
  [
    {
      _id: 1,
      name: "Govind Maddala",
      colg_id: 11,
      loc_id: 444
    },
    {
        _id: 2,
        name: "Akhil Koduri",
        colg_id: 11,
        loc_id: 111
    },
    {
        _id: 3,
        name: "Akhil Nagulamalli",
        colg_id: 22,
        loc_id: 222
    }
  ]
  ```
- data-2: [collection: "colg"]
  ```
  [
    {
        _id: 11,
        colgName: "NIT Raipur"
    },
    {
        _id: 22,
        colgName: "NIT Jamshedpur"
    },
    {
        _id: 33,
        colgName: "Simhadri Colg"
    }
  ]
  ```
- data-3: [collection: "howConnected"]
  ```
  [
    {
        _id: 111,
        loc: "Engg"
    },
    {
        _id: 222,
        loc: "JSW"
    },
    {
        _id: 333,
        loc: "Enmovil"
    },
    {
        _id: 444,
        loc: "Life"
    },
  ]
  ```
- Query: for name: "Govind Maddala", get all the details.
  ```
  const pipeline = [
    {
      $match: {
        name: "Govind Maddala"
      }
    },
    {
      $lookup: {
        from: "colg",
        localField: "colg_id",
        foreignField: "_id",
        as: "colg_name"
      }
    },
    {
      $lookup: {
        from: "howConnected",
        localField: "loc_id",
        foreignField: "_id",
        as: "how_connected"
      }
    },
    {
      $replaceRoot: {
        newRoot: {
          $mergeObjects: [
            {
              name: "$name",
              collegeName: { $arrayElemAt: ["$colg_name.colgName",0]},
              how_connected: { $arrayElemAt: ["$how_connected.loc",0]}
            }
          ]
        }
      }
    }
  ]
  ```
- Output:
  ```
  [
    {
      name: 'Govind Maddala',
      collegeName: [
        {
          _id: 11,
          colgName: 'NIT Raipur'
        }
      ],
      howConnected: [
        {
          _id: 444,
          loc: 'Life'
        }
      ]
    }
  ]

  // With $replaceRoot pipeline stage:
  {
    name: 'Govind Maddala',
    collegeName: 'NIT Raipur',
    howConnected: 'Life'
  }
  ```

#### $match:
- Notes:
  - Filters the documents to pass only the documents that match the specified condition(s) to the next pipeline stage.
  - similar to query provided in find
- Data:
  ```
  [
    { "_id": new ObjectId("512bc95fe835e68f199c8686"), "author": "dave", "score": 80, "views": 100 },
    { "_id": new ObjectId("512bc962e835e68f199c8687"), "author": "dave", "score": 85, "views": 521 },
    { "_id": new ObjectId("55f5a192d4bede9ac365b257"), "author": "ahn", "score": 60, "views": 1000 },
    { "_id": new ObjectId("55f5a192d4bede9ac365b258"), "author": "li", "score": 55, "views": 5000 },
    { "_id": new ObjectId("55f5a1d3d4bede9ac365b259"), "author": "annT", "score": 60, "views": 50 },
    { "_id": new ObjectId("55f5a1d3d4bede9ac365b25a"), "author": "li", "score": 94, "views": 999 },
    { "_id": new ObjectId("55f5a1d3d4bede9ac365b25b"), "author": "ty", "score": 95, "views": 1000 }
  ]
  ```
- Query: 
  - For normal find query:
    ```
    {
        author: "dave",
        $or:[{score:{$gte:81}},{views:{eq:521}}]
    }
    ```
  - Using aggregate, the same query is: 
    ```
    const pipeline = [
      {
        $match: {
          author: "dave",
          $or:[{score:{$gte:81}},{views:{eq:521}}]
        }
      }
    ]
    db.collection.aggregate(pipeline)
    ```
- Output:
  ```
  [
   { "_id": new ObjectId("512bc962e835e68f199c8687"), "author": "dave", "score": 85, "views": 521 }, 
  ]
  ```

#### $merge:
- Notes:
  - Writes the results of the aggregation pipeline to a specified collection.
  - The $merge operator must be the last stage in the pipeline.
  - Can output to a collection in the same or different database.
  - Read operations of the $merge statement are sent to secondary nodes, while the write operations occur only on the primary node.
  - Creates a new collection if the output collection does not already exist.
  - Can incorporate results (insert new documents, merge documents, replace documents, keep existing documents, fail the operation, process documents with a custom update pipeline) into an existing collection.
- Data:
  ```
  [
    { "_id": new ObjectId("512bc95fe835e68f199c8686"), "author": "dave", "score": 80, "views": 100 },
    { "_id": new ObjectId("512bc962e835e68f199c8687"), "author": "dave", "score": 85, "views": 521 },
    { "_id": new ObjectId("55f5a192d4bede9ac365b257"), "author": "ahn", "score": 60, "views": 1000 },
    { "_id": new ObjectId("55f5a192d4bede9ac365b258"), "author": "li", "score": 55, "views": 5000 },
    { "_id": new ObjectId("55f5a1d3d4bede9ac365b259"), "author": "annT", "score": 60, "views": 50 },
    { "_id": new ObjectId("55f5a1d3d4bede9ac365b25a"), "author": "li", "score": 94, "views": 999 },
    { "_id": new ObjectId("55f5a1d3d4bede9ac365b25b"), "author": "ty", "score": 95, "views": 1000 }
  ]
  ```
- Query: The output of output1 should be merged into a collection named "mergedColl"
  ```
  const pipeline1 = [
      {
        $match: {
          author: "dave",
          $or:[{score:{$gte:81}},{views:{eq:521}}]
        }
      }
  ]

  const pipeline = [
    {
      $match: {
        author: "dave",
        $or:[{score:{$gte:81}},{views:{eq:521}}]
      }
    },
    {
      $merge: {
        into: "mergedColl"
      }
    }
  ]

  await db.collection.aggregate(pipeline);
  //Now:
  await database.connect("aggregate", "mergedColl").collection().find().toArray();
  ```
- Output:
  ```
  [
   { "_id": new ObjectId("512bc962e835e68f199c8687"), "author": "dave", "score": 85, "views": 521 }, 
  ]
  ```

#### $out:
- Notes:
  -  Takes the documents returned by the aggregation pipeline and writes them to a specified collection.
  -  must be the last stage in the pipeline. 
  -  The $out operator lets the aggregation framework return result sets of any size.
  -  Warning: replaces the specified collection if it exists.
  -  Syntax: { $out: { db: "output-db", coll: "output-collection" } }
  -  Importance: 
     -  You cannot specify a sharded collection as the output collection. And the input collection for a pipeline can be sharded. 
     -  To output to a sharded collection, use $merge
- Data: (above)
- Query:
  ```
  const pipeline = [
    {
      $match: {
        author: "dave",
        $or:[{score:{$gte:81}},{views:{eq:521}}]
      }
    },
    {
      $out: "mergedColl" // in same database with new collection name, 
      (or)
      $out: { db: "outerDB, coll: outerColl }
    }
  ]

  await db.collection.aggregate(pipeline);
  //Now:
  await database.connect("outerDB", "outerColl").collection().find().toArray();
  ```
- Output:
  ```
  [
   { "_id": new ObjectId("512bc962e835e68f199c8687"), "author": "dave", "score": 85, "views": 521 }, 
  ]
  ```

### Pipeline Operators: