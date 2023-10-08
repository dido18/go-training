
- capture changes in the database and trigger the evnet to other system
- database -> DMS (listen for insert/update/deletes using binlog) -> kinesis -> 

Downside:
- change from only one table
- need transformation enrichment of data 

**transactionall outbox:** 
- put the aggreagte data in a table of transactional log