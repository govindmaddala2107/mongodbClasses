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