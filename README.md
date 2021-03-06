# Varjoend

A lightweight end-to-end process testing library for the Java ecosystem.

This is an early proof of concept version, not released to any maven repository.

## Quickstart

Checkout the repository and build the library:

```bash
git checkout git@github.com:varjoinen/varjend.git
cd varjoend
./mvnw install

```

Add library as dependency:

```xml
<dependency>
    <groupId>org.varjoinen.testing</groupId>
    <artifactId>varjoend</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <scope>test</scope>
</dependency>
```

Create a test:

```java
public class EndToEndTest {

    @Test
    public void endToEndTest() throws Exception {

        Process process = Process
                .builder()
                .configuration(ProcessConfiguration.builder()
                        .propagateExceptions(false)
                        .build())
                .step(new Step("Count 1", this::incrementCount))
                .step(new Step("Count 2", this::incrementCount))
                .step(new Step("Exception", this::throwException))
                .step(new Step("Assertions", (ctx) -> {
                    Integer count = ctx.getVariable("count", Integer.class);
                    assertEquals(2, count);
                }))
                .build();

        process.getContext().setVariable("count", 0);

        process.execute();

        //Output: [ok] Count 1 --> [ok] Count 2 --> [failed] Exception --> [ok] Assertions --> [process completed with errors]
        System.out.println(process.visualise());
    }

    private void throwException(Context ctx) {
        throw new RuntimeException("error");
    }

    private void incrementCount(Context ctx) {
        Integer count = ctx.getVariable("count", Integer.class);
        ctx.setVariable("count", count + 1);
    }
}
```

## Development

Project has following dependencies:

* [Maven](https://maven.apache.org)
* [Lombok](https://projectlombok.org)