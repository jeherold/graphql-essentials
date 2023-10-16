# GraphQl Essential with mongodb for data persistence

NOTE: Requires MonogDB to use this app - built on Mac - here for reference on TGS hub

#### Built out a GraphQL Server with node and mongodb with Studio3T to see the db

## Schema

Types - for the schema types

Enums - to restrict the field type

Inputs - for the mutation inputs

## MongDB Notes on setup/ start & stop

https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-os-x/

To run MongoDB (i.e. the mongod process) as a macOS service, run:

```
brew services start mongodb-community@7.0
```

To stop a mongodb running as a macOS service, use the following command as needed:

```
brew services stop mongodb-community@7.0
```

### Example Query Get Product by Id - define what you want to see

```js
query {
  getProduct(id:"652c69919283162495c69ce6") {
    name
    description
    price
    soldout
    stores {
      store
    }
  }
}

// returns
{
  "data": {
    "getProduct": {
      "name": "Widget77",
      "description": "Widget(77)",
      "price": 34.89,
      "soldout": "SOLDOUT",
      "stores": [
        {
          "store": "Pasadena"
        },
        {
          "store": "San Diego"
        }
      ]
    }
  }
}

```

### Example Query Get All Products - define what you want to see

```js
query {
  getAllProducts {
    name
    description
    price
  }
}

// returns

{
  "data": {
    "getAllProducts": [
      {
        "name": "Widget11",
        "description": "Another widget (12) for your garden",
        "price": 34.89
      },
      {
        "name": "Widget15",
        "description": "Another widget (15) for your garden",
        "price": 34.89
      },
      {
        "name": "Widget16",
        "description": "Another widget (16) for your garden",
        "price": 34.89
      },
      {
        "name": "Widget77",
        "description": "Widget(77)",
        "price": 34.89
      }
    ]
  }
}
```

### Example Mutation in GraphiQl - create new product

```js
mutation {
  createProduct(input: {name: "Widget16", description: "Another widget (16) for your garden", price: 34.89, soldout: ONSALE, inventory: 21, stores: [{store: "Pasadena"}, {store: "San Diego"}]}) {
    id
    price
    name
    soldout
  }
}

// return once the mutation is run

{
  "data": {
    "createProduct": {
      "id": "652c69919283162495c69ce6",
      "price": 34.89,
      "name": "Widget16",
      "soldout": "ONSALE"
    }
  }
}

```

### UpdateProduct Mutation

```js
// mutation on everything - the ! in the schema means that you HAVE to always have that as part of the input
mutation {
  updateProduct(input: {
    id:"652c69919283162495c69ce6",
    name: "Widget77",
    description: "Widget(77)",
    price: 34.89,
    soldout: SOLDOUT,
    inventory: 21,
    stores: [
      {store: "Pasadena"},
      {store: "San Diego"},
    ]}) {
    id
    price
    name
    soldout
  }
}

// Return when the mutation has run
{
  "data": {
    "updateProduct": {
      "id": "652c69919283162495c69ce6",
      "price": 34.89,
      "name": "Widget77",
      "soldout": "SOLDOUT"
    }
  }
}

// update again but I only want the name and soldout returned -
// mutation
mutation {
  updateProduct(input: {
    id:"652c69919283162495c69ce6",
    name: "Widget77",
    description: "Widget(77)",
    price: 34.89,
    soldout: SOLDOUT,
    inventory: 0,
    stores: [
      {store: "Pasadena"},
      {store: "San Diego"},
    ]}) {
    name
    soldout
  }
}

// return
{
  "data": {
    "updateProduct": {
      "name": "Widget77",
      "soldout": "SOLDOUT"
    }
  }
}
```

## Example delete mutation

```js
// mutation for deleting 652c5eecc9da98011eeef402
// no return needed since we are deleting. it is setup to return a string saying successfully deleted

mutation {
  deleteProduct(id: "652c5eecc9da98011eeef402")
}

// return
{
  "data": {
    "deleteProduct": "Successfully deleted widget"
  }
}
```

## Aliases for labels - GraphQL out of the box allows you to make mutliple queries - using Aliases

- suppose you want specific info from one widget but different info for the second widget in your product line
- Example to better understand
- Alias the queries - you can use your own labels for the different queries you want to get

```js
// note that alias1 and alias2 could be called anything you needed. no requirement on the label - just called alias1 and alias2 for examples
query {
  alias1: getProduct(id: "652c68fb9283162495c69ce3") {
    name
    price
  }
  alias2: getProduct(id: "652c69919283162495c69ce6") {
    name
    description
  }
}

// return
{
  "data": {
    "alias1": {
      "name": "Widget16",
      "price": 34.89
    },
    "alias2": {
      "name": "Widget77",
      "description": "Widget(77)"
    }
  }
}

```

## Fragments - another GraphQL out of the box

- can be used to request the same information for multiple items
- example here is using alias names of widget1 and widget2 (these are alias labels not product names)
- just create the fragment of what info you need and then insert into the alias query with ...fragmentName

```js

query {
  widget1: getProduct(id: "652c68fb9283162495c69ce3") {
    ...widgetFragment
  }
  widget2: getProduct(id: "652c69919283162495c69ce6") {
    ...widgetFragment
  }
}

fragment widgetFragment on Product {
  name
  price
  description
  soldout
}


// return - will return same information from all requests in the query
{
  "data": {
    "widget1": {
      "name": "Widget16",
      "price": 34.89,
      "description": "Another widget (16) for your garden",
      "soldout": "ONSALE"
    },
    "widget2": {
      "name": "Widget77",
      "price": 34.89,
      "description": "Widget(77)",
      "soldout": "SOLDOUT"
    }
  }
}
```
