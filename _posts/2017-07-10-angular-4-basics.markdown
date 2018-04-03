---
layout: single
title:  "Angular 4 Basics"
date:   2017-07-10 11:10:25 +0200
categories: angular
tags: angular basics helloworld
published: false
header:
  teaser: /assets/images/common/angular-logo.png
---

This is absolute beginners guide for setting-up development environment for writing AngularJs 4 application. This post is intended to serve as quick-start guide for developing first Angular 4 project as well as quick reference for Angular basics.
Angular is a JavaScript framework which allows you to create reactive Single-Page-Applications (SPAs). Angular uses Typescript - a super-set to javascript and it offers more features like classes, Types, Interfaces, ...). With Typescript allows you to write more robust code because it is validated at development time and not at the runtime as javascript. Typescript doesn't run in browser, it is compiled to javascript before application runs. This compilation is done by the Angular CLI.

### Content

[Installing NodeJs](#installing-nodejs)

### Requirements

* [NodeJS](https://nodejs.org/en/download/) 
* [Angular CLI](https://cli.angular.io/) *(Not required but highly recommended.)*
* [Visual Studio Code](https://code.visualstudio.com/) (or any text editor)

### Angular CLI

Angular CLI will greatly simplify the process of:
Creating new Angular projects with default settings that are immediately ready to be served
Creating Angular components, routes, services and pipes
Serving your application locally while developing
Running your tests (unit and end-to-end)

#### Creating Angular projects using CLI

Angular projects are created using *new* keyword and specifying name of your project. Just open your command prompt window and run following commands:

```
ng new awesome-app-name
```
This will create new Angular project using default settings at your current location.

#### Creating Angular components, routes, services and pipes
You can take advantage of *generate* command to speed-up creation of Angular building blocks. While in root project location execute following commands:
```
// For creating a component
ng generate component foo
// or just 
ng g c foo
``` 
This will generate folder named *foo* with required html, css, ts and spec files:
* foo
    * foo.component.html
    * foo.component.ts
    * foo.component.css
    * foo.component.spec
 Additionally some code will be added to **app.module.ts** file in order to import newly created component:
 
 ```
 import { BrowserModule } from '@angular/core';
 import { NgModule } from '@angular/core';
 import { AppComponent } from '@angular/core';
 import { FooComponent } from './foo/foo.component';

 @NgModule({
     declarations: [
         AppComponent,
         FooComponent
     ],
     imports: [
         BrowserModule
     ],
     providers: [],
     bootstrap: [AppComponent]
 })
export class AppModule { }
 ```

### Components

Components are one of the key features of Angular. You generally build your application by composing it from number of components. Every application starts with app-component - a root component of any Angular application. It basically holds the entire application and we nest all our custom components to this component. Component is basically a TypeScript class, decorated with @Component decorator. Inside decorator you define components selector property and template (and style), for example:

```
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-foo',
  templateUrl: './foo.component.html',
  styleUrls: ['./foo.component.css']
})
export class FooComponent {
  
}
```

### Bindings

Data binding represents way of communication between TypeScript code as your business logic and Template which is visual representation - HTML page that user sees. There are several types of data binding in Angular and which one is used depends of the specific situation. Generally application would use all types of bindings and here they all are: 
String Interpolation ({{ data }}) - a one-way binding used when we want to output some information from our TypeScript code to our Template (HTML). It uses double curly braces with some property or expression in between. It is possible to bind to some TypeScript method or ternary expression as long as it **returns a string value**. It is not possible to write multi-line statements, for example if-else statements. 
```
// String Interpolation example in our HTML code
<p> Hello  { { userName } } </p>
<p> { { getGreeting() } } </p>

```
Where *userName* is some property and getGreeting is a method in our TypeScript code:
```
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-foo',
  templateUrl: './foo.component.html',
  styleUrls: ['./foo.component.css']
})
export class FooComponent {
  userName = "John";

  getGreeting(){
      return 'Good to see you!';
  }
}
```
Change to the *userName* property would automatically trigger change to the paragraph element by inserting value of *userName*.

Property Binding ([property]="data")
Also a one-way binding used to create binding between native HTML properties and our TypeScript code. It uses square brackets around property to bind to. For example it is possible to bind button's *disabled* property to our own property (or some method) in TypeScript. 

```
// Property binding to button's *disabled* property
<button [disabled]='!isEnabled' class='btn btn-primary'>Start</button>
```
And in TypeScript code:
```
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-foo',
  templateUrl: './foo.component.html',
  styleUrls: ['./foo.component.css']
})
export class FooComponent {
  isEnabled = false;

}
```
In this example button's *disabled* property will take value of *false* and will be disabled. If we change value of *isEnabled* property later in the code, *disabled* property would change accordingly. We could also use some method or an expression direct in HTML code, as long it resolves to Boolean value at the end (because *disabled* property expects a Boolean).

Event Binding 
With Event binding we can easily trigger our TypeScript functions when some event is fired on the page. The syntax to achieve event binding is as follows: ((event)='expression'). This is an example using button and it's onClick event:
```
<button (click)='clickHandler()' class='btn btn-primary'>Start</button>

```
Matching Typescript code:
```
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-foo',
  templateUrl: './foo.component.html',
  styleUrls: ['./foo.component.css']
})
export class FooComponent {
  
  clickHandler(){
    console.log("Button clicked!");
  }
}
```
It is possible to write some code directly in the html, instead of calling clickHandler() function, but putting to much of code logic in html is not so readable an it is bad practice. The MDN (Mozilla Developer Network) offers detailed lists of all properties and events of the html elements and it is good reference point.

Two-Way-Binding ([(ngModel)]="data")
This is a combination of two bindings: Property and event binding. It means that any change to TypeScript property will update html property that it is bound to and vice versa. If that is what you need, this is the way to achieve that:
```
// Binding to input element
<input type="text" class="from-control" 
[(ngModel)]="userName">
// We use String Interpolation here just to see live changes to userName property
<p>{ { userName } }</p>
``` 
While in our TypeScript we have:
```
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-foo',
  templateUrl: './foo.component.html',
  styleUrls: ['./foo.component.css']
})
export class FooComponent {
  userName = '';  
  
}
```
Now, when we type some text in the input box, paragraph text bellow is also updated. Any change to userName property would also updated text in the input box (hence the two-way binding).

### Directives
Directives are instructions to the DOM.

#### Built-in directives

There are many built-in directives in Angular and here are examples for some of them:
```
// ngIf directive - expects the expression which resolves to Boolean (* is required as directive changes the DOM)
<button *ngIf="buttonIsEnabled"> My Button</button>

// ngFor - iterates through array
<p *ngFor="let log in logList"> log </p>

// ngFor with index
<p *ngFor="let log in logList; let i = index"> log </p>

// ngStyle - expects an javascript object in for of key-value pairs 
// where key is style name and value is style value.
// In the example we are binding to ngStyle property of ngStyle directive
<p [ngStyle]="{backgroundColor: GetColor()}"> Some paragraph text </p>

// ngClass - expects an javascript object in for of key-value pairs 
// where key is a class name and value is Boolean value which determines  
// should class be applied to element or not.
<p [ngClass]="{myClass: true}"> Some random text. </p>


```

#### Custom directives


