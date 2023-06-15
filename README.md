# UniQL: Universal Query Language

UniQL is a JSON-based, database-agnostic query language designed to provide a standardized way to query various types of databases, including SQL and NoSQL, without having to write database-specific queries. UniQL query language looks a lot like the MongoDB query language, but is extended and improved to support multiple database.

## Features

- JSON-based query format
- Database agnostic
- Supports multiple Programming languages (golang,java,kotlin,nodejs,rust,c#)
- Supports common query operations including filtering, sorting, projection, skipping, and limiting

## Query Structure

UniQL queries are structured as JSON objects, containing the following fields:

- `filter`: A JSON object specifying the conditions for the query.
- `sort`: A JSON object specifying the fields to sort by and the sort direction (ascending or descending).
- `projection`: A JSON object specifying which fields should be returned.
- `skip`: A numeric value indicating the number of documents or rows to skip.
- `limit`: A numeric value indicating the maximum number of documents or rows to return.

### Example Query

```json
{
  "filter": {
    "age": { "$gt": 25 },
    "address.city": "New York"
  },
  "sort": {
    "age": 1
  },
  "projection": {
    "name": 1,
    "age": 1
  },
  "skip": 5,
  "limit": 10
}
```

### Filter

Filtering supports the following operators:

```json
{
  "filter": {
    "age": { "$gt": 25 },
    "address.city": "New York"
  }
}
```

#### Comparison

- $eq: Matches values that are equal to a specified value.
- $gt: Matches values that are greater than a specified value.
- $gte: Matches values that are greater than or equal to a specified value.
- $in: Matches any of the values specified in an array.
- $lt: Matches values that are less than a specified value.
- $lte: Matches values that are less than or equal to a specified value.
- $ne: Matches all values that are not equal to a specified value.
- $nin: Matches none of the values specified in an array.

#### Logical

- $and: Joins query clauses with a logical AND returns all documents that match the conditions of both clauses.
- $not: Inverts the effect of a query expression and returns documents that do not match the query expression.
- $nor: Joins query clauses with a logical NOR returns all documents that fail to match both clauses.
- $or: Joins query clauses with a logical OR returns all documents that match the conditions of either clause.
- $exists: Matches documents that have the specified field.
- $type: Selects documents if a field is of the specified type.

### Sort

Sort direction can be specified with `1` for ascending and `-1` for descending.

```json
{
  "sort": {
    "age": 1,
    "address.city": -1
  }
}
```

### Projection

Projection allows to specefy what fields should be populated in te result

```json
{
  "projection": {
    "id": -1,
    "name": 1,
    "address.city": 1
  }
}
```

### Skip

To specify the number of documents to skip

```json
{
  "skip": 5
}
```

### Limit

To specify the maximum number of documents to return.

```json
{
  "limit": 10
}
```

### Usage

#### Golang

```golang
/*
type UniQLQuery struct {
	Filter     map[string]interface{} `json:"filter"`
	Sort       map[string]interface{} `json:"sort"`
	Projection map[string]interface{} `json:"projection"`
	Skip       int                    `json:"skip"`
	Limit      int                    `json:"limit"`
}

uniqlQuery := `{
  "filter": {
    "age": {"$gt": 25},
    "city": "New York"
  },
  "sort": {
    "age": 1
  },
  "projection": {
    "name": 1,
    "age": 1
  },
  "skip": 5,
  "limit": 10
}`
*/

func FetchResources(ctx context.Context, col *mongo.Collection, uniqlQuery UniQLQuery) ([]any, error) {
  opts := options.Find().
    SetProjection(uniqlQuery.ToMongoProjection())
    SetSort(uniqlQuery.ToMongoSort())
    SetSkip(uniqlQuery.ToMongoSkip())
    SetLimit(uniqlQuery.ToMongoLimit())

  cur, err := col.Find(ctx, uniqlQuery.ToMongoFilter())
  
  //...
}

```