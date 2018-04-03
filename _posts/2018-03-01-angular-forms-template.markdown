---
layout: single
title:  "Angular Forms - The Template Approach"
date:   2018-03-01 16:27:25 +0100
categories: angular
tags: angular forms template
published: true
comments: true
header:
  teaser: /assets/images/common/angular-logo.png
---

It is absolutely possible to create form and handle user input in the HTML, why would we need Angular's help to do this? Problem lies in the fact that we usually end up with submitting form's data to the server using the submit button. Due to the fact that we are creating SPA with help of the Angular framework and therefore there no "submitting", we need a different approach. Instead of the submitting, we can use Angular's Http Module to send form's data back and forth between our application and some backend server. Additionally, Angular helps us with this task by providing lot of functionality one might need when dealing with forms. Some of this functionality we get out-of-box with Angular are: easy access to form's data, user input validation, ability to conditionally change how form is displayed and what is visible or enabled and much more. 
There are two approaches to developing forms in Angular: Template and Reactive. This post will cover some of the basics of the Template approach through an example of creating simple pizza ordering form ('cuz everyone likes pizza!).

## Content

[Creating Simple Form](#creating-simple-form)

[Accessing Forms Data From Typescript](#accessing-forms-data-from-typescript)

[Using Select Element](#using-select-element)

[Using Radio Buttons](#using-radio-buttons)

[Using Checkbox Element](#using-checkbox-element)

[Adding Forms Validation](#adding-forms-validation)

[Grouping Forms Data](#grouping-forms-data)

[Default Values And Data Binding](#default-values-and-data-binding)

[Setting And Patching The Data](#setting-and-patching-the-data)

[Summary](#summary)

## Creating Simple Form

Lets start with a simple form, which has one input element and a submit button:  
  
![pizza-form-basic.png]({{ "/assets/images/2018-03-01-angular-forms/pizza-gui-basic.png" | relative_url }}){:style="border: 1px solid #ddd;"}

Let's create some basic HTML for demonstration purposes:

```html
<div>
  <form>
    <div class="container">
      <div class="row justify-content-md-center">
        <div class="col-md-6 ">
          <h1>Welcome to our pizza place!</h1>
          <hr />
        </div>
      </div>
      <div class="row">
        <div class="col-md-6">
          <h3>Create your pizza!</h3>
          <hr />
          <div class="form-group">
            <label for="name">Pizza Name</label>
            <input type="text" class="form-control" id="name">
          </div>
          <button class="btn btn-primary" type="submit" >Order Now</button>
        </div>
      </div>
    </div>
  </form>
</div>
```

## Accessing Forms Data From Typescript

Now, lets modify this form using the Angular syntax: 

```html
<div>
  <form (ngSubmit)="onSubmit(f)" #f="ngForm">
    <div class="container">
      <div class="row justify-content-md-center">
        <div class="col-md-6 ">
          <h1>Welcome to our pizza place!</h1>
          <hr />
        </div>
      </div>
      <div class="row">
        <div class="col-md-6">
          <h3>Create your pizza!</h3>
          <hr />
          <div class="form-group">
            <label for="name">Pizza Name</label>
            <input type="text" class="form-control" id="name" name="name" ngModel>
          </div>
          <button class="btn btn-primary" type="submit" >Order Now</button>
        </div>
      </div>
    </div>
  </form>
</div>
```

Here is the appropriate typescript to go with out form:

```ts
import { Component } from '@angular/core';
import { NgForm } from '@angular/forms';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  onSubmit(form: NgForm) {
    console.log('Value of a name variable is: ' + form.value.name);
  }
}

```

When we submit this form, we can see following output in the console as expected:

![pizza-console-basic.png]({{ "/assets/images/2018-03-01-angular-forms/pizza-console-basic.png" | relative_url }}){:style="border: 1px solid #ddd;"}

But to understand how this works, let examine what Angular exactly did for us here. We have our HTML form element, but it has some additional Angular syntax:  
`<form (ngSubmit)="onSubmit(f)" #pizzaForm="ngForm">`  
The `ngSubmit` is a directive which specifies the function to be executed when the form is submitted. In the example, we have specified that onSubmit function should be executed and this function is defined in our typescript.  
What is interesting is that we were able to pass parameter f to this function, which is of type NgForm. NgForm is another directive, and we can export a directive into a local template variable like: `#pizzaForm="ngForm"`.  

But this is just one piece of the puzzle.

`<input type="text" class="form-control" id="name" name="name" ngModel>`  
To finally get access to form values, we need to use another directive: `ngModel`, to tell Angular in what elements of the form are we interested in. Also, we need to give a name to our element using the name attribute, and this name we will also use when accessing the value of the element in our typescript code.  
Finally we are able to get value from the input element as follows:  
  
`console.log('Value of a name variable is: ' + form.value.name);`  
  
We didn't have to pass `ngForm` object to the onSubmit function. Another approach would be to use Angular's ViewChild decorator and store our form data in the variable we define in our typescript class like:  
  
```ts
import { Component, ViewChild } from '@angular/core';
import { NgForm } from '@angular/forms';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  @VeiwChild('pizzaForm') pizzaForm: NgForm;

  onSubmit() {
    console.log('Value of a name variable is: ' + pizzaForm.value.name);
  }
}
```
Don't forget to include ViewChild form `@angular/core`!

## Using Select Element

Lets add select element to our form to be able to choose pizza size:  
  
![pizza-gui-select.png]({{ "/assets/images/2018-03-01-angular-forms/pizza-gui-select.png" | relative_url }}){:style="border: 1px solid #ddd;"}

Here is the HTML for the above example:
```html
<div>
  <form (ngSubmit)="onSubmit(f)" #f="ngForm">
    <div class="container">
      <div class="row justify-content-md-center">
        <div class="col-md-6 ">
          <h1>Welcome to our pizza place!</h1>
          <hr />
        </div>
      </div>
      <div class="row">
        <div class="col-md-6">
          <h3>Create your own pizza!</h3>
          <hr />
          <div class="form-group">
            <label for="name">Pizza Name</label>
            <input type="text" class="form-control" id="name" name="name" ngModel>
          </div>
          <div class="form-group">
            <label for="pizza-size">Choose Size</label>
            <select id="pizza-size" class="form-control" name="pizzaSize" ngModel>
              <option value="small">Small</option>
              <option value="medium">Medium</option>
              <option value="large">Large</option>
            </select>
          </div>
          <button class="btn btn-primary" type="submit">Order Now</button>
        </div>
      </div>
    </div>
  </form>
</div>
```
And the appropriate typescript code:
```ts
import { Component } from '@angular/core';
import { NgForm } from '@angular/forms';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {

  onSubmit(form: NgForm) {
    console.log('Chosen pizza size option is: ' + form.value.pizzaSize);
  }

}
```
We have manually selected _medium_ option for the pizza size, and successfully accessed this value using the same techniques as with the input element and the console shows the value:

![pizza-console-select.png]({{ "/assets/images/2018-03-01-angular-forms/pizza-console-select.png" | relative_url }}){:style="border: 1px solid #ddd;"}

## Using Radio Buttons

Lets add some radio buttons to be able to choose desired ketchup type:  
  
![pizza-gui-radio.png]({{ "/assets/images/2018-03-01-angular-forms/pizza-gui-radio.png" | relative_url }}){:style="border: 1px solid #ddd;"}

And after adding code for this to out HTML, it looks like this:
```html
<div>
  <form (ngSubmit)="onSubmit(f)" #f="ngForm">
    <div class="container">
      <div class="row justify-content-md-center">
        <div class="col-md-6 ">
          <h1>Welcome to our pizza place!</h1>
          <hr />
        </div>
      </div>
      <div class="row">
        <div class="col-md-6">
          <h3>Create your own pizza!</h3>
          <hr />
          <div class="form-group">
            <label for="name">Pizza Name</label>
            <input type="text" class="form-control" id="name" name="name" ngModel>
          </div>
          <div class="form-group">
            <label for="pizza-size">Choose Size</label>
            <select id="pizza-size" class="form-control" name="pizzaSize" ngModel>
              <option value="small">Small</option>
              <option value="medium">Medium</option>
              <option value="large">Large</option>
            </select>
          </div>
          <div class="form-group">
            <strong>Katchup Type</strong>
            <div class="radio" *ngFor="let katchupType of katchupTypes">
              <label for="katchupType">
                <input type="radio" id="katchupType" [value]="katchupType" name="katchup" ngModel> {{ katchupType }}
              </label>
            </div>
          </div>
          <button class="btn btn-primary" type="submit">Order Now</button>
        </div>
      </div>
    </div>
  </form>
</div>
```
Also, we have to add ketchup types array to out typescript code:
```ts
import { Component } from '@angular/core';
import { NgForm } from '@angular/forms';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  ketchupTypes = ['No Katchup', 'Mild', 'Hot'];

  onSubmit(form: NgForm) {
    console.log('Chosen kethup type is: ' + form.value.ketchup);
  }

}
```

After submitting the form, we get the following output in the console:  
  
![pizza-console-radio.png]({{ "/assets/images/2018-03-01-angular-forms/pizza-console-radio.png" | relative_url }}){:style="border: 1px solid #ddd;"}

## Using Checkbox Element

A checkbox is another common element we use in the forms, so let us add some to our application. We will use checkboxes to select ingredients for our pizza.
  
![pizza-gui-checkbox.png]({{ "/assets/images/2018-03-01-angular-forms/pizza-gui-checkbox.png" | relative_url }}){:style="border: 1px solid #ddd;"}

Lets see the HTML:

```html
<div>
  <form (ngSubmit)="onSubmit(f)" #f="ngForm">
    <div class="container">
      <div class="row justify-content-md-center">
        <div class="col-md-6 ">
          <h1>Welcome to our pizza place!</h1>
          <hr />
        </div>
      </div>
      <div class="row">
        <div class="col-md-6">
          <h3>Create your own pizza!</h3>
          <hr />
          <div class="form-group">
            <label for="name">Pizza Name</label>
            <input type="text" class="form-control" id="name" name="name" ngModel>
          </div>
          <div class="form-group">
            <label for="pizza-size">Choose Size</label>
            <select id="pizza-size" class="form-control" name="pizzaSize" ngModel>
              <option value="small">Small</option>
              <option value="medium">Medium</option>
              <option value="large">Large</option>
            </select>
          </div>
          <div class="form-group">
            <strong>Katchup Type</strong>
            <div class="radio" *ngFor="let ketchupType of ketchupTypes">
              <label for="ketchupType">
                <input type="radio" id="ketchupType" [value]="ketchupType" name="ketchup" ngModel> {{ ketchupType }}
              </label>
            </div>
          </div>
          <div class="form-group">
            <strong>Ingredients</strong>
            <div class="checkbox">
              <label>
                <input type="checkbox" name="cheese" ngModel>Cheese</label>
            </div>
            <div class="checkbox">
              <label>
                <input type="checkbox" name="ham" ngModel>Ham</label>
            </div>
            <div class="checkbox">
              <label>
                <input type="checkbox" name="egg" ngModel>Egg</label>
            </div>
            <div class="checkbox">
              <label>
                <input type="checkbox" name="mushroom" ngModel>Mushroom</label>
            </div>
            <div class="checkbox">
              <label>
                <input type="checkbox" name="paprika" ngModel>Paprika</label>
            </div>
            <div class="checkbox">
              <label>
                <input type="checkbox" name="olives" ngModel>Olives</label>
            </div>
          </div>
          <button class="btn btn-primary" type="submit" >Order Now</button>
        </div>
      </div>
    </div>
  </form>
</div>
```

And the typescript:

```ts
import { Component } from '@angular/core';
import { NgForm } from '@angular/forms';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  ketchupTypes = ['No Katchup', 'Mild', 'Hot'];

  onSubmit(form: NgForm) {
    console.log(form);
  }
}
```

We have created a checkbox for all of our ingredients and added a name and `ngModel` to each one of them.
Now, when the form is submitted, we can see that all of our checkboxes in the console, and see that proper ingredients are selected:  
  
![pizza-console-checkbox.png]({{ "/assets/images/2018-03-01-angular-forms/pizza-console-checkbox.png" | relative_url }}){:style="border: 1px solid #ddd;"}

## Adding Forms Validation

So far we only used _ngForm_ object to access value property, but there is a lot more to it. We can also use this object for the form validation purposes.
Let us say we want to make our pizza name input field mandatory. Here is some code which demonstrates how to achieve this with help of Angular.

```html
<!--Rest of the code omitted for clarity...-->
<div class="form-group">
  <label for="name">Pizza Name</label>
  <input type="text" class="form-control" id="name" name="name" ngModel required #nameInput="ngModel">
  <p *ngIf="nameInput.invalid && (nameInput.dirty || nameInput.touched)" 
  class="text-danger">Please give Your pizza a name.</p>
</div>
<!--Rest of the code omitted for clarity...-->
<button class="btn btn-primary" type="submit" [disabled]="f.valid">Order Now</button>
```
  
We have added `required` validation attribute to the name input element.
In order to demonstrate forms validation, we are also disabling our _submit_ button if the form is not valid. In addition to that, it would be nice to tell the user what is wrong with the form's data. We can do this by using already existing `ng-invalid` class.  
  
```css
input.ng-invalid.ng-touched {
  border: 1px solid lightcoral;
  border-left: 4px solid lightcoral;
}
```
In our css code, we intentionally applied this _ng-invalid_ style only to the input element, but we can do the similar thing for other elements too.  
In order to provide more details to the user about the validation error, we will also display some text, explaining what is wrong with the form, if the input is not valid. The `ngIf` directive is the perfect candidate to help us with achieving this, so we have added it to the paragraph.
Now, when we don't enter a name of our pizza, it is not possible to submit the form, and name input field is marked with red border.  
  
> NOTE: Why use `touched` and `dirty`? Usually, we don't want to display that form is invalid before the user even had a chance of editing the form. The `touched` property tells us exactly that - if the field was touched or not. Another useful property is `dirty`, which tells us if the value of the field was changed. Similarly, we use `ng-touch` and `ng-dirty` in or css.
  

Let's see how our form looks now, with input validation added:  
  
![pizza-gui-validation.png]({{ "/assets/images/2018-03-01-angular-forms/pizza-gui-validation.png" | relative_url }}){:style="border: 1px solid #ddd;"}

## Grouping Forms Data

It is possible to group several elements in a group in order to have some structure in our application, which is helpful in larger forms. It also helps with the validation, giving us the possibility to check validation of a group of elements instead of entire form.
We can create a group by placing `ngModelGroup` directive in a wrapping div element for example. `ngModelGroup` needs a name, so we can reference it later in  our code, and here is the example:

```html
<!-- Div element wrapping some controls-->
<div ngModelGroup="myGroup">
  <!--Add elements to group here-->
</div>
```

## Default Values And Data Binding

So far we only used ngModel to tell Angular what HTML elements we want to have in our form. We can also use property binding to set initial data of our elements. Let's set initial values for our __pizza size__ and __ketchup type__. We would chang out HTML as follows:

```html
<div class="form-group">
<label for="pizza-size">Choose Size</label>
<select id="pizza-size" class="form-control" name="pizzaSize" [ngModel]="'small'">
  <option value="small">Small</option>
  <option value="medium">Medium</option>
  <option value="large">Large</option>
</select>
</div>
<div class="form-group">
<strong>Katchup Type</strong>
<div class="radio" *ngFor="let ketchupType of ketchupTypes">
  <label for="ketchupType">
    <input type="radio" id="ketchupType" [value]="ketchupType" name="ketchup" 
    [ngModel]="ketchupTypes[0]"> {{ ketchupType }}
  </label>
</div>
</div>
```
Now we have value _Small_ chosen for the pizza size and _No Ketchup_ chosen for the ketchup type as a default.  
  
Let's see how our form looks now, with input validation added:  
  
![pizza-gui-binding.png]({{ "/assets/images/2018-03-01-angular-forms/pizza-gui-binding.png" | relative_url }}){:style="border: 1px solid #ddd;"}

We could also use two-way binding if required, by defining some property in our typescript and bind it using two-way binding syntax aka _banana in the box [()]_, for example: `[(ngModel)]="someCustomProperty"`.

## Setting And Patching The Data

Yet another way of setting the form's data is by using our NgForm object and it's setValue function. We need to pass javascript object when calling this function, exactly representing our form data. To demonstrate this, let' add _Generate_ button that we can use to populate our form with some hardcoded data.
  
```html
<!--Code ommited for clarity... -->
<div class="name-input">
  <label for="name-input">Pizza Name</label>
  <div class="input-group" id="yes">
    <input type="text" class="form-control" id="name" name="name" ngModel required #nameInput="ngModel">
    <span class="input-group-btn">
      <button class="btn btn-default" type="button" (click)="onGenerate(f)">Generate</button>
    </span>
  </div>
  <p *ngIf="nameInput.invalid && (nameInput.dirty || nameInput.touched)" class="text-danger">Please give Your pizza a name.</p>
</div>
<!--Code ommited for clarity... -->
```

Typescript for this looks like:
```ts
import { Component, ViewChild } from '@angular/core';
import { NgForm } from '@angular/forms';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  defaultName = 'Awesome Pizza';
  ketchupTypes = ['No Katchup', 'Mild', 'Hot'];
  cheese = false;
  ham = false;
  egg = false;
  mushroom = false;
  paprika = false;
  olives = false;

  onSubmit(pizzaForm: NgForm) {
    console.log(pizzaForm.value);
  }

  onGenerate(pizzaForm: NgForm) {
    pizzaForm.setValue({
      name: this.defaultName,
      ketchup: 'Hot',
      pizzaSize: 'large',
      cheese: true,
      egg: true,
      ham: true,
      olives: true,
      paprika: false,
      mushroom: false
    });
  }
}
```
We are passing our NgForm object to onGenerate method, just like we did with the _Submit_ button. Then, in the typescript code, we are calling setValue function and passing javascript object with some random values.
After pressing _Generate_ button, the form looks like this:
  
![pizza-gui-generate.png]({{ "/assets/images/2018-03-01-angular-forms/pizza-gui-generate.png" | relative_url }}){:style="border: 1px solid #ddd;"}

Of course, it is possible to achieve the same result by using property binding, but it is nice to know this feature as well.
If we don't want to populate all the elements of the form, we could use patchValue function instead. Then we would only pass to the function those properties we want to be updated - only the name in the following example:

```ts
pizzaForm.form.patchValue({
      name: this.defaultName
    });
```

## Summary

In the examples shown in this post we used some of the features the Angular is providing in order to make our lives easier when dealing with HTML form's data. This can be enough to get started, but as there is a lot more to Angular Forms, I can recommend visiting the official documentation by following this [link](https://angular.io/guide/forms).

Thanks for reading and feel free to check out other articles on the Code Freeze!