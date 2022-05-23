##### Mocking examples

##### Using dependency for wiremock

    <dependency>
     <groupId>com.github.tomakehurst</groupId>
     <artifactId>wiremock-jre8</artifactId>
     <scope>test</scope>
    </dependency>

##### Need to create a class

```java

public class WiremockAccountService implements
    QuarkusTestResourceLifecycleManager {

        private WireMockServer wireMockServer;
        

        @Override
        public Map<String, String> start() {

            wireMockServer = new WireMockServer();
            wireMockServer.start();

            stubFor(get(urlEqualTo("/account/122222/balance"))
                    .willReturn(aResponse().withHeader("Content-Type","application/json")
                    .withBody("435.76")
                    ));

            stubFor(post(urlEqualTo("/accounts/1212231/transaction"))
                    .willReturn(noContent())
                                );
            
            return Collections.singletonMap(
                "io.quarkus.transaction.AccountService/mp-rest/url",
                wireMockServer.build());
            )


        }

    @Override
    public void stop() {

        if (null != wireMockServer) {
            wireMockSever.stop();
        }
    }
    }
```



##### Using the testServer


```java

    @QuarkusTest
    @QuarkusTestResource(WiremockAccountService.class)
    public class TransactionSeverTest {

        @Test
        void testTransaction() {

            given()
            .body("143.32")
            .contentType(ContentType.JSON)
            .when().post("/transactions/{accountNumber}",1231233)
            .then()
            .statusCode(200);

        }
    }

```


    mvn clean install 


#### AccountServiceProgramatic

```java

    @Path("/accounts")
    @Produces(MediaType.JSON)
    public interface AccountServiceProgrammatic {

        @GET
        @Path
        BigDecimal getBalance(@PathParam("accountNumber") Long accountNumber);


        @POST
        @Path
        void transact(@PathParam("accountNumber") Long accountNumber,
        BigDecimal mount);


    }


```

#### For programmatic access

```java

    @Path("/transactions")
    public class TransactionResource{

        @ConfigProperty(name="account.service",defaultValue="http://localhost:8080")
        String accountServiceUrl


        @Post
        @Path("/api/{accountNumber}")
        public Response newTransactionWithApi(@PathParam("accountNumber") Long accountNumber, BigDecimal amount) throws MalformedURLException
        {


            AccountServiceProgrammatic acctService =
                RestClientBuilder.newBuilder()
                .baseUrl(new URL(accountServiceurl))
                .connectTimeout(500,TimeUnit.MILLISECONDS)
                .readTimeout(1200,TimeUnit.MILLISECONDS)
                .build(AccountServiceProgrammatic.class);

            acctService.transact(accountNumber, amount);
            return Response.ok().build();
        }
    }

```



