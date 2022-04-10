# Don't discover bugs in production, use unit tests

![Image to all is fine](https://www.meme-arsenal.com/memes/f146fd499738ead5ef3b2ee576ef3271.jpg)

A bug in software development is common, but some times it can cost a lot. The most known case of a bug that cost million of dollars was the Mariner 1 spacecraft. An investigation revealed the cause. A very simple software error, the developer omitted an hyphen in a line of code. That sent an incorrect guidance signals to to the spacecraft. The cost of the omission turned out to be more than $18 million at the time (about $169 million in today’s currency).

So, don't lose money discovering bugs in production. Prevent it with a simple method, use tests in the development stage. Here you will learn one type of test for Java projects, and how to create and run them in during development. At the end, you will be ready to configure your project with tests. Use libraries, plan the scenarios and create the tests and mocks. Finally, you will prevent bugs in production.  

## Prevent bugs with Unit Test

The most simple test is unit test. It need to be simple and test only one
scenario in isolated way. 

### Mock

In a unit test, the main goal is to test some behavior from one unit (a class in Java). A mock is a way to create a object that the unit that you are testing depends on. So, If you are testing a Class called `ExportToJsonService` and this class depends on a another class called `FileService`, you can test the `ExportToJsonService` without instantiate the real `FileService` implementation, you can use some library to create a Mock of `FileService`. The mock will be a object of `FileService`, but without any implementation of the behaviors from the real implementation.

## Create a unit test

Now, you know about unit test and mocks. Before starting creating a tests, we need libraries to don't reinvent the wheel. There are two Java libraries that can help you to create unit tests in JAVA:

* Junit
* EasyMock

The first one is a platform that help a java developer to create tests and run in a JVM. The second is a library that help you to isolate the test creating mocks for you. In the next sessions, you will learn how to use theses libraries in development proccess.  In the end, you will find the full example of a project with theses libraries in a github repository.
### Project example

So, let define some project to exemplify the use of the java test libraries. This project is simple, it generates a PDF certificate to participants of an event. So, there are two features in this project:
 
- Generate a certificate in PDF format
- Generate a certificate metada in JSON format

So, we designed the project to have these classes:

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

    void exportJsonFile(Certificate certificate);

    void exportPDFFile(Certificate certificate);

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

After plan the test, develop it with Junit and EasyMock. We can create a Test with `Junit 5` with a Class that
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

We created a test for the unit. Now we need to create a instance of our unit: `GenerateCertificate`. After, we need to call the
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

After, We use the EasyMock library  to create mocks and call's verification.  So, we can create a scenario to test some code flux. See the example above:

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

* line 1 - 2: define a mock exportService. The annotation specific to create an instance without any behavior.
* line 4: create a instance of the unit and inject the mocks.
* line 5: define the object to test and inject the mocks
* line 7-16: define the block to test and Junit execute it
* line 10: call `replayAll` to specify for EasyMock the mocks to verify after that point.
* line 15: call `verifyAll` to configure EasyMock to verify all calls in all mocks created. It will throw an error when some call are not expected.

The test will verify the calls to `generateCertificate`. If `exportPDFFile()` was not called, the test will fail.

Therefore, the EasyMock will help you to validate the unit dependencies behaviors. You can see more features from EasyMock at the [official documentation](https://easymock.org/user-guide.html#verification).

### Running a test

So, at this point, we can run the test with maven: `mvn test` or with gradle: `gradlew test`. The test will fail, because we did not implement the function like the test expect. According to Test driven development (TDD), we should implement the unit in a cyclic process. You need run test, implement/fix and run test. So, the implementation is incomplete until the test had passed. Find more information about TDD [here](https://en.wikipedia.org/wiki/Test-driven_development).

### Versions used in this example

The example used in theses tutorial is hosted
by [Github](https://github.com/alexferreiradev/tecnologias_java/tree/test-techniques/test-techniques). The versions used are following:

* Junit: 5
* EasyMock: 4.3
* Java 11

## Conclusion

We presented how to prevents bugs with unit tests, the libraries that you can use to create the tests and mocks. Now you can prevent bug in production.

We used two libraries that are enough for a developer create unit tests to his software. But, you need to pay attention to the versions and the compatibility between them. There are another libraries, but this article is not comparing them. We is showing one example of library for each requirements to create tests.

I am a Java Developer that help another Java developers to apply tests techniques in software development, so they deliver stable features faster without break things.

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
* ~~Plan the sections according to Bruno review~~
* ~~change the mock to be the prevent bugs, after, we will present the libraries, just in the next session~~ 
* ~~remove refer to tdd in the creation section~~
* ~~change the section create unit test~~
* ~~review conclusion~~
* ~~change TDD to refer, but not the main point, just to future~~
* ~~Review with editor with dictionary:~~ 
* ~~https://www.grammarly.com/~~
* ~~https://prowritingaid.com/Premium~~
* ~~https://languagetool.org/pt-BR~~
* ~~https://hemingwayapp.com/~~
* ~~review project exemplo~~
* ~~fix the code, put two methods in export pdf and json~~
* ~~fix the code in repository~~
* ~~ask to someone review~~
* decidir qual a melhor forma de guardar historico de 5 minutos
* organizar branch
* mergiar na develop

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
    formas de organizar git repo
        squash de tudo ? 
            nao guarda data e hora de commits ?
        manter todos commits ?
            fica muito grande o historico de branches
        Criar arquivo metadados ?
            mover datas de commits para arquivo ? 
            seria dificil extrair datas dos commits ? 
            teria que ser via script ? 
        manter diffs em zip ?
            podemos manter toda a branch ? 
            como copiar estes arquivos do git ? 
            teria que ser via script ? 
        existe forma de exportar uma branch ? 
            desde a base até ultimo commit ? 
            qual formato de arquivo ? 
            teria que ser via script ? 
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
                                    <https://raygun.com/blog/costly-software-errors-history/> <https://raygun.com/blog/costly-software-errors-history/>>
                                        marine
                                        the morris worm
                                        bitcoin hack
                                Software to marketplace
                                    wrong price
                                        <https://querysprout.com/amazon-price-mistake-policy/> <https://querysprout.com/amazon-price-mistake-policy/>> <https://querysprout.com/amazon-price-mistake-policy/>>
                                        <https://help.vtex.com/tutorial/troubleshooting-price-divergence-errors-in-marketplace-orders--6MbmPX4SKyRkcTJxVhRna8> <https://help.vtex.com/tutorial/troubleshooting-price-divergence-errors-in-marketplace-orders--6MbmPX4SKyRkcTJxVhRna8>> <https://help.vtex.com/tutorial/troubleshooting-price-divergence-errors-in-marketplace-orders--6MbmPX4SKyRkcTJxVhRna8>>
                                    Wrong calculation
                                    not finalise the order
                                Software to car
                                software to airplane
                    prevent bugs
        melhor forma de organizar branchs

