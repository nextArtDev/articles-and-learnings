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