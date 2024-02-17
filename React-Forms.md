# Welcome to React Forms

Ways to manage Form values
1. Manage form value by State
2. Extract values by refs
3. Use FormData and native browser features

## buttons
1. Its a default behaviour of buttons within a form to send a request to the server  and refresh the page. The default type is **submit**, so changing it to **button** can stop this submission.
2. The form can also trigger a submit on the **onSubmit** handler

## Managing Form data
1. The individual form element values can be stored in separate states. This will be difficult to maintain if the form has multiple data entry elements.
2. Alternate approach will be to use a bind state, where 1 single object will handle all the entered values.
3. Another approach is to use **useRef**, and it can be used on all HTML elements. It typically requires less code than refs. For it we need to create the ref using **useRef** hook. Connect it with the html using **ref={}**. and then extract the current value during form submission , for example "email.current.value"
4. Disadvantage is we cannot manipulate DOM value using ref and also multiple refs may make it cumbersome in bigger forms.
5. Third approach is to use the browser provided constructor object **FormData**
6. The **name** prop is mandatory in this approach for all the HtmlElements.
7. This method has 1 drawback when there is multiple checkbox assigned to 1 name property , hence the approach is
```
const form = new FormData(event.target);
const acqChannel= form.getAll("acquisition")
const data = Object.fromEntries(form.entries());
data.acquisition=acqChannel;
console.log(data);
```

## Resetting Form data

1. Button with type **reset** automatically resets the form entries.
2. Second approach is to programmatically set the state values to the default values after submission.
3. Third approach is to reset the value while using the **useRef** is not recommended to update the current value since it directly affects the DOM. Instead its better to follow the reset  as ```event.target.reset()```

## Form Validation

### Validation on Key Stroke

1. Forms input can be validated on every key stroke with its state value. But here the error message will be shown too early , also once the error value is set it cannot be reset.

### Validation on lost focus 

1. **onBlur** event will trigger once the input is touched and then the focus is lost. Validation at this stage will have the entire data as the input.
2. Here the validation can be made on whether the input field is touched.

### Validation on form submission 

1. Here the entire logic is validated upon submission and hence individual state can be mantained for error validation

###Validation by Built-In Validation props

1. Adding **required** to the html elements which are mandatory.
2. Adding **minlength** for Password length check.


