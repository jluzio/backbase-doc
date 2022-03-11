# Ingestion / Integration strategies
- Push pattern: Inbound REST API
- Pull pattern: Outbound REST API
- Custom integration: programmatically developed integrations using the Backbase API specifications

## Examples
Integration patterns for Transactions
This topic explains the integration patterns that are available to tie Transactions to the core banking system.
The following methods to integrate Transactions journeys with the core banking system are available:

- Inbound integration
The Transactions journeys interact with DBS that ties into the core banking system. The core banking system pushes changes to DBS that persists the transactions and enriches them with Digital Banking Services features, such as the ability to add notes or categories. In addition to receiving pushed changes, DBS can also actively pull changes from the core banking system. Mixing pushing and pulling methods is possible.

- Outbound integration
The Transactions journeys interact with DBS that only checks with Access Control whether the user is entitled to access the transactions. When the permission is established, Digital Banking Services passes the request on to the core banking system, via a custom integration service. In this mode, transactions are not persisted in DBS, enrichment of data is not possible, and several features of Personal Finance Management are not supported.

- Direct integration
The Transactions journeys interact directly with the core banking system. DBS is not required. This option requires developers to create a custom integration layer that maps the interface expected by the Transactions journeys to the interface of the core banking system.
