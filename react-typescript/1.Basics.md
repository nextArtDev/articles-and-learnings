# Basics

## object is a valid type

This type can regard us to expect other primitive types for a variable
but of course its not a best choice

```typescript

let user:object;

user= 'max' // throw an error

// it should be for example
let user : {
    name:string,
    age:number
}
```

## Array type 

```typescript
let bobbies:Array<string> // can not be: hobbies=[1,2,3]
// Its equal to:
let hobbies:string[]
```

## Defining function types

Its like an array function but its not: `calcFn:(a:number, b:number) => number`

```typescript
function calculate(
    a:number,
    b:number,
    calcFn:(a:number, b:number) => number
){
    calcFn(a,b)
}

calculate(2, 5, add)
```

## Differences between types and interfaces

_type_ is for object types and other types like functions and always we can use it but we can not use _interface_ for (for example) union types.

- _interface_ is easily _extendable_ 
- in _interface_ when we use classes can use _implements_ and it means at least class should have interface properties

```typescript
class AuthCredentials implements Credentials {

}
```

## merging types

```typescript

// by using "type"
type AppAdmin = Admin & AppUser

// by using "interface"
interface AppAdmin extends Admin, AppUser{}
// Or it can be added
interface AppAdmin extends Admin, AppUser{
    doctor:string
}
```

## union types

```typescript
type Role = 'admin' | 'user' | 'editor';
```

## Generic type

`Array<Role>` is a built in generic type which is equal to: `Role[]`

we use it when we don't know the shape of the data, it could be string or object, so we got a placeholder for that type. now we can use this for multiple type of data.

```typescript
type DataStorage<T> = {
  storage: T[];
  add: (data: T) => void;
};

const textStorage: DataStorage<string> = {
  storage: [], // this now should be string array
  add(data) { // this data should be type string
    this.storage.push(data);
  },
};

const userStorage: DataStorage<User> = {
  storage: [],
  add(user) {},
};

// Tow placeholder generic functions 
function merge<T, U>(a: T, b: U) {
  return {
    ...a,
    ...b,
  };
}

// const user = merge<{name:string},{age:number}>({},{})

const newUser = merge(
  { name: 'Max' }, // Its T: {name:string}
  { age: 34 } // Its U: { age: 34 }
);
```

```typescript
```

```typescript
```

```typescript
```
