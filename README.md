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
- Query: get data where a person worked in one company only i.e array size of jobs should be equals to 1.
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
- Notes:
  - Sorts all input documents and returns them to the pipeline in sorted order.
  - The $sort stage has the following prototype form: { $sort: { \<field1>: \<sort order>, \<field2>: <sort order> ... } }
  - $sort takes a document that specifies the field(s) to sort by and the respective sort order. 
  - \<sort order> can have one of the following values:
    - 1   : sort ascending
    - -1  : sort descending
  - Limits : You can sort on a maximum of 32 keys.
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


#### $project:
- Notes:
  - Passes along the documents with the requested fields to the next stage in the pipeline. 
  - The specified fields can be existing fields from the input documents or newly computed fields.
  - Syntax: { $project: { <specification(s)> } }
  - Specifications have the following forms:
      - \<field>\: 
        - <1 or true>: inclusion of a field. Non-zero integers are also treated as true.
        - <0 or false>: suppression or exclusion of the field.
        - \<expression>: If the the expression evaluates to **$$REMOVE**, the field is excluded in the output.
  - **Embedded Document Fields**: 
    - Either use dot notation (Or) you can nest the fields
      - dot notation: eg: "contact.address.country": <1 or 0 or expression>
      - nesting field notation: eg: contact: { address: { country: <1 or 0 or expression> } }
        Note: When nesting the fields, you cannot use dot notation inside the embedded document to specify the field, 
        e.g. contact: { "address.country": <1 or 0 or expression> } is invalid.
  - Path Collision Errors in Embedded Fields
    - You cannot specify both an embedded document and a field within that embedded document in the same projection.
    - The following $project stage fails with a Path collision error because it attempts to project both the embedded contact document and the contact.address.country field:
      - { $project: { contact: 1, "contact.address.country": 1 } }
  - Note:
    - When you use a $project stage it should typically be the last stage in your pipeline, used to specify which fields to return to the client.
    - Using a $project stage at the beginning or middle of a pipeline to reduce the number of fields passed to subsequent pipeline stages is unlikely to improve performance, as the
    - database performs this optimization automatically.
  - MongoDB's $project does not allow mixing inclusion and exclusion in the same object — except for the _id field.
  - Restrictions:
    - An error is returned if the $project specification is an empty document.
    - You cannot use an array index with the $project stage.
  - $unset stage can also be used to exclude fields.
- Data:
  ```
  [
    {
        "_id": 1,
        title: "abc123",
        isbn: "0001122223334",
        author: { last: "zzz", first: "aaa" },
        address: { city: "Hyderabad" },
        copies: 5
    },
    {
        "_id": 2,
        title: "Baked Goods",
        isbn: "9999999999999",
        author: { last: "xyz", first: "abc", middle: "" },
        address: { city: "Hyderabad" },
        copies: 2,
        lastModified: "2017-07-21"
    },
    {
        "_id": 3,
        title: "Ice Cream Cakes",
        isbn: "8888888888888",
        author: { last: "xyz", first: "abc", middle: "mmm" },
        address: { city: "Hyderabad" },
        copies: 5,
        lastModified: "2017-07-22"
    }
  ]
  ```
- Query:
  ```
  const pipeline = [
    {
      $project: {
        _id: 0,
        title: 1,
        // "address.city": 0, // throws error because MongoDB's $project does not allow mixing inclusion and exclusion in the same object — except for the _id field.
        "author.first": 1,
        "author.last": 1,
        "author.middleChanged": {
            $cond:{
                if:{$eq:["","$author.middle"]},
                then:"$$REMOVE",
                else:"$author.middle"
            }
        }, 
        "author.middle":1 
      }
    }
  ]

  ```
- Output:
  ```
  [
    {
      title: 'abc123',
      author: {
        last: 'zzz',
        first: 'aaa'
      }
    },
    {
      title: 'Baked Goods',
      author: {
        last: 'xyz',
        first: 'abc',
        middle: ''
      }
    },
    {
      title: 'Ice Cream Cakes',
      author: {
        last: 'xyz',
        first: 'abc',
        middle: 'mmm',
        middleChanged: 'mmm'
      }
    }
  ]
  ```

#### $replaceRoot
- Notes:
  - Replaces the input document with the specified document. The operation replaces all existing fields in the input document, including the _id field. You can promote an existing embedded document to the top level, or create a new document for promotion.
  - The $replaceRoot stage has the following form:
    ```{ $replaceRoot: { newRoot: \<replacementDocument\> } }```
  - The stage errors and fails if \<replacementDocument\> :
    - is not a document. 
    - resolves to a missing document (i.e. the document does not exist),
    - Ex:
      ```
      db.collection.insertMany(
        [
          { "_id": 1, "name" : { "first" : "John", "last" : "Backus" } },
          { "_id": 2, "name" : { "first" : "John", "last" : "McCarthy" } },
          { "_id": 3, "name": { "first" : "Grace", "last" : "Hopper" } },
          { "_id": 4, "firstname": "Ole-Johan", "lastname" : "Dahl" },
        ]
      )
      ```
      - and the error: MongoServerError: PlanExecutor error during aggregation :: caused by :: 'newRoot' expression must evaluate to an object, but resulting value was: MISSING. Type of resulting value: 'missing'. Input document: {}

    - How to handle this: 
      - To avoid the error, you can use $mergeObjects to merge the name document into some default document. for example: 
      ```
      db.collection.aggregate([
          { $replaceRoot: { newRoot: { $mergeObjects: [ { _id: "$_id", first: "", last: "" }, "$name" ] } } }
      ])
      ```
      - Alternatively, you can skip the documents that are missing the name field by including a $match stage to check for existence of the document field before passing documents to the $replaceRoot stage: Ex:
      ```
      db.collection.aggregate([
          { 
            $match: { 
              name : { 
                $exists: true, 
                $not: { 
                  $type: "array" 
                }, 
                $type: "object" 
              } 
            } 
          },
          { 
            $replaceRoot: { 
              newRoot: "$name" 
            } 
          }
      ]);
      ```
      - Or, you can use $ifNull expression to specify some other document to be root; for example:
      ```
      db.collection.aggregate([
          { 
            $replaceRoot: { 
              newRoot: { 
                $ifNull: [ "$name", { _id: "$_id", missingName: true} ] 
              } 
            } 
          }
      ])
      ```
##### Missing Values:
  - Data:
    ```
    [
        { "_id": 1, "name": { "first": "John", "last": "Backus" } },
        { "_id": 2, "name": { "first": "John", "last": "McCarthy" } },
        { "_id": 3, "name": { "first": "Grace", "last": "Hopper" } },
        { "_id": 4, "firstname": "Ole-Johan", "lastname": "Dahl" },
    ]
    ```
  - Query: 
    ```
    db.collection.aggregate([
      {
        $replaceRoot: {
          newRoot: {
            "$name"
          }
        }
      }
    ])
    ```
  - Output: 
    - [Because for _id: 4, there is no name field], it throws an error.
    - If _id: 4 is not there, output would be like this:
      ```
      [
        { first: 'John', last: 'Backus' },
        { first: 'John', last: 'McCarthy' },
        { first: 'Grace', last: 'Hopper' },
      ]
      ```
- How to handle:
  - Checking if name exists:
    ```
    const pipeline = [ 
      { 
        $match: { 
          name: { $exists: true, $type: "object"} 
        } 
      }, 
      { 
        $replaceRoot: { 
          newRoot: "$name"
        } 
      } 
    ]
    ```
  - Check if name is null and give alternative:
  ```
  const pipeline = [
    { 
      $replaceRoot: { 
        newRoot: { 
          $ifNull: [ "$name", { _id: "$_id", missingDetails: true, actual: "$$ROOT" } ] 
        } 
      } 
    }
  ]
  ```
  - Output for missing name is as follows: 
    ```
    [
      {
        _id: 4,
        missingDetails: true,
        actual: {
          _id: 4,
          firstname: 'Ole-Johan',
          lastname: 'Dahl'
        }
      }
    ]
    ```
##### Embedded docx:
- Data:
  ```
  [
    { "_id": 1, "name": "Arlene", "age": 34, "pets": { "dogs": 2, "cats": 1 } },
    { "_id": 2, "name": "Sam", "age": 41, "pets": { "cats": 1, "fish": 3 } },
    { "_id": 3, "name": "Maria", "age": 25 }
  ]
  ```
- Query: Push data { dogs: 5, cats: 2, fish: 5, rabbit: 20 } into pets object.
  ```
  const pipeline = [
    {
      $replaceRoot: {
        newRoot: {
          $mergeObject: [
            <!-- "$$ROOT", -->
            {
              dogs: 5, cats: 2, fish: 5, rabbit: 20
            },
            "$pets"
          ]
        }
      }
    }
  ]
  ```
- Output: 
  ```
  [
    { dogs: 2, cats: 1, fish: 5, rabbit: 20 },
    { dogs: 5, cats: 1, fish: 3, rabbit: 20 },
    { dogs: 5, cats: 2, fish: 5, rabbit: 20 }
  ]
  ```
- Explanation:
  Explanation:
    - The $mergeObjects expression merges the specified default document with the pets document.
    - $replaceRoot stage is to replace each input document with the result of a $mergeObjects operation.

##### Docx Nested in array:
- Data:
  ```
  [
    {
        "_id": 1,
        "grades": [
            { "test": 1, "grade": 80, "mean": 75, "std": 6 },
            { "test": 2, "grade": 85, "mean": 90, "std": 4 },
            { "test": 3, "grade": 95, "mean": 85, "std": 6 }
        ]
    },
    {
        "_id": 2,
        "grades": [
            { "test": 1, "grade": 90, "mean": 75, "std": 6 },
            { "test": 2, "grade": 87, "mean": 90, "std": 3 },
            { "test": 3, "grade": 91, "mean": 85, "std": 4 }
        ]
    }
  ]
  ```
- Query: Get grades only with grade > 85:
  ```
  const pipeline = [
    {
      $unwind: "$grades"              
    },
    // { "_id": 2, grades: { "test": 1, "grade": 90, "mean": 75, "std": 6 } }
    {
      $replaceRoot: "$grades"
    },
    // { "test": 1, "grade": 90, "mean": 75, "std": 6 }
    {
      $match: {
        grade: { $gt: 85 }
      }
    }
  ]
  ```
- Output: 
  ```
  [
    { "test": 3, "grade": 95, "mean": 85, "std": 6 },
    { "test": 1, "grade": 90, "mean": 75, "std": 6 },
    { "test": 2, "grade": 87, "mean": 90, "std": 3 },
    { "test": 3, "grade": 91, "mean": 85, "std": 4 }
  ]
  ```
- Explanation:
  - The $mergeObjects expression merges the specified default document with the pets document.
  - $replaceRoot stage is to replace each input document with the result of a $mergeObjects operation.
  
##### Using root:
- Data:
  ```
  [
      { "_id": 1, name: "Fred", email: "fred@example.net" },
      { "_id": 2, name: "Frank N. Stine", cell: "012-345-9999" },
      { "_id": 3, name: "Gren Dell", home: "987-654-3210", email: "beo@example.net" }
  ]
  ```
- Query:
  ```
  const pipeline = [
    {
        $replaceRoot: {
            newRoot: {
                $mergeObjects: [
                    { _id: "", name: "", email: "", cell: "", home: "" },
                    "$$ROOT"
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
      _id: 1,
      name: 'Fred',
      email: 'fred@example.net',
      cell: '',
      home: ''
    },
    {
      _id: 2,
      name: 'Frank N. Stine',
      email: '',
      cell: '012-345-9999',
      home: ''
    },
    {
      _id: 3,
      name: 'Gren Dell',
      email: 'beo@example.net',
      cell: '',
      home: '987-654-3210'
    }
  ]
  ```
- Observation: It keeps empty values first and empty values are replaced by root values.

#### $replaceWith:
- Notes:
  - Replaces the input document with the specified document. The operation replaces all existing fields in the input document, 
   including the _id field.
  - The $replaceWith is an alias for $replaceRoot.
  - Syntax: { $replaceWith: \<replacementDocument> }
  - If the \<replacementDocument> is not a document, $replaceWith errors and fails.
  - Refer rest other notes from replaceRoot or from mongodb documentation since both are almost same
- Data:
  ```
  [
      { "_id": 1, "name": "Arlene", "age": 34, "pets": { "dogs": 2, "cats": 1 } },
      { "_id": 2, "name": "Sam", "age": 41, "pets": { "cats": 1, "fish": 3 } },
      { "_id": 3, "name": "Maria", "age": 25 }
  ]
  ```
- Query:
  ```
  const pipeline = [
    {
      $replaceWith: {
        $mergeObjects: [{
          dogs: 5, cats: 2, fish: 5, rabbit: 20
        }, "$pets"]
      }
    }
  ]
  ```
- Output:
  ```
  [
    {
      dogs: 2,
      cats: 1,
      fish: 5,
      rabbit: 20
    },
    {
      dogs: 5,
      cats: 1,
      fish: 3,
      rabbit: 20
    },
    {
      dogs: 5,
      cats: 2,
      fish: 5,
      rabbit: 20
    }
  ]
  ```
- Data-2:
  ```
  [
      { "_id": 1, "item": "butter", "price": 10, "quantity": 2, date: "2019-03-01T08:00:00Z", status: "C" },
      { "_id": 2, "item": "cream", "price": 20, "quantity": 1, date: "2019-03-01T09:00:00Z", status: "A" }
  ]
  ```
- Query:
  ```
  const pipeline = [
    {
      $replaceWith:{
          item_name:"$item",
          amount:{ $multiply:[ "$price", "$quantity" ] },
          status:"COMPLETE",
          asOfNow:"$$NOW"
      }
    }
  ]
  ```
- Output:
  ```
  [
    {
      item_name: 'butter',
      amount: 20,
      status: 'COMPLETE',
      asOfNow: 2025-07-20T09:38:24.864Z
    },
    {
      item_name: 'cream',
      amount: 20,
      status: 'COMPLETE',
      asOfNow: 2025-07-20T09:38:24.864Z
    }
  ]
  ```

#### $sample:
- Notes:
  - Randomly selects the specified number of documents from the input documents.
  - Syntax: { $sample: { size: \<positive integer N> } }
  - Behavior:
    - If all of the following conditions are true, $sample uses a pseudo-random cursor to select the N documents:
      - $sample is the first stage of the pipeline.
      - N is less than 5% of the total documents in the collection.
      - The collection contains more than 100 documents.
    - If any of the previous conditions are false, $sample:
      - Reads all documents that are output from a preceding aggregation stage or a collection scan.
      - Performs a random sort to select N documents.
- Data:
  ```
  [
      { "_id": 1, "name": "dave123", "q1": true, "q2": true },
      { "_id": 2, "name": "dave2", "q1": false, "q2": false },
      { "_id": 3, "name": "ahn", "q1": true, "q2": true },
      { "_id": 4, "name": "li", "q1": true, "q2": false },
      { "_id": 5, "name": "annT", "q1": false, "q2": true },
      { "_id": 6, "name": "li", "q1": true, "q2": true },
      { "_id": 7, "name": "ty", "q1": false, "q2": true }
  ]
  ```
- Query: 
  ```
  const pipeline = [
    {
      $sample: {
        size: 2
      }
    }
  ]
  ```
- Output: 
  ```
  [
    { "_id": 5, "name": "annT", "q1": false, "q2": true },
    { "_id": 1, "name": "dave123", "q1": true, "q2": true }
  ]
  ```

#### $set:
- Notes:
  - Adds new fields to documents. $set outputs documents that contain all existing fields from the input documents and newly added fields.
  - The $set stage is an alias for $addFields.
  - Both stages are equivalent to a $project stage that explicitly specifies all existing fields in the input documents and adds the new 
      fields.
  - You can include one or more $set stages in an aggregation operation.
  - To add field or fields to embedded documents (including documents in array- use the dot notation.)
  - To add an element to an existing array field with $set, use with $concatArrays.
  - $set has the following form: { $set: { \<newField>: \<expression>, ... } }
  
- Data:
  ```
  [
    {
        _id: 1,
        student: "Maya",
        homework: [10, 5, 10],
        quiz: [10, 8],
        extraCredit: 0
    },
    {
        _id: 2,
        student: "Ryan",
        homework: [5, 6, 5],
        quiz: [8, 8],
        extraCredit: 8
    }
  ]
  ```
- Query:
  ```
  const pipeline = [
      {
          <!-- // $addFields:{}  -->
          $set: {
              homeworkMarks: { $sum: "$homework" },
              totalQuizMarks: { $sum: "$quiz" }
          }
      },
      {
          <!-- // $addFields: {}  -->
          $set: {
              allSum: {
                  $add: ["$homeworkMarks", "$totalQuizMarks", "$extraCredit"]
              }
          }
      }
  ]
  ```
- Output: 
  ```
  [
    {
        _id: 1,
        student: "Maya",
        homework: [10, 5, 10],
        quiz: [10, 8],
        extraCredit: 0,
        homeworkMarks: 25,
        totalQuizMarks: 18,
        allSum: 43
    },
    {
        _id: 2,
        student: "Ryan",
        homework: [5, 6, 5],
        quiz: [8, 8],
        extraCredit: 8,
        homeworkMarks: 16,
        totalQuizMarks: 16,
        allSum: 40
    }
  ]
  ```
#### $skip:
- Notes:
  - Skips over the specified number of documents that pass into the stage and passes the remaining documents to the next stage in the pipeline.
  - syntax: { $skip: \<positive 64-bit integer> }
  - $skip takes a positive integer that specifies the maximum number of documents to skip.
  - If $sort [aggregation stage], sort() and  findAndModify commands are using, be sure to include at least one field in your sort that contains unique values, before passing results to the $skip stage.
- Query:
  ```
  const pipeline = [
    {
      $match: {
        Conditons
      }
    },
    {
      $skip: 2
    }
  ]
  ```
#### $sortByCount
- Notes:
  - Groups incoming documents based on the value of a specified expression, then computes the count of documents in each distinct group.
  - Each output document contains two fields:
    - _id field containing the distinct grouping value
    - count field containing the number of documents belonging to that grouping or category.
  - The documents are sorted by count in descending order.
  - Syntax: { $sortByCount:  \<expression> }
- Data:
  ```
  [
      { "_id": 1, "title": "The Pillars of Society", "artist": "Grosz", "year": 1926, "tags": ["painting", "satire", "Expressionism", "caricature"] },
      { "_id": 2, "title": "Melancholy III", "artist": "Munch", "year": 1902, "tags": ["woodcut", "Expressionism"] },
      { "_id": 3, "title": "Dancer", "artist": "Miro", "year": 1925, "tags": ["oil", "Surrealism", "painting"] },
      { "_id": 4, "title": "The Great Wave off Kanagawa", "artist": "Hokusai", "tags": ["woodblock", "ukiyo-e"] },
      { "_id": 5, "title": "The Persistence of Memory", "artist": "Dali", "year": 1931, "tags": ["Surrealism", "painting", "oil"] },
      { "_id": 6, "title": "Composition VII", "artist": "Kandinsky", "year": 1913, "tags": ["oil", "painting", "abstract"] },
      { "_id": 7, "title": "The Scream", "artist": "Munch", "year": 1893, "tags": ["Expressionism", "painting", "oil"] },
      { "_id": 8, "title": "Blue Flower", "artist": "O'Keefe", "year": 1918, "tags": ["abstract", "painting"] }
  ]
  ```
- Query: In tags, get the count of each tag.
  ```
  const pipeline = [
    {
      $unwind: "$tags",
    },
    {
       $sortByCount:"$tags"
    }
  ]
  ```
- Output:
  ```
  [
    {
      _id: 'painting',
      count: 6
    },
    ...
    {
      _id: 'woodcut',
      count: 1
    }
  ]
  ```

#### $unionWith:
- Notes:
  - Performs a union of two collections
  - combines pipeline results from two collections into a single result set. 
  - outputs the combined result set (including duplicates) to the next stage.
  - The order in which the combined result set documents are output is unspecified.
  - Syntax: { $unionWith: { coll: "<collection>", pipeline: [ \<stage1>, ... ] } }
    - To include all documents from the specified collection without any processing, you can use the simplified form:
            { $unionWith: "\<collection>" } 
  - The combined results from the previous stage and the $unionWith stage can include duplicates.
- Data:
  - Collection:1
    ```
    [
      { _id: 1, supplier: "Aardvark and Sons", state: "Texas" },
      { _id: 2, supplier: "Bears Run Amok.", state: "Colorado" },
      { _id: 3, supplier: "Squid Mark Inc. ", state: "Rhode Island" },
    ]
    ```
  - Collection:2
    ```
    [
      { _id: 1, warehouse: "A", region: "West", state: "California" },
      { _id: 2, warehouse: "B", region: "Central", state: "Colorado" },
      { _id: 3, warehouse: "C", region: "East", state: "Florida" },
    ]
    ```
- Query:
  ```
  const pipeline = [
    {
      $project: { state: 1, _id: 0 }
    },
    {
      $unionWith: {
        coll: "unionWithDemoColl2",
        pipeline: [
          {
            $project: { state: 1, _id: 0 }
          }
        ]
      }
    },
    {
      $group: { 
        _id:"$state"
      }
    }
  ]
  ```
- Output:
  ```
  [
    { _id: "Texas" },
    { _id: "Colorado" },
    { _id: "Rhode Island" },
    { _id: "California" },
    { _id: "Florida" }
  ]
  ```

#### $unset:
- Notes:
  - Removes/excludes fields from documents.
  - Syntax: The $unset stage has the following syntax:
    - To remove a single field, the $unset takes a string that specifies the field to remove:
            { $unset: "\<field>" }
    - To remove multiple fields, the $unset takes an array of fields to remove.
            { $unset: [ "\<field1>", "\<field2>", ... ] }
    - for embedded docx: 
      - { $unset: "\<field.nestedfield>" }
      - { $unset: [ "\<field1.nestedfield>", ...] }
  - The $unset is an alias for the $project stage that removes/excludes fields:
    - { $project: { "\<field1>": 0, "\<field2>": 0, ... } }
- Data:
  ```
  [
      { "_id": 1, title: "Antelope Antics", isbn: "0001122223334", author: { last: "An", first: "Auntie" }, copies: [{ warehouse: "A", qty: 5 }, { warehouse: "B", qty: 15 }] },
      { "_id": 2, title: "Bees Babble", isbn: "999999999333", author: { last: "Bumble", first: "Bee" }, copies: [{ warehouse: "A", qty: 2 }, { warehouse: "B", qty: 5 }] }
  ]
  ```
- Query:
  ```
  const pipeline = [
    {
        $unset: "copies"
    },
    {
        $unset:["title","_id","author.last"]
    }
  ]
  ```
- Output:
  ```
  [
    {
      isbn: '0001122223334',
      author: {
        first: 'Auntie'
      }
    },
    {
      isbn: '999999999333',
      author: {
        first: 'Bee'
      }
    }
  ]
  ```

#### $unwind
- Notes:
  - Deconstructs an array field from the input documents to output a document for each element. Each output document is the input document 
    with the value of the array field replaced by the element.
  - Syntax: To unwind an array field, we can pass 
    - a field path operand or 
    - a document operand or 
    -  array field path
  - When using this syntax, $unwind does not output a document if the field value is null, missing, or an empty array.
  - { $unwind: \<field path> }
  - If you specify a path for a field that does not exist in an input document or the field is an empty array, $unwind, by default, 
    ignores the input document and will not output documents for that input document.
  - To output documents where the array field is missing, null or an empty array, use the "preserveNullAndEmptyArrays" option.
  - $unwind treats the sizes field as a single element array if:
    - the field is present,
    - the value is not null, and 
    - the value is not an empty array.
  -  preserveNullAndEmptyArrays: to include documents whose sizes field is null, missing, or an empty array.
  -  includeArrayIndex: include the array index in the output.
- Data:
  ```
  [
      { "_id": 1, "item": "ABC1", sizes: ["S1", "M1", "L1"] },
      { "_id": 2, "item": "ABC2", sizes: ["S2", "M2", "L2"] },
      { "_id": 3, "item": "ABC3", sizes: ["S3", "M3", "L3"] },
      //refer 5th point
      { "_id": 4, "item": "Gloves" },
      { "_id": 5, "item": "Scarf", "sizes": null },
      { "_id": 6, "item": "Shorts", "sizes": [] },
      { "_id": 7, "item": "Hat", "sizes": "M" },
  ]
  ```
- Query-1:
  ```
  const pipeline = [
    {
      $unwind: "$sizes"
    }
  ]
  ```
- Output-1:
  ```
  [
    { "_id": 1, "item": "ABC1", sizes: "S1" },
    { "_id": 1, "item": "ABC1", sizes: "M1" },
    { "_id": 1, "item": "ABC1", sizes: "L1" },

    { "_id": 2, "item": "ABC2", sizes: "S1" },
    { "_id": 2, "item": "ABC2", sizes: "M1" },
    { "_id": 2, "item": "ABC2", sizes: "L1" },

    { "_id": 3, "item": "ABC3", sizes: "S1" },
    { "_id": 3, "item": "ABC3", sizes: "M1" },
    { "_id": 3, "item": "ABC3", sizes: "L1" },

    { "_id": 7, "item": "Hat", "sizes": "M" },
  ]
  ```
  PS: _id: 4,5,6 data is not there for sizes tag.

- Query-2:
  ```
  const pipeline = [
    {
      $unwind: {
          path: "$sizes",
          preserveNullAndEmptyArrays: true,
          includeArrayIndex: "arrayIndex" //==> adds index of element
      }
    }
  ]
  ```
- Output-2:
  ```
  [
    { "_id": 1, "item": "ABC1", sizes: "S1", arrayIndex: 0 },
    { "_id": 1, "item": "ABC1", sizes: "M1", arrayIndex: 1 },
    { "_id": 1, "item": "ABC1", sizes: "L1", arrayIndex: 2 },

    { "_id": 2, "item": "ABC2", sizes: "S1", arrayIndex: 0 },
    { "_id": 2, "item": "ABC2", sizes: "M1", arrayIndex: 1 },
    { "_id": 2, "item": "ABC2", sizes: "L1", arrayIndex: 2 },

    { "_id": 3, "item": "ABC3", sizes: "S1", arrayIndex: 0 },
    { "_id": 3, "item": "ABC3", sizes: "M1", arrayIndex: 1 },
    { "_id": 3, "item": "ABC3", sizes: "L1", arrayIndex: 2 },

    { "_id": 4, "item": "Gloves" },

    { "_id": 5, "item": "Scarf", sizes: null, arrayIndex: null },

    { "_id": 6, "item": "Shorts", arrayIndex: null },

    { "_id": 7, "item": "Hat", "sizes": "M", arrayIndex: null },
  ]
  ```

### Pipeline Operators:
- Notes:
  - These expression operators are available to construct expressions for use in the aggregation pipeline stages.
  - similar to functions that take arguments 
    - in the form of array and have the following form:
        - { \<operator>: [ \<argument1>, \<argument2> ... ] }
    - If operator accepts a single argument, you can omit the outer array designating the argument list:
        - { \<operator>: \<argument> }
    - To avoid parsing ambiguity if the argument is a literal array, you must wrap the literal array in a $literal expression or 4
        keep the outer array that designates the argument list.
        - $literal: Returns a value without parsing. Use for values that the aggregation pipeline may interpret as an expression.
                    : syntax: { $literal: \<value> }
#### $abs:
- Notes:
  - Returns the absolute value of a number.
  - Syntax: { $abs: \<number> }
  - The \<number> expression can be any valid expression as long as it resolves to a number.
    - { $abs: -1 }    ==> 1
    - { $abs: 1 }     ==> 1
    -  { $abs:  null} ==> null
    -  { $abs:  NaN } ==> NaN
- Data:
  ```
  [
      { _id: 1, start: 5, end: 8 },
      { _id: 2, start: 4, end: 4 },
      { _id: 3, start: 9, end: 7 },
      { _id: 4, start: 6, end: 7 }
  ]
  ```
- Query:
  ```
  const pipeline = [
    {
      $project: { 
        difference: { 
            $abs: { $subtract: ["$start", "$end"] 
          } 
        } 
      }
    }
  ]
  ```
- Output:
  ```
  [
    { _id: 1, difference: 3 },
    { _id: 2, difference: 0 },
    { _id: 3, difference: 2 },
    { _id: 4, difference: 1 },
  ]
  ```

#### $add
- Notes:
  - Adds numbers together or adds numbers and a date. 
  - If one of the arguments is a date, $add treats the other arguments as milliseconds to add to the date.
  - The $add expression has the following syntax:    { $add: [ \<expression1>, \<expression2>, ... ] }
  - The arguments can be any valid expression as long as they resolve to either all numbers or to numbers and a date.
- Data:
  ```
  [
      { "_id": 1, "item": "abc", "price": 10, "fee": 2, date: new Date("2014-03-01T08:00:00Z") },
      { "_id": 2, "item": "jkl", "price": 20, "fee": 1, date: new Date("2014-03-01T09:00:00Z") },
      { "_id": 3, "item": "xyz", "price": 5, "fee": 0, date: new Date("2014-03-15T09:00:00Z") },
  ]
  ```
- Query-1:
  ```
  const pipeline1 = [
      {
          $project: { item: 1, totalSum: { $add: ["$price", "$fee"] } }
      }
  ]
  ```
- Output-1:
  ```
  [
    { "_id": 1, "item": "abc", totalSum: 12 },
    { "_id": 2, "item": "jkl", totalSum: 21 },
    { "_id": 3, "item": "xyz", totalSum: 5  },
  ]
  ```
- Query-2:
  ```
  const pipeline2 = [
      {
          $project: { item: 1, dateAddition: { $add: ["$date", 3 * 24 * 60 * 60 * 1000] } } //adding 3 days
      }
  ]
  ```
- Output-2:
  ```
  [
    { "_id": 1, "item": "abc", dateAddition: "2014-03-04T08:00:00.000Z" },
    { "_id": 2, "item": "jkl", dateAddition: "2014-03-04T09:00:00.000Z" },
    { "_id": 3, "item": "xyz", dateAddition: "2014-03-18T09:00:00.000Z" },
  ]
  ```