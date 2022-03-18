Schedule Service
----------------

```java
@Component
@RequiredArgsConstructor
@Slf4j
public class ScheduledTask {
  private static final int ONE_HOUR = 36_000_000;
  private static final int THREE_SECONDS = 3_000;

  private final RestTemplate restTemplate = new RestTemplateBuilder().build();
  private final TransactionMapper transactionMapper;

  // 1
  private final PresentationTransactionTransactionsClient client;

  @Value("${openbankproject.transactions.url}")
  private String url;

  // 2
  @Scheduled(fixedRate = ONE_HOUR, initialDelay = THREE_SECONDS)
  public void createTransactions() {
      log.info("Scheduler triggered... Starting transaction ingestion");
      List<TransactionsPostRequestBody> transactions = requestData();
      log.info("transactions: {}", transactions);
      // API is: POST /transaction-manager/service-api/v2/transactions
      client.postTransactions(transactions);
  }

  // 3
  private List<TransactionsPostRequestBody> requestData() {
      Transactions transactions = restTemplate.getForEntity(url, Transactions.class).getBody();

      // 4
      return transactions.getTransactions().stream()
          .map(transactionMapper::toTransactionsPostRequestBody)
          .collect(Collectors.toList());
  }

}
```

Note: Must enable scheduling
```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableScheduling
public class Application extends SpringBootServletInitializer {

    public static void main(final String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

Mapper
------
Use SSDK library `service-sdk-starter-mapping` and define the mapper.

```java
@Mapper(componentModel = "spring")
public interface TransactionMapper {

    @Mapping(target = "externalId", source = "id")
    @Mapping(target = "arrangementId", constant = "8a8a920a6bac6722016bac6e44dd0001")
    @Mapping(target = "externalArrangementId", constant = "A01")
    @Mapping(target = "reference", source = "thisAccount.number")
    @Mapping(target = "description", source = "details.description", defaultValue = "Gift")
    @Mapping(target = "typeGroup", constant = "Payment")
    @Mapping(target = "type", constant = "Credit/Debit Card")
    @Mapping(target = "category", source = "thisAccount.kind")
    @Mapping(target = "bookingDate", dateFormat = "yyyy-MM-dd'T'HH:mm:ss'Z'", source = "details.posted")
    @Mapping(target = "valueDate", dateFormat = "yyyy-MM-dd'T'HH:mm:ss'Z'", source = "details.completed")
    @Mapping(target = "transactionAmountCurrency.amount", source = "details.newBalance.amount", qualifiedByName = "instructedAmountConverter", defaultValue = "0")
    @Mapping(target = "transactionAmountCurrency.currencyCode", constant = "EUR")
    @Mapping(target = "instructedAmountCurrency.amount", source = "details.value.amount")
    @Mapping(target = "instructedAmountCurrency.currencyCode", constant = "EUR")
    @Mapping(target = "currencyExchangeRate", constant = "1")
    @Mapping(target = "counterPartyName", source = "thisAccount.kind")
    @Mapping(target = "counterPartyAccountNumber", source = "thisAccount.IBAN", qualifiedByName = "map", defaultValue = "NL86ABNA4461927814")
    @Mapping(target = "counterPartyBIC", source = "thisAccount.bank.nationalIdentifier", defaultValue = "ING00000001")
    @Mapping(target = "counterPartyCountry", constant = "NL")
    @Mapping(target = "counterPartyBankName", source = "thisAccount.bank.name")
    @Mapping(target = "creditorId", source = "otherAccount.holder.name")
    @Mapping(target = "mandateReference", source = "details.type")
    @Mapping(target = "billingStatus", constant = "BILLED")
    @Mapping(target = "checkSerialNumber", constant = "1")
    @Mapping(target = "runningBalance", constant = "1")
    @Mapping(target = "creditDebitIndicator", source = "details.value.amount", qualifiedByName = "creditDebitIndicator")
    TransactionsPostRequestBody toTransactionsPostRequestBody(Transaction transaction);

    @Named("creditDebitIndicator")
    default TransactionsPostRequestBody.CreditDebitIndicator amountToCreditDebitIndicator(String amount) {
        BigDecimal value = new BigDecimal(amount);
        if (value.compareTo(BigDecimal.ZERO) < 0)
            return TransactionsPostRequestBody.CreditDebitIndicator.DBIT;
        return TransactionsPostRequestBody.CreditDebitIndicator.CRDT;
    }

    @Named("instructedAmountConverter")
    default BigDecimal convert(Object amount) {
        return new BigDecimal(amount.toString());
    }

    @Named("map")
    default String map(Object value) {
        return value.toString();
    }
}
```

Generate Beans for Transactions
-------------------------------

Use JSONSchema2POJO or other methods to generate the classes for the api of the core system.

