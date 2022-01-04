# Test libraries for Java Developers deliver stable features

A unit test need to be simple and test just one scenario in isolated way. There are two Java libraries that help to do
that:

* Junit
* EasyMock

The first one is a platform that help a java developer to create tests and run in a JVM. The second is a library that
help you to isolate the test creating mocks for you. In the next sections you will see the concepts and how to use theses libraries in development proccess.

## Mock

In a unit test, the main goal is to test some behavior from one unit (a class in Java). A mock is a way to create a
object that the unit that you are testing depends on. So, If you are testing a Class called ExportToJsonService and this
class depends on a another class called FileService, you can test the ExportToJsonService without instantiate the real
FileService implementation, you can use some library to create a Mock of FileService. The mock will be a object of
FileService, but without any implementation of the behaviors from the real implementation.

## Practice

The best way to learn is practising. So, let define some project to exemplify the use of test. In the follow sections we will present how to use the libraries, how to create a test and run and the full example are in the github repository referenced in the end. 

### Project example

There are two features in
this project:

- Generate a certificate in PDF format
- Generate a certificate metada in JSON format

Therefore, we have a project that can have a class called `GenerateCertificateService` that has a constructor with two
arguments of type `ExportService` which is an interface that has two implementations: `JsonExportService`
and `PDFExportService`.

### Architecture

- Main class
- Services: generateCertificate, exports

**Classes**

**GenerateCertificate**

```java
public class GenerateCertificate {
   
   private final ExportService pdfExportService;
   private final ExportService jsonExportService;
   
   public GenerateCertificate(ExportService pdfExportService, ExportService jsonExportService) {
      this.pdfExportService = pdfExportService;
      this.jsonExportService = jsonExportService;
   }
   
   public void generateCertificate(CertificateData certificateData) {
      // Generate Object Certificate from data
      // Export a PDF file from certificate object
      // Export a Json metadata from certificate object
   }
   
}
```

**ExportService**

```java
public interface ExportService {
   
   void export(Certificate certificate);
   
}
```

### Plan to create tests

The creation of tests start from the scenarios that you need to test. There are two types of scenarios:

- Success when the input are correct
- Error when the input are incorrect

So, the first test of GenerateCertificate is when the CertificateData is enough to generate a Certificate objetct that
can be exported in PDF and in JSON. Moreover, the second test is when there is something in the CertificateData that
lead to a error when try to create a certificate object.

Therefore, the first test is when the certificate object passed to exportService instance are correct and the two
instances of the exportService is called.

### First test

After plan the test, you can implement with Junit and EasyMock. A Test with `Junit 5` can be created with a Class that
had `@Test` above a method. So, the method annotated is the first scenario.

**GenerateCertificateTest**

```java
import org.junit.jupiter.api.Test;


public class GenerateCertificateTest {
   
   @Test
   public void whenGenerateCertificate_withCertificateDataValid_thenExportCertificate() {
      
   }
} 
```

After we need to create a instance of our unit that we will test: `GenerateCertificate`. After, we need to call the method to test our scenerio inside the method of the test:

```java
import dev.alexferreira.testtechniques.example.certificate.model.Certificate;
import dev.alexferreira.testtechniques.example.certificate.model.CertificateData;
import dev.alexferreira.testtechniques.example.certificate.service.ExportService;
import org.easymock.*;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;

class GenerateCertificateTest {

    private final GenerateCertificate generateCertificate;

    @Test
    void whenGenerateCertificate_withCertificateDataValid_thenExportCertificate() {
        CertificateData certificateData = new CertificateData();
        generateCertificate.generateCertificate(certificateData);
    }
}
```

After, we need to create the scenario that will be validated. For this propose we need to create mocks and asserts. We
use the EasyMock library in this example:

```java
import dev.alexferreira.testtechniques.example.certificate.model.Certificate;
import dev.alexferreira.testtechniques.example.certificate.model.CertificateData;
import dev.alexferreira.testtechniques.example.certificate.service.ExportService;
import org.easymock.*;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;

@ExtendWith(EasyMockExtension.class)
class GenerateCertificateTest extends EasyMockSupport {

/*1**/    @Mock
/*2**/    private ExportService exportService;
/*3**/
/*4**/    @TestSubject
/*5**/    private final GenerateCertificate generateCertificate = new GenerateCertificate(null);
/*6**/
/*7**/    @Test
/*8**/    void whenGenerateCertificate_withCertificateDataValid_thenExportCertificate() {
/*9**/        exportService.exportPDFFile(EasyMock.anyObject(Certificate.class));
/*10**/        replayAll();
/*11**/
/*12**/        CertificateData certificateData = new CertificateData();
/*13**/        generateCertificate.generateCertificate(certificateData);
/*14**/
/*15**/        verifyAll();
    }
}
```

**Test detailed**

Let's analyse the test:

* line 1 - 2: define a mock exportService to be used to construct 
* line 4: define that the object that will use the mock will be created by the filed annotated.
* line 5: define the object that will be tested and injected the mocks
* line 7-16: define the block to test, what will be executed by Junit
* line 10: call replayAll to expecify to EasyMock that the mocks will be used after that point.
* line 15: call verifyAll to expecify to EasyMock that need to verify all calls in all mocks created and throw an error when some call are not expected.

The test will verify if when we call the generateCertificate(), will be called the exportPDFFile() method from exportService mock. If it was not called, the test will fail.

Therefore, the EasyMock will help you to validate that the logic to use the dependencies from your unit in the expected way. You can see more freatures from EasyMock at the [official documentation](https://easymock.org/user-guide.html#verification).

### Running a test

So, at this point, if we run the test with maven: `mvn test` or with gradle: `gradlew test`, the test will fail, because we did not implement the method like the test expect. TDD (Test driven development) propose these, you define the test, after you implement it.

### Versions used to example

The example used in theses tutorial is hosted by [Github](https://github.com/alexferreiradev/tecnologias_java/tree/test-techniques/test-techniques). The versions used are following:
* Junit: 5
* EasyMock: 4.3
* Java 11

## TDD - Test Driven Development

After creating the test, we should implement the unit, do in a cycle step: run test, implement/fix, run test. So, the implementation is incomplete until the test had passed. 
In these example, the implementation to the project can be [that](https://github.com/alexferreiradev/tecnologias_java/tree/test-techniques/test-techniques).

## Conclusion

The two libraries are enought to a developer create unit tests to his software. However, the developers need to pay attention to the versions and the compatibility between them. Therefore, the Junit offer a Platform to run tests and the EasyMock offer a way to isolate the unit from the its dependencies. There are anothers libraries, but this article is not comparing, it just is showing the example libraries to each requirements to create tests.


# TODO

* ~~Add project example~~
* ~~Add test example~~
* ~~Complete the fist test~~
* ~~Study EasyMock to create the example of init, mock, verify and captureargs~~
* ~~Create branch test in technologias (rebase commits)~~
* ~~add code in the post~~
* ~~Describe the test and how to run with maven~~
* ~~Study or try to put lines in the markdown~~
* ~~Add lines in the code to explain all the parts (Mock, replay, verify)~~
* ~~Add some link to EasyMock official docs~~
* ~~Add a code example in the github tecnologias_java~~
* ~~Add gradle cmd test with `or`~~
* ~~Show the code to implementation to test pass~~
* ~~Add Section describing the version used in the example~~
* ~~Add a link to github example~~
* ~~Add conclusion section~~
* ~~Review the sections in mm~~
* ~~Adjust the sections according to mm~~
* Add infos to sections 
* Review the writing
* ask to someone review

  next
* Explain how to debug a test and how can be usefull
* Explain how the mock work and can be used (only mainly features)
* Create a post of Junit 4 and 5
* Create a post of EasyMock (Explain all features and limitations)
* Add TODO to new posts with a task to put link to the new posts
* 
* Add second test section
* Explain that this test is according to bussiness rules
* List the business rules and validations
* Explain how to create the next tests or find the test that are missing
* Add a section of line coverage

MM

Test libraries
    content
        Plataforma
            Junit
        Mock
            EasyMock
        Mock
        Pratica
            projeto
            Exemplos de codigos
            Plano para criacao de testes
            Primieiro test
            Run test
            Explicacao de teste
            TDD
        Conclusao
    plan
        Apresentar libs
            Junit
            EasyMock
        Conceitos apresentados
            Plataforma
            Mock
        Pratica
            intro sessao
                o que tem na sesao
                colocar em pratica
            intro projeto
                obj
                arquitetura
            Plano para criacao de teste
            config deps
                uso de junit e EasyMock
            primeiro test
            Run test
            Explicacao de teste
                junit
                EasyMock
            TDD
                how to finish the implementation
        Conclusao
            Para que serve junit e easyMock
            onde pode ser utilizado
            outras libs similares
