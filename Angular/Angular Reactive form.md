
### Angular form Fundamental

When we build a form with Angular, we always create a component class for the form logic and a template for the form's user interface. If the form updates data, the component class calls a service to retrieve existing data from a database or other data store on a server somewhere, and that data is displayed as default values on the form. 

### Angular provides two techniques for building forms: template‑driven and reactive, previously called model‑driven. 

#### Template Driven Forms
Template‑drive forms put the responsibility for the forms seperately in the template, which we define with basic HTML and data binding. 

1. The template‑driven technique is straightforward and easy to use, and it looks very familiar if you've built forms with Angular 1. 
2. Template‑driven forms rely heavily on two‑way data binding, we don't have to write any code to copy the data to the input elements or track the user's entries. It's all handled automatically. 
3. Angular also automatically tracks form and input element state. We can use this state information to determine when the form is invalid and disable the Save button, for example.


#### Reactive Forms
Reactive forms shift much of that responsibility to the component class. We still create the template with HTML, but we manage the data and validation in the component code. 

1. They handle more complex validation scenarios, such as changing validation based on a user's selection or on the form state. 
2. Reactive forms do not use data binding, so the form cannot mutate our data model. The component code controls how to handle any changes to the data. Some developers prefer this immutability. 

3. Using reactive forms makes it easier to perform an action on a value change, such as transforming the value to uppercase or performing a partial lookup. We have access to reactive transformations, such as debounceTime to delay reacting to user input and distinctUntilChanged to ignore values that are the same as previously processed values. 

4. Reactive forms make it easier to dynamically add input elements to the form, so we could allow the user to enter any number of addresses, for example.

5. And since more of the logic and validation are in the code, not the template, some developers find that the logic is easier to unit test. 

 

##### These states define whether the user has changed a value in an input element. 

Pristine - If the value is unchanged, the state of the input element is pristine. If all input elements on a form are pristine, then the form itself is pristine.

Dirty - Changed values have a state of dirty. If any input element on the form is dirty, then the form state is dirty.

Valid - A state and an array track validity. The input element state is valid if it passes all defined validation rules. The form itself is valid if all input elements on the form are valid. 

Validation errors are added to an errors collection. The key of each array element is the name of the validation rule associated with the error. 

##### These states define whether the user has visited an input element.

 Touched - A value of touched means the user has set focus into the input element and then left the input element.A form is touched if any input element has been touched.
 
 Untouched - Untouched is set if the user has not set focus, then left the input element. 
 
#### Form Building Blocks

Angular has a set of predefined form and input element states.These building blocks are used by both template‑driven and reactive forms to track state and values. 

 1. FormControl tracks the value and state of an individual input element, such as an input box. 
 
 2. FormGroup tracks the value and state of a group of FormControls.  A form itself is manages as a FormGroup, because what is a form but a group of input elements whose state and value we wish to track together?  But any group of input elements on a form can comprise a FormGroup. We could group the input elements for a mailing address block, for example. And FormGroups themselves can be nested within other FormGroups. 
 
##### What is a form model exactly?
A form model is the data structure that represents the HTML form. The structure of the form model reflects the form and input elements in the template. In most cases, each input element within the HTML form has a corresponding FormControl in the form model.

1. The form model retains form state, such as dirty or valid. 
2. It retains the user's entries in its value property. 
3. The form model also tracks all of the FormControls and nested FormGroups within the form, along with their state and value. 
Note- Don't confuse the form model with the data model that we use with data binding.

##### Rective Forms Proecss

1. Reactive forms shift the responsibility for creating the form model to the component class. 
We define the form model by creating the instances of the 
2. FormGroup and FormControl building blocks in our component class. We define the validation rules in the class. We can even handle display of validation error messages in the class. 
3. We manage the data for the form in the class.No data binding in the HTML. 
4. The class provides methods for form processing, such as handling the submit. 
5. We still define the visual parts of the form in the template, so we create the form element and input elements in the template. We then bind these input elements to the form model defined in the component class. So instead of binding the input elements to the data model properties directly, we bind to the form model we build in our component class.


##### Rective forms hel in Complex Scenarios 
1. Scenarios, such as dynamically adding input elements to the form, such as multiple address blocks.
2. Adding code to watch what the user types and reacting accordingly. 
3. Waiting validation until the user stops typing, providing different validation rules for different situations.
4. What if your team prefers working with immutable data, that means you need to forgo two‑way data binding. 

These scenarios are where the reactive forms approach really shines, as we'll see through the remainder of this course.


#### Steps for createing Reactive form

#### 1. Importing the ReactiveFormsModule

To work with reactive forms, you’ll be using the ReactiveFormsModule instead of the FormsModule, so import it in your app or feature module: 

```
//app.module.ts

import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { ReactiveFormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';

import { AppComponent } from './app.component';

@NgModule({
  // ...
  imports: [
    BrowserModule,
    ReactiveFormsModule,
    HttpModule
  ],
  // ...
})
export class AppModule { }
```

#### 2. The Component Class

We define the form model by creating the FormGroup and FormControl instances ourselves in our component class.

We then bind the template to the form model. This means that our form is not directly modifying our data model. So how do we create the form model? Every form model requires at least one FormGroup, which I call the root FormGroup. 
 
If a value if provided when newing a form control, it’ll be used as the initial value for the field.

Notice how the form group and form control names are the same that we used in our template, and how we initialize our form group in the ngOnInit lifecycle hook:

```
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormControl } from '@angular/forms';

@Component({
  // ...
})
export class AppComponent implements OnInit {

  //This property holds the reference to our form model.
  myForm: FormGroup;

  ngOnInit() {
    this.myForm = new FormGroup({
      name: new FormControl('Ashvin'),
      email: new FormControl(''),
      message: new FormControl('')
    });
  }

  onSubmit(form: FormGroup) {
    console.log('Valid?', form.valid); // true or false
    console.log('Name', form.value.name);
    console.log('Email', form.value.email);
    console.log('Message', form.value.message);
  }
}
```

#### 3. Template: formGroup and formControlName

1. formGroup: The form will be treated as a FormGroup in the component class, so the formGroup directive allows to give a name to the form group.

2. formControlName: Each form field should have a formControlName directive with a value that will be the name used in the component class.


With reactive forms the logic is declared entirely in the component class, so the template syntax is made very easy:

```
//app.component.html

<form [formGroup]="myForm" (ngSubmit)="onSubmit(myForm)">
  <input formControlName="name" placeholder="Your name">
  <input formControlName="email" placeholder="Your email">
  <input formControlName="message" placeholder="Your message">

  <button type="submit">Send</button>
</form>
```

#### 4. Validation

Adding validation to the form is very simple too. Just add the Validators class to your imports and declare your form controls with arrays instead of simple string values.

The first value in the array is the initial form value and the second value is for the validator(s) to use. Notice how multiple validators can be used on the same form control by wrapping them into an array:

```
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
  // ...
})
export class AppComponent implements OnInit {
  myForm: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit() {
  this.myForm = this.fb.group({
    name: ['Benedict', Validators.required],
    email: ['', [Validators.required, Validators.pattern('[a-z0-9.@]*')]],
    message: ['', [Validators.required, Validators.minLength(15)]]
  });
  }
}
```

##### Accessing Form Value & Validity in the Template

1. Using control name 
```
Syntex- formGroupName.controls.formControlName.porpety
Ex- myform.contorls.name.valid
```

2. Using formGroup get method
```
Syntex- formGroupName.get('formControlName').porpety
Ex- myform.get('name').valid
```

3. In component you can also access mode
```
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormControl } from '@angular/forms';

@Component({
  // ...
})
export class AppComponent implements OnInit {

  //This property holds the reference to our form model.
  myForm: FormGroup;
  
  //creae the refernce that used in template later ex name.valid
  name = new FormControl('Ashvin');
  email = FormControl('');
  message = FormControl('');

  ngOnInit() {
    this.myForm = new FormGroup({
      name: this.name,
      email: this.email,
      message: this.message
    });
  }

}
```

In our template we can access each form control’s value and validity as well as the value and validity of the whole form group as a whole.

The following showcases a few ways to get to values, validity and errors:

```
<form [formGroup]="myForm" (ngSubmit)="onSubmit(myForm)">
  <input formControlName="name" placeholder="Your name">

  <p>Your choosen name: {{ myForm.get('name').value }}</p>
  <div *ngIf="myForm.get('name').hasError('required')">
    Oops, please provide a name!
  </div>

  <input formControlName="email" placeholder="Your email">

  <div *ngIf="myForm.controls.email.hasError('pattern')">
    Oops, wrong pattern buddy!
  </div>

  <input formControlName="message" placeholder="Your message">

  <div *ngIf="myForm.controls.message.errors.minlength">
    Write something longer please!
  </div>

  <button type="submit" [disabled]="myForm.invalid">
    Send
  </button>
</form>
```
Notice how we can get to a control with either myForm.get(‘name’) or with myForm.controls.name, and we can get to errors with either .hasError(‘required’) or .errors.required. It’s a matter of preference which form you choose to use.

Notice also how be disable our submit button if our whole form is invalid.

##### Adjusting Validation Rules at Runtime

```
myControl.setValidators(Validators.requird)

myControl.setValidators([Validators.required, Validators.maxLength(30)]);

myControl.clearValidators();

//If we change the validator and we want 
//control to reevalueted based on new validators
myControl.updateValueAndValidity()
```

#### 5. Updating parts of the data model

When updating the value for a form group instance that contains multiple controls.There are two ways to update the model value:

1. Use the setValue() method to set a new value for an individual control. The setValue() method strictly adheres to the structure of the form group and replaces the entire value for the control.

2. Use the patchValue() method to replace any properties defined in the object that have changed in the form model.

Note: If we want to only set subset values of model then use patchValue. If need to set the values of each control of formGroup then use setValue.

```
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormControl } from '@angular/forms';

@Component({
  // ...
})
export class AppComponent implements OnInit {

  //This property holds the reference to our form model.
  myForm: FormGroup;
  
  //creae the refernce that used in template later ex name.valid
  name = new FormControl('Ashvin');
  email = FormControl('');
  message = FormControl('');

  ngOnInit() {
    this.myForm = new FormGroup({
      name: this.name,
      email: this.email,
      message: this.message
    });
  }

  updateAllFormValues(){
    this.myForm.setValue({
      name: 'ashvin',
      email: 'ashvin@domain.com',
      message: 'This is main information'
    });
  }

  setDefultMessage(){
    this.myForm.patchValue({
      message: 'This is defulat meesage'
    });
  }

}
```
#### 6. Using the FormBuilder service to generate controls

The FormBuilder service provides convenient methods for generating controls. Formbuilder create model form the configuration.

Use the following steps to take advantage of this service.

1. Import the FormBuilder class.
2. Inject the FormBuilder service.
3. Generate the form contents.

```
import { Component, OnInit } from '@angular/core';
//step 1
import { FormBuilder, FormGroup } from '@angular/forms';

@Component({
  // ...
})
export class AppComponent implements OnInit {
  myForm: FormGroup;

  //step2 - Inject formBuilder service
  constructor(private fb: FormBuilder) {}

  ngOnInit() {
    //configure the form
    this.myForm = this.fb.group({
      name: 'Smith',
      email: '',
      message: ''
    });

    //way2 
    this.myForm = this.fb.group({
      name: {value: 'Ashvin'},
      email: {value:''},
      message: {value:'This is defult message', disabled:true}
    });

    //way3 
    this.myForm = this.fb.group({
      name: [''],
      email: [''],
      message: [{value:'This is defult message', disabled:true}]
    });


  }

  // ...
}
```

#### 6. Custom validators

Since the built-in validators won't always match the exact use case of your application, sometimes you'll want to create a custom validator.

We define a custom validation rule with a custom validator. In its simplest form, a validator is a function. 

1. The validator function always take one parameter, the FormControl or FormGroup being validated. To allow passing in either a FormControl or a FormGroup, we type the parameter using the abstract class, AbstractControl.

2. The validator function returns an object defining the broken rule or null if it is valid. The returned object is comprised of a key and value pair where the key is a string and the value is a Boolean. In the custom validator function, we perform whatever logic we desire. If our logic determines that the validation rule passes, we return a null. If the validation rule fails, we return an object.

3. The key is a string and defines the name of the broken validation rule. The value is set to true to indicate that the current entry has an error. The broken validation rule is then added to the passed in FormControl or FormGroup error collection. 

```
import { FormBuilder, AbstractControl } from '@angular/forms';
import { Component, OnInit } from "@angular/core";

@Component({
  selector: 'reactive-form',
  templateUrl: './reactive-form.component.html'
})
export class AppReactiveForm implements OnInit {
  myForm: FormGroup;

  constructor(
    private fb: FormBuilder
  ) {}

  ngOnInit() {
    this.myForm = this.fb.group({
      phone: ['', [ValidatePhone]] // added the function in validators array of form-control
    });
  }
}

function ValidatePhone(control: AbstractControl): {[key: string]: any} | null  {
  if (control.value && control.value.length != 10) {
    return { 'phoneNumberInvalid': true };
  }
  return null;
}
```

As above, we have to provide the following implementation method: validate(control: AbstractControl): : {[key: string]: any} | null. This validator will return an object if the validation is not passed which is { 'phoneNumberInvalid': true } and will return null if the validation is passed.

In template
```
<div class="form-group col-sm-4">
  <label for="">Phone</label>
  <input type="text"  formControlName="phone" >  

  <span class="invalid-feedback" *ngIf="(myForm.controls.phone.touched || myForm.controls.phone.touched.dirty) && myForm.controls.phone.errors?.phoneNumberInvalid"> 
  <!-- Checked the errors property contains the 'phoneNumberInvalid' property or not which is returned by the validation function -->
      Phone number must be of 10 digit
  </span>
</div>
```

##### Custom validators with parameter

The validator function we wrote works well for any simple validation, but what if we need parameters? To define a custom validator with parameters, we need a more complex function that returns a validator function.

A validator function can only take one parameter, the AbstractControl to validate, we can't simply pass more parameters to this function. 

Instead, we build a factory function that returns the validator function. Here we have our factory function. We can specify any number and type of parameters here. This factory function returns a validator function, so we define ValidatorFn as the return type and return our validator function. This is the same validator function that we write above, but instead of declaring and naming this function, we simply return it using the arrow function syntax.

```
import { ValidatorFn } from '@angular/forms';

//factory function that reaturn validatorFn
function ratingRange(min: number, max: number): ValidatorFn {
  
  return (c: AbstractControl): { [key: string]: boolean } | null => {
    if (c.value !== null && (isNaN(c.value) || c.value < min || c.value > max)) {
      return { 'range': true };
    }
    return null;
  };
}
```

#### Reacting to Changes

we explore how to watch for user changes and react to those changes dynamically controlling the form. What if we could watch every change that the user makes? Every character? Every keystroke? Yeah, that might sound a bit creepy, but in reactive forms, it's a good thing, really. 

By watching the user's changes, we can modify the display, the validation, or the messages, providing a much more dynamic and personal experience. 