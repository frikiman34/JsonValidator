# SimpleJsonValidator
[![BCH compliance](https://bettercodehub.com/edge/badge/frikiman34/SimpleJsonValidator?branch=master)](https://bettercodehub.com/)

Json Validator for NodeJS with simple ruby-like syntax

## Installation
You can install this library using npm:

`npm i --save simplejsonvalidator`

## Usage

First, you need to create a new JsonValidator instance calling to the exported function:

```javascript
const jsonValidator = require('simplejsonvalidator');
const validator = jsonValidator();
```

Then, you can create validators using the 'create' method, which accepts a callback and the validator name

```javascript
validator.create(t => ({
  user: t.string.required,  
  age: t.number,
  text: t.string.max(10).required,
}), 'validatorName');
```

or you can omit the validator name and assign the returning validator to a variable:

```javascript
const myValidator = validator.create(t => ({
  user: t.string.required,  
  age: t.number,
  text: t.string.max(10).required,
}));
```

Finally, you can validate any json using the 'validate' function:

```javascript
const json = {
  user: 'user',  
  age: 22,
  text: '123456789',
};
myValidator.validate(json);
```

You can also nest objects like this:

```javascript
const demoValidator = validator.create(t => ({
  user: t.string.required,
  age: t.number,
  text: t.string.max(10).required,
  data: {
    token: t.string,
  },
}));
```

And you can check why a json is invalid:

```javascript
const errors = demoValidator.errors();
```

Errors look like these:

```javascript
[
  {
    message: 'element "key" must be string',
    type: 'string',
  },
  {
    message: 'key "user.name" should be upper than 10',
    type: 'string',
  },
  {
    message: 'key "user.age" should be positive',
    type: 'number',
  },
  {
    message: 'element "user.connection" must be date',
    type: 'date',
  },
]
```

## Types
### String
Validates if type is string. Example:

```javascript
validator.create(t => ({
  key: t.string,
}));
```

You can use these validators in string type:

| Validator     | Explaination                 | Example                                   |
| ------------- | ---------------------------- |:-----------------------------------------:|
| required      | makes key required           | t.string.required                         |
| shouldBe      | checks if the values matches | t.string.shouldBe('apples', 'oranges')    |
| max(number)   | maximum lenght limit         | t.string.max(10)                          |
| min(number)   | minimum lenght limit         | t.string.min(1)                           |
| matches(regex)| tests if string matches regex| t.string.matches(/Regex/)                 |

### Number
Validates if type is number. Example:

```javascript
validator.create(t => ({
  key: t.number,
}));
```

You can use these validators in number type:

| Validator     | Explaination                  | Example                   |
| ------------- | ---------------------------- |:-------------------------:|
| required      | makes key required           | t.number.required         |
| shouldBe      | checks if the values matches | t.number.shouldBe(22, 21) |
| positive      | checks if number is positive | t.number.positive         |
| negative      | checks if number is negative | t.number.negative         |

### Boolean
Validates if type is boolean. Example:

```javascript
validator.create(t => ({
  key: t.boolean,
}));
```

You can use these validators in boolean type:

| Validator     | Explaination                  | Example                   |
| ------------- | ---------------------------- |:-------------------------:|
| required      | makes key required           | t.boolean.required        |
| shouldBe      | checks if the value matches  | t.boolean.shouldBe(false) |

### Array
Validates if type is array. Example:

```javascript
validator.create(t => ({
  key: t.array,
}));
```

You can use these validators in array type:

| Validator             | Explaination                                              | Example                               |
| --------------------- | -------------------------------------------------------- |:-------------------------------------:|
| required              | makes key required                                       | t.array.required                      |
| shouldBe              | checks if the values matches                             | t.array.shouldBe([22, 21], [1, 'hi']) |
| exactLength(number)   | check if array length is exactly the specified length    | t.array.exactLength(20)               |
| lengthLowerTo(number) | check if array length is lower to the specified length   | t.array.lengthLowerTo(9)              |
| lengthUpperTo(number) | check if array length is upper to the specified length   | t.array.lengthUpperTo(1)              |
| notEmpty              | check if array legnth is not empty                       | t.array.notEmpty                      |

### Date
Validates if type is date. Example:

```javascript
validator.create(t => ({
  key: t.date,
}));
```

You can use these validators in date type:

| Validator         | Explaination                                | Example                              |
| ----------------- | ------------------------------------------ |:------------------------------------:|
| required          | makes key required                         | t.date.required                      |
| shouldBe          | checks if the values matches               | t.date.shouldBe(new Date())          |
| beforeDate(Date)  | check if the date is before desired date   | t.date.beforeDate(new Date())        |
| afterDate(Date)   | check if the date is after desired date    | t.date.afterDate(new Date())         |


## Express Framework Integration

You can use an express middleware to check jsons. You can do it calling to createMiddleware function
to create a middleware:

```javascript
const myValidator = validator.create(t => ({
  user: t.string.required,  
  age: t.number,
  text: t.string.max(10).required,
}));

app.post('/', validator.createMiddleware(myValidator), (req, res) => res.send(req.body););
```

The middleware, when a json is not valid, returns found errors.
You can customize the returning status code:

```javascript
const middleware = validator.createMiddleware(myValidator, 401);
```
