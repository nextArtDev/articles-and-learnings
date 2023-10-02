my toturial: <https://www.youtube.com/@Codevolution>
suggested by doc: <https://www.youtube.com/@bluebill1049/videos>

npm i "react-hook-form"

react-hook-form does not rerender the page after each key stroke but track it after each!

## useForm

```typescript
    const form = useForm()
```
One of the key concepts in React Hook Form is to register your component into the hook to make its value available for both the form validation and submission.
 - Managing data
 - Submit form data
 - Enforce validations and provide visual feedback

form states:
- Current value of every field in the form
- Weather a field has been interacted with ex: -> _values:{...}_
- weather a field value has changed -> ex: _visited:{...}_
- Weather the form is invalid -> ex: _isValid:{...}_
- Weather a field contains errors -> ex:  _errors:{...}_

to help manage form state, react hook form provides **register** that can be access on the form object
 we register a form control with react-hook-form by that, We can call this method and passing in string argument, the methods returns _four_ method we need to hook into the form control:
 - name
 - ref
 - onChange
 - onBlur
  
  when we populate a form element by this, automatically react-hook-form start tracking the state of it

```typescript
    const form = useForm()
    const {register} = form;

    const {name, ref , onChange , onBlur } = register("email");

    return(
        <form>
        // <input type="text" id="username" name="username" > // usu form
        <input type="text" id="username" name={name} ref={ref} onChange={onChange} onBlur={onBlur()} > 
        </form>
    )
```
instead of all this, we can just pass register props by passing the field name to it: **{...register("username")}**

```typescript
    const form = useForm()
    const {register} = form;

    return(
        <form>
        <input type="text" id="username" {...register("username")} > 
        </form>
    )
```

### Installing react-hook-form devTools
npm i -D @hookform/devtools

then we import it and invoke it, after populating the form with that by _control_ prop,and after the closing tag of react-hook-form we use it

```typescript
// 1.importing DevTool
    import {DevTool} from "@hookform/devtools"


    const form = useForm()
//2. populate form with that by "control prop"
    const {register , control} = form;

    return(
        <form>
        <input type="text" id="username" {...register("username")} > 
        </form>
//3. invoking DevTool
        <DevTool control={control}  />
    )
```

## form submission with react-hook-form

it takes 3 steps:

-Define a function that should be call when a submit button is pressed
const onSubmit = () =>{}

-Destructure the _handleSubmit_ function from the form function and listen to the form onSubmit event and pass the handleSubmit as a handler, to handle submit, pass the onSubmit function as the argument

const form = useForm();
const {handleSubmit} = form
<form onSubmit={handleSubmit(onSubmit)} >

by that handleSubmit automatically receives the data:
const onSubmit = (data) =>{console.log(data)} //username value will be printed

-Fix the Typescript type error by defining the type of data which we will receive and infer the type when we invoking the useForm hook too

type FormValues = {
    username: string
}

const form = useForm<FormValues>()
const onSubmit = (data: FormValues) =>{
    console.log(data)
}

**All the form submission**:

```typescript
//defining the type fpr the form inputs
type FormValues = {
    username: string
}

// inferring the type of form from type we crated
const form = useForm<FormValues>();

//Getting handleSubmit from useForm hook
const {handleSubmit} = form

//passing the type to the onSubmit data which we automatically get from handleSubmit after populate form with that
const onSubmit = (data: FormValues) =>{
    console.log(data)
}

//populating the form with onSubmit by handleSubmit which we get from useForm hook
<form onSubmit={handleSubmit(onSubmit)} >
//...

```
## Form validation
The built in validation is only onSubmit validation, and it just applies when form is submitted.
It supports various HTML validation rules like: _required_ _minLength_ _maxLength_ _min_ _max_ _pattern_

- Add **noValidate** to "form" element,
- Pass validation as a second object to the register attribute

```typescript
//Adding "noValidate" to the form
<form onSubmit={handleSubmit(onSubmit)} noValidate >
    <input
    type='text'
    id='username'
//Adding "required" and error message to the second argument of register function
    {...register("username"),{
        required:{
            value:true, 
            message:"Username is Required"
            }
        }}
    />
```
### _pattern_ validation
It's like a regular validation except it accepts a pattern (for example regex expression) ans message.

```typescript
//Adding "noValidate" to the form
<form onSubmit={handleSubmit(onSubmit)} noValidate >
    <input
    type='text'
    id='username'
//Adding "required" and error message to the second argument of register function
    {...register("password"),{
        patter:{
            value:/^[a-zA-Z0-9] //...
            message:"Invalid email format"
        }
    }}
    />
```
## Displaying Error message

_formState_ object inside "form" contains information like _error object_
we destructure **errors** from formState, It contains all the errors of all the field which have form validation.
```typescript
const form = useForm<FormValues>()
const {formState} = form;

const {errors} = formState;

//..
//Each field which has form validation has an error object
<p>{errors.username?.message}</p>
```

### Custom validation
What if for example we want to user not allowed to insert specific email like "admin@example.com" to the input?
To add a custom validation we add a key-value to the options object, passed to into the register function, the key is called _validate_ and this is going to be a function which automatically receives the fieldValue as an argument 

```typescript
<form onSubmit={handleSubmit(onSubmit)} noValidate >
    <input
    type='text'
    id='username'
    {...register("password"),{
        patter:{
            value:/^[a-zA-Z0-9] //...
            message:"Invalid email format"
        }
    },
    validate:(fieldValue)=>{
        return (
            //If it was true, show the error message
            fieldValue !== "admin@example.com" || "Enter a different email address"
        )
    }}
    />
```
If we want to add more custom validations, we create an object for _validate_ , ans pass a key for each custom error

```typescript
<form onSubmit={handleSubmit(onSubmit)} noValidate >
    <input
    type='text'
    id='username'
    {...register("password"),{
        patter:{
            value:/^[a-zA-Z0-9] //...
            message:"Invalid email format"
        }
    },
    validate:{
        notAdmin:(fieldValue)=>{
        return (
            fieldValue !== "admin@example.com" || "Enter a different email address"
        )
    },
    notBlackListed:(fieldValue)=>{
        return (
            !fieldValue.endWith("baddomain.com") || "This domain is not supported"
        )
    }}
    />
```
