# Don't discover bugs in production, use unit tests

![Image to all is fine](https://www.meme-arsenal.com/memes/f146fd499738ead5ef3b2ee576ef3271.jpg)

A bug in software development is common, but some times it can cost a lot. The most known case of a bug that cost million of dollars was the Mariner 1 spacecraft. An investigation revealed the cause was a very simple software error, a hyphen was omitted in a line of code, which meant that incorrect guidance signals were sent to the spacecraft. The overall cost of the omission was reported to be more than $18 million at the time (about $169 million in today’s world). Therefore, dont lose money discovering bugs in production, prevent it with a simple method, use tests in the development stage. In this article, we will present one type of test that can be made with Java softwares, how to create and run them in the development stage. At the end, you will be ready to configure your project with tests libraries, plan the scenarios that will be tested, create the tests and mocks and finally, prevent bugs in production.  

## Prevent bugs with Unit Test

The most simple test is unit test. It need to be simple and test only one
scenario in isolated way. There are two Java libraries that can help you to do that:

* Junit
* EasyMock

The first one is a platform that help a java developer to create tests and run in a JVM. The second is a library that
help you to isolate the test creating mocks for you. In the next sections you will see the concepts and how to use
theses libraries in development proccess.

### Mock

In a unit test, the main goal is to test some behavior from one unit (a class in Java). A mock is a way to create a
object that the unit that you are testing depends on. So, If you are testing a Class called ExportToJsonService and this
class depends on a another class called FileService, you can test the ExportToJsonService without instantiate the real
FileService implementation, you can use some library to create a Mock of FileService. The mock will be a object of
FileService, but without any implementation of the behaviors from the real implementation.

## Create a unit test

Now, you know about unit test and mocks. So, let define some project to exemplify the use of the libraries to TDD. In the
below sections, we will present how to use the libraries, how to create and run a test, and in the end, you can find the
full example in a github repository.

### Project example

The example project is simple, it generate a certificate PDF acording to a object and a json with the metadata of the
event. There are two features in this project:

- Generate a certificate in PDF format
- Generate a certificate metada in JSON format

So, the project can be designed to have these classes:

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

### Planning your tests

The creation of tests start from the scenarios that you need to test. There are two types of scenarios:

- Success when the input are correct
- Error when the input are incorrect

So, the first test of GenerateCertificate is when the CertificateData is enough to generate a Certificate objetct that
can be exported in PDF and in JSON. Moreover, the second test is when there is something in the CertificateData that
lead to a error when try to create a certificate object.

Therefore, the first test is when the certificate object passed to exportService instance are correct and the two
instances of the exportService is called.

### First unit test

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

After we need to create a instance of our unit that we will be tested: `GenerateCertificate`. After, we need to call the
method to test our scenario inside the method of the test with the correct inputs:

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

    /*01**/
    @Mock
    /*02**/ private ExportService exportService;
    /*03**/
    /*04**/
    @TestSubject
    /*05**/ private final GenerateCertificate generateCertificate = new GenerateCertificate(null);

    /*06**/
    /*07**/
    @Test
    /*08**/    void whenGenerateCertificate_withCertificateDataValid_thenExportCertificate() {
        /*09**/
        exportService.exportPDFFile(EasyMock.anyObject(Certificate.class));
        /*10**/
        replayAll();
        /*11**/
        /*12**/
        CertificateData certificateData = new CertificateData();
        /*13**/
        generateCertificate.generateCertificate(certificateData);
        /*14**/
        /*15**/
        verifyAll();
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
* line 15: call verifyAll to expecify to EasyMock that need to verify all calls in all mocks created and throw an error
  when some call are not expected.

The test will verify if when we call the generateCertificate(), will be called the exportPDFFile() method from
exportService mock. If it was not called, the test will fail.

Therefore, the EasyMock will help you to validate that the logic to use the dependencies from your unit in the expected
way. You can see more freatures from EasyMock at
the [official documentation](https://easymock.org/user-guide.html#verification).

### Running a test

So, at this point, if we run the test with maven: `mvn test` or with gradle: `gradlew test`, the test will fail, because
we did not implement the method like the test expect. TDD (Test driven development) propose these, you define the test,
after you implement it.

### Versions used to example

The example used in theses tutorial is hosted
by [Github](https://github.com/alexferreiradev/tecnologias_java/tree/test-techniques/test-techniques). The versions used
are following:

* Junit: 5
* EasyMock: 4.3
* Java 11

## TDD - Test Driven Development

After creating the test, we should implement the unit, do in a cycle step: run test, implement/fix, run test. So, the
implementation is incomplete until the test had passed. In these example, the implementation to the project can
be [that](https://github.com/alexferreiradev/tecnologias_java/tree/test-techniques/test-techniques).

## Conclusion

The two libraries are enought to a developer create unit tests to his software. However, the developers need to pay
attention to the versions and the compatibility between them. Therefore, the Junit offer a Platform to run tests and the
EasyMock offer a way to isolate the unit from the its dependencies. There are anothers libraries, but this article is
not comparing, it just is showing the example libraries to each requirements to create tests.

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
* ~~Add gradle cmd test with or~~
* ~~Show the code to implementation to test pass~~
* ~~Add Section describing the version used in the example~~
* ~~Add a link to github example~~
* ~~Add conclusion section~~
* ~~Review the sections in mm~~
* ~~Adjust the sections according to mm~~
* ~~Add infos to sections~~
* ~~Review the writing~~
* ~~Doc review from Bruno~~
~~* Plan the sections according to Bruno review~~
* Review with editor with dictionary
* fix the code, put two methods in export pdf and json
* fix the code in repository
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
plan 2
    review
        Choose your public
            to remove backgrounds unnecessary
        change the intro to get attention
            resolve a problem
            expose the problem
            expose a solution to a problem
        change the name of sections
            to get attention
            to make consistent
            to make sense
            link each section each other
        Put some image
    sections after review
        titlle
            deliver fast with unit tests
        intro
            when occour the problem
                You have only integration tests
            the problem
                the time to test is big
            the solution
                use unit test with mocks
        problem
            fear of update the dependencies
                without test, the dev will discover the change of a behavior of a lib in production
            Softwares without unit tests
                fear to add teste
                dont have knowledge to add test in the team
            time to test is big
                there are only integration tests
                there are a need of QA to test
            difficult to create a testing culture in a team
                team have fear to test
        want
            Softwares stables
            deliver stable features
                create isolated tests
            make project stable
            make project updatable
                new features
                up to date technologies
            make project updated
                up to date technologies
        what I have
            how to create a test
            how to create isolated test
            how to test diferents scenarios
            how migrate a project without test to a stable
            what is the test content
            how to create isolate test
            how to use a library to mock
            how to mock a dependency of a unit
            how run a test
        my public
            dev pleno Java
                quer se tornar senior
                softwares com baixa cobertura de codigo
    new sections name
        intro
        Prevent bugs in production
            libraries to unit test to JAVA
            Isolating a unit test with mocks
        Use Junit and EasyMock
            Implement using TDD
            Create a unit test
            EasyMock Detailed
        Benefits using unit tests
    tasks
        done
            list the problems
            list the contents
            list the solutions
            plan tasks
            explore the problems
            select a main problem
                fear of update dependencies
            list titles
                update software
                    the main way to work with updates softwares
                    the easyest way to update your software
                solution
                    dont have bugs in production anymore, use unit tests
                    have updated softwares, use unit tests
                    dont discovery bugs in production, use unit tests
            select 3 titles
        update the article with the new points
            done
                title
                intro to problem
                name of sections
            write sections
                intro
                    search more data
                        about losing money with bugs
                        news with
                            software error
                                <https://raygun.com/blog/costly-software-errors-history/>
                                    marine 
                                    the morris worm
                                    bitcoin hack
                            Software to marketplace
                                wrong price
                                    <https://querysprout.com/amazon-price-mistake-policy/> <https://querysprout.com/amazon-price-mistake-policy/>>
                                    <https://help.vtex.com/tutorial/troubleshooting-price-divergence-errors-in-marketplace-orders--6MbmPX4SKyRkcTJxVhRna8> <https://help.vtex.com/tutorial/troubleshooting-price-divergence-errors-in-marketplace-orders--6MbmPX4SKyRkcTJxVhRna8>>
                                Wrong calculation
                                not finalise the order
                            Software to car
                            software to airplane
                prevent bugs
