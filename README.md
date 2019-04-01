# LazyAppTemplate

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 7.3.3.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `--prod` flag for a production build.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI README](https://github.com/angular/angular-cli/blob/master/README.md).

Angular Module
This modularity in Angular it’s called NgModule. Each application is composed of at least one NgModule class which is the root module of the application. The root module is called AppModule by default and is located in src/app/app.module.ts file. The application runs through the Bootstrapping process of the root module.

Do not confuse Angular Module with JavaScript Module.

For creating an Angular Module we must use the class decorator @NgModule , this decorator uses a metadata object with properties that define the module. The main properties are:

imports: array with other modules that are used by components it this module.
declarations: receives an array of components that are part of the module.
exports: define an array of components, directives and pipes which will be used by other modules.
providers: declares the services, if it is the root module, the services are available for the entire application.
Example of a basic root module:

import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule
  ],
  providers: [], //Os serviços declarados na propriedade providers do módulo root ficam disponíveis em toda a aplicação
  bootstrap: [AppComponent] //somente o módulo root define um bootstrap
})
export class AppModule { }


In this sample code, we have turned the AppModule class into an Angular Module just by using the @NgModule decorator.

Lazy Loading
One advantage of splitting the application into modules is the ability to load certain modules only when needed. When using Lazy Loading modules, loading is only done when the use navigates to the route of the respective module.

Let’s see the operation of Lazy Loading in practice, using the @angular/cli we will create a new application:

ng new lazy-app --routing
With this command we create the entire application structure, the “routing” parameter causes @angular/cli to also create the application route file.

Edit the app.component.html template file to include the navigation links on the home page:

<nav>
  <a routerLink="home">Home Component</a>
  <a routerLink="lazy">Lazy Module</a>
</nav>
<router-outlet></router-outlet>


Our application does not have a defined style, we will only increase the font and add a spacing between navigation links by editing the app.component.css file:

a {    
    text-align: center;
    padding: 14px;    
    font-size: 17px;    
}


Let’s create the Home component that will be loaded at startup and called by the default route:

ng generate component home
After generating the Home component we will add the routes in app-routing.module.ts file:

import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { HomeComponent } from './home/home.component';
const routes: Routes = [
  {path: '', redirectTo: 'home', pathMatch: 'full'},
  {path: 'home', component: HomeComponent}
];
@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }


The first definition redirects the default route to the home route, the second call the Home component. With these changes, after the Hot Reloading the Home component will load on the home page:


Creating the Lazy Module
Let’s create a new module called Lazy:

ng generate module lazy --routing
The @angular/cli generates a new module and its respective routing file:


Next, we will create a component that will be part of the Lazy module. We will navigate to the module folder and execute the component creation command:

cd src/app/lazy
ng generate component sobre

Inside the module folder when executing the command causes @angular/cli to automatically import the component created in the lazy.module.ts file:

import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { LazyRoutingModule } from './lazy-routing.module';
import { SobreComponent } from './sobre/sobre.component';
@NgModule({
  imports: [
    CommonModule,
    LazyRoutingModule
  ],
  declarations: [SobreComponent]
})
export class LazyModule { }


The newly created component has automatically imported by @angular/cli in the declarations property.

The next step is edit the app-routing.module.ts file to add the routing to the module:

import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { HomeComponent } from './home/home.component';
const routes: Routes = [
  {path: '', redirectTo: 'home', pathMatch: 'full'},
  {path: 'home', component: HomeComponent},
  {path: 'lazy', loadChildren: './lazy/lazy.module#LazyModule'}
];
@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }


We create a new route called lazy, which uses the loadChild property to load the module. This way the Lazy module will only be loaded when the lazy route is triggered by the user.

To load the About component, that is inside the Lazy module we must edit the lazy-routing.module.ts file:

import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { SobreComponent } from './sobre/sobre.component';
const routes: Routes = [
  {path: '', component: SobreComponent}
];
@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class LazyRoutingModule { }


This route defines that when the module is loaded its default route will pointer to the About component:


As we see in the image, clicking the link “Lazy Module” load the Lazy Module which then calls the About component. In the network tab in the Chrome Dev Tools we can check the files that are loaded:


And make sure that the Lazy module is only loaded after clicking the link:


Definitely splinting an application into modules and using Lazy Load is a great alternative to improve the loading time and the initial file size of the application, because in the Bootstrapping process only the root module will be loaded.