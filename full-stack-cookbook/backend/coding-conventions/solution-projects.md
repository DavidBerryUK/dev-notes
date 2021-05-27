# C# Solution Structure

\\TODO: solution / project diagram

Application solutions are broken down into projects, each with its own purpose. This assists in seperating project purpose and concerns into specialised projects.

The following is the breakdown of a typical solution. 

```
MyBackend.Api
MyBackend.Database
MyBackend.Models
MyBackend.Services
MyBackend.Utilites
```

## MyBackend.Api
The entry point into the application via ASP.Net Controllers. Data passed in and out of controllers should either be simple value types, or **Api Models**, they should never be database entity models.

## MyBackend.Database
Database access class, this does not contain any business logic. 

If the application is using a relational database, this layer will generally consist of an **Entity Framework** context, but the actual entity models will be placed in the models project. This allows the models to be access by the Services Project, which would not have access to the database project.

## MyBackend.Models
Simple database objects used to hold data. This includes those for database models, Api request and responses and other internal data models. The models have different suffixes to indicate their purpose....

* Database models have the suffix of **Entity**
* Api models have the suffix of **ApiModel**

```
Api
    CustomerDetailsApiModel.cs
    OrderDetailsApiModel.cs
    OrderLineApiModel.cs

Entity
    CustomerEntity.cs
    OrderEntity.cs
    OrderItemEntity.cs
    UserEntity.cs
```

## MyBackend.Services
Services are called from the API project Asp.Net controllers and generally accept a ApiModel as input. They also return more complex values as ApiModels that can be returned to the calling application.

Services access the database via EntityModels, but should not return them to the API layer as this is a security risk and causes complications should the database need to be reorganised. They can be passed between different services.

## MyBackend.Utilites
Contains help classes that generally perform non business tasks, such as formatting strings in a specific way, accessing application configuration or performing generic calculations

Helpers classes can be created for calling other external services via API, but this should not contain anys specific logic business or external service logic. Once this becomes a requirement the class should probably be placed into the Services project.

Utility classes are more likely to be shared between different projects on for different customers.