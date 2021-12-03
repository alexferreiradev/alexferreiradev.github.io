# 2 Java unit test libraries
A unit test need to be simple and test just one scenario in isolated way.
There are two Java libraries that help to do that:

* Junit
* EasyMock

The first one is a platform that help a java developer to create tests and run in a JVM. The second is a library that help you to isolate the test creating mocks for you.

## Mock

In a unit test, the main goal is to test some behavior from one unit (a class in Java). A mock is a way to create a object that the unit that you are testing depends on. So, If you are testing a Class called ExportToJsonService and this class depends on a another class called FileService, you can test the ExportToJsonService without instantiate the real FileService implementation, you can use some library to create a Mock of FileService. The mock will be a object of FileService, but without any implementation of the behaviors from the real implementation.

## Project Example

The best way to learn is practising. So, let define some project to examplefy the use of test. Therefore, we have a
project to need to test a class called `GenerateCertificate` that has a constructor with a argument of
type `ExportService` witch is an interface that has two implementations: `JsonExportService` and `PDFExportService`.

### Features
- Generate a certificate in PDF format 
- Generate a certificate metada in JSON format

### Architeture
- Resource 
- Service

## Junit
Library created to help Java developers to create owner tests, a addition to quality team tests. This library can be donwload or used with dependency managment tools (gradle, maven).

## EasyMock

