# Dependency Injection

In software engineering, dependency injection is a technique in which an object receives other objects that it depends on. These other objects are called dependencies. In the typical "using" relationship the receiving object is called a client and the passed (that is, "injected") object is called a service.

Dependancy allows a service to specify what services it needs to run and the dependency framework will ensure that these are created and available for use.

The means that the service does not need to understand how to construct the dependancy service.

Services should use an interface which is registered with the dependancy framework along with the actual implementation of the service. This allows there to be several different actual classes that match the interface, and the one that is actually used is defined at registation time.

The use of interfaces also allows mock services to be created for testing, or early application development that return known values for each method. This helps avoid prevent blocks on development.

//TODO: provide good example

  * get user controller
  * get user service
  * database service
  * astrology service
  * orders service
  * age calculator service

