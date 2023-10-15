## Basic Setup

[Got to top](#let-start-express)

**Embedded on `index.jx` to Connect.**

```Js
const express = require("express");
const cors = require("cors");
const { MongoClient, ServerApiVersion, ObjectId } = require("mongodb");
const jwt = require("jsonwebtoken"); // import jwt if use JsonWeb token
require("dotenv").config(); // import env file if use environment variables after install || npm install dotenv --save|| ane Create a .env file in the root of your project:
const port = process.env.PORT || 5000;
const app = express();
// used Middleware
app.use(cors());
app.use(express.json());
// Connact With MongoDb Database
const uri = `mongodb+srv://${process.env.DB_USER}:${process.env.DB_PASS}@cluster0.krxly.mongodb.net/?retryWrites=true&w=majority`;
const client = new MongoClient(uri, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
  serverApi: ServerApiVersion.v1,
});
// Create a async fucntion to all others activity
async function run() {
  try {
    await client.connect();
    // Create Database to store Data
    const testCollection = client.db("testDatabaseName").collection("testData");
  } finally {
    // await client.close();
  }
}
// Call the function you declare abobe
run().catch(console.dir);
// Root Api to check activity
app.get("/", (req, res) => {
  res.send("Hello From NR Computers!");
});
app.listen(port, () => {
  console.log(`NR Computers listening on port ${port}`);
});
```

## Get Method

[Got to top](#let-start-express)

### Get/Access all data from MongoDB database

`Server Site Code`

```Js
app.get("/service", async (req, res) => {
      const result = await testCollection.find().toArray();
      res.send(result);
    });
```

`Client site code`

```Js
useEffect(() => {
      fetch("http://localhost:5000/service")
        .then((res) => res.json())
        .then((data) => console.log(data));
    }, []);
```

## Get/Access Specific data from Mongodb database with id/email

`Server Site Code`

```Js
app.get("/service/:id", async (req, res) => {
const id = req.params.id;
const result = await testCollection.find({ _id: new ObjectId(id) }).toArray();
res.send(result);
});
```

`Client site code`

```Js
useEffect(() => {
fetch(`http://localhost:5000/service/${id}`)
.then((res) => res.json())
.then((data) => console.log(data));
}, []);
```

## Get/Access Specific data from MongoDB database with multiple queries

`Server Site Code`

```Js
app.get("/service", async (req, res) => {
const query = req.body;
const result = await testCollection.find(query).toArray();
res.send(result);
});
```

`Client site code`

```Js
    const query = {
      email: "exampale@example.com",
      name: "set name",
      time: "set time",
    };
    useEffect(() => {
      fetch("http://localhost:5000/service", {
        method: "GET",
        body: JSON.stringify(query), // send any of query by which you find data
      })
        .then((res) => res.json())
        .then((data) => console.log(data));
    }, []);
```

## Get/Access Limited data from the MongoDB database after finding the query

`Server Site Code`

```Js
    app.get("/service", async (req, res) => {
      const query = {};
      const findData = testCollection.find(query);
      const result = await findData.skip(5).limit(3).toArray(); // you can also set skip & limit range dynamicly.
      res.send(result);
    });
```

`Client site code`

```Js
 useEffect(() => {
      fetch("http://localhost:5000/service")
        .then((res) => res.json())
        .then((data) => console.log(data));
    }, []);
```

## Post Method

[Got to top](#let-start-express)

### Post data without checking on MongoDB database

`Server Site Code`

```Js
app.post("/service", async (req, res) => {
const service = req.body;
const result = await testCollection.insertOne(service);
res.send(result);
});
```

`Client site code`

```Js
    const newData = {
      // store new Data in Object here
      name: "",
      price: "",
    };
    fetch("http://localhost:5000/service", {
      method: "POST",
      headers: {
        "content-type": "application/json",
      },
      body: JSON.stringify(newData),
    })
      .then((res) => res.json())
      .then((result) => {
        const allData = [...previusData, newData];
        console.log(newReview);
      });
```

### Post data With check-in Mongodb database. Batter to use Put Methood for this task

`Server Site Code`

```Js
    app.post("/service/:email", async (req, res) => {
      const service = req.body.service;
      const query = req.body.newQuery;
      query.email = email;
      const exist = testCollection.findOne(query);
      if (exist) {
        res.send({ result: "success" });
      } else {
        const result = await testCollection.insertOne(service);
        res.send(result);
      }
    });
```

`Client Site Code`

```Js
    const service = {
      name: "",
      price: "",
    };
    const newQuery = {
      name: "",
      time: "",
    };
    fetch("http://localhost:5000/service", {
      method: "POST",
      headers: {
        "content-type": "application/json",
      },
      body: JSON.stringify({ service, newQuery }),
    })
      .then((res) => res.json())
      .then((result) => {
        const allData = [...previusData, newData];
        console.log(newReview);
      });
```

## Put Method

[Got to top](#let-start-express)

### Update & insert Data on the database by id/email/otherQuery

`Server site Code`

```Js
    app.put("/service/:id", async (req, res) => {
      const id = req.params.id;
      const service = req.body;
      const result = await testCollection.updateOne(
        { _id: new ObjectId(id) }, // Find Data by query many time query type is "_id: id" Cheack on database
        {
          $set: service, // Set updated Data
        },
        { upsert: true } // define work
      );
      res.send({ result });
    });
```

`Client Site Code`

```Js
const updatedData = {
name: "",
role: "",
};
    fetch(`http://localhost:5000/service/${id}`, {
      method: "PUT",
      headers: {
        "content-type": "application/json",
      },
      body: JSON.stringify(updatedData),
    })
      .then((res) => res.json())
      .then((data) => {
        alert("item Updated successfully!!!");
      });
```

## Patch Method

[Got to top](#let-start-express)

### update Data by id/email/othersQueary

`Server site code`

```Js
app.patch("/service/:id", async (req, res) => {
const id = req.params.id;
const service = req.body;
const result = await testCollection.updateOne(
{ _id: id }, // sometime _id: new ObjectId(id)
{
$set: service,
}
);
res.send(result);
});
```

`Client site code`

```Js
const updatedData = {
      name: "",
      role: "",
    };
    fetch(`http://localhost:5000/service/${id}`, {
      method: "patch",
      headers: {
        "content-type": "application/json",
      },
      body: JSON.stringify(updatedData),
    })
      .then((res) => res.json())
      .then((data) => {
        alert("item Updated successfully!!!");
      });
```

## Delete Method

[Got to top](#let-start-express)

### Delete Data from MongoDB Database by id

`Server site code`

```Js
app.delete("/service/:id", async (req, res) => {
const id = req.params.id;
const result = await testCollection.deleteOne({ _id: new ObjectId(id) });
res.send(result);
});
```

`Client Site Code`

```Js
    fetch(`http://localhost:5000/service/${_id}`, {
      method: "DELETE",
    })
      .then((res) => res.json())
      .then((data) => {});
```

## Count the number of arrays

```Js
app.get("/billsCount", async (req, res) => {
    const count = await billCollection.countDocuments();
    res.send({ count });
});
        
```
