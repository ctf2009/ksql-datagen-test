# Datagen Testing
This folder tries to explore the various ways we can use the Datagen, specifically to produce Avro records

**Note**
The following is noted in KSQL documentation. For the Datagen, you cannot provide this so the resulting schemas in the registry will look a bit different than expected in terms of naming. In the schema of things, this is ok because with KSQL we dont use namespaces or the names of records. Thereore this is fine for testing

```
By default, ksqlDB-registered Avro schemas have the same name (KsqlDataSourceSchema) and the same namespace (io.confluent.ksql.avro_schemas). You can override this behavior by providing a VALUE_AVRO_SCHEMA_FULL_NAME property in the WITH clause, where you set the VALUE_FORMAT to 'AVRO'. As the name suggests, this property overrides the default name/namespace with the provided one. For example, com.mycompany.MySchema registers a schema with the MySchema name and the com.mycompany namespace
```

## 1 - Simple Schema
This is a very simple schema which has 3 fields. It makes use of `regex`, `options` and `range`
To run it, execute the following

```
ksql-datagen \
 bootstrap-server=localhost:9092 \
 schema=1-simple-schema.avsc \
 value-format=avro \
 schemaRegistryUrl=http://localhost:8081 \
 iterations=10 \
 topic=DATAGEN-TEST-1 \
 key=id
```

The output is quite verbose but should have something similar to the following. You can see below that 10 records were generated

```
[2023-11-28 19:51:07,445] INFO [Producer clientId=KSQLDataGenProducer] Instantiated an idempotent producer. (org.apache.kafka.clients.producer.KafkaProducer:596)
[2023-11-28 19:51:07,476] INFO These configurations '[ksql.schema.registry.url]' were supplied but are not used yet. (org.apache.kafka.clients.producer.ProducerConfig:379)
[2023-11-28 19:51:07,715] INFO [Producer clientId=KSQLDataGenProducer] Cluster ID: R7v1IUZhQ72E17NYEWUD-g (org.apache.kafka.clients.Metadata:287)
[2023-11-28 19:51:07,717] INFO [Producer clientId=KSQLDataGenProducer] ProducerId set to 21 with epoch 0 (org.apache.kafka.clients.producer.internals.TransactionManager:441)
['fRYlwoacC'] --> ([ 'fRYlwoacC' | 'code1' | 163L ]) ts:1701165067716
['elLXMWpP'] --> ([ 'elLXMWpP' | 'code3' | 714L ]) ts:1701165067812
['OEyaxcytrK'] --> ([ 'OEyaxcytrK' | 'code1' | 262L ]) ts:1701165067812
['sQAzINJlI'] --> ([ 'sQAzINJlI' | 'code2' | 66L ]) ts:1701165067812
['XSsQBcci'] --> ([ 'XSsQBcci' | 'code2' | 343L ]) ts:1701165067813
['bhWgjJESWy'] --> ([ 'bhWgjJESWy' | 'code1' | 141L ]) ts:1701165067813
['ZcqjgIfYv'] --> ([ 'ZcqjgIfYv' | 'code3' | 92L ]) ts:1701165067813
['AorUmGZBq'] --> ([ 'AorUmGZBq' | 'code1' | 433L ]) ts:1701165067813
['qiiapSFgl'] --> ([ 'qiiapSFgl' | 'code3' | 855L ]) ts:1701165067814
['DmZxwVRS'] --> ([ 'DmZxwVRS' | 'code3' | 451L ]) ts:1701165067814
[2023-11-28 19:51:07,823] INFO [Producer clientId=KSQLDataGenProducer] Closing the Kafka producer with timeoutMillis = 9223372036854775807 ms. (org.apache.kafka.clients.producer.KafkaProducer:13
```

Note that the schema that was created in the registry looks like this

```
{
  "connect.name": "io.confluent.ksql.avro_schemas.KsqlDataSourceSchema",
  "fields": [
    {
      "default": null,
      "name": "id",
      "type": [
        "null",
        "string"
      ]
    },
    {
      "default": null,
      "name": "code",
      "type": [
        "null",
        "string"
      ]
    },
    {
      "default": null,
      "name": "count",
      "type": [
        "null",
        "long"
      ]
    }
  ],
  "name": "KsqlDataSourceSchema",
  "namespace": "io.confluent.ksql.avro_schemas",
  "type": "record"
}
```

## 2 - Nested Record Schema
This example has a record nested in another record. It uses `options` to define input
To run it, execute the following

```
ksql-datagen \
 bootstrap-server=localhost:9092 \
 schema=2-nested-record.avsc \
 value-format=avro \
 schemaRegistryUrl=http://localhost:8081 \
 iterations=10 \
 topic=DATAGEN-TEST-2 \
 key=id
```

You should get an output similar to the below

```
[2023-11-28 20:07:25,862] INFO [Producer clientId=KSQLDataGenProducer] Instantiated an idempotent producer. (org.apache.kafka.clients.producer.KafkaProducer:596)
[2023-11-28 20:07:25,894] INFO These configurations '[ksql.schema.registry.url]' were supplied but are not used yet. (org.apache.kafka.clients.producer.ProducerConfig:379)
[2023-11-28 20:07:26,131] INFO [Producer clientId=KSQLDataGenProducer] Cluster ID: R7v1IUZhQ72E17NYEWUD-g (org.apache.kafka.clients.Metadata:287)
[2023-11-28 20:07:26,132] INFO [Producer clientId=KSQLDataGenProducer] ProducerId set to 25 with epoch 0 (org.apache.kafka.clients.producer.internals.TransactionManager:441)
['JuHYFfeDcE'] --> ([ 'JuHYFfeDcE' | Struct{count=10,hash=hash1} ]) ts:1701166046131
['WafdSmzq'] --> ([ 'WafdSmzq' | Struct{count=20,hash=hash2} ]) ts:1701166046227
['xepPvjMW'] --> ([ 'xepPvjMW' | Struct{count=20,hash=hash2} ]) ts:1701166046227
['ufedkHMAEUMs'] --> ([ 'ufedkHMAEUMs' | Struct{count=10,hash=hash1} ]) ts:1701166046228
['rqTEOKykrll'] --> ([ 'rqTEOKykrll' | Struct{count=20,hash=hash2} ]) ts:1701166046228
['RLHyFTwq'] --> ([ 'RLHyFTwq' | Struct{count=20,hash=hash2} ]) ts:1701166046229
['PCjgQvwYa'] --> ([ 'PCjgQvwYa' | Struct{count=20,hash=hash2} ]) ts:1701166046229
['QWHWhLXia'] --> ([ 'QWHWhLXia' | Struct{count=20,hash=hash2} ]) ts:1701166046230
['gzdeeWlqTBoaIY'] --> ([ 'gzdeeWlqTBoaIY' | Struct{count=20,hash=hash2} ]) ts:1701166046230
['mfKRDYLKal'] --> ([ 'mfKRDYLKal' | Struct{count=10,hash=hash1} ]) ts:1701166046230
[2023-11-28 20:07:26,239] INFO [Producer clientId=KSQLDataGenProducer] Closing the Kafka producer with timeoutMillis = 9223372036854775807 ms. (org.apache.kafka.clients.producer.KafkaProducer:1311)
```

Note that the schema that was created in the registry looks like this

```
{
  "connect.name": "io.confluent.ksql.avro_schemas.KsqlDataSourceSchema",
  "fields": [
    {
      "default": null,
      "name": "id",
      "type": [
        "null",
        "string"
      ]
    },
    {
      "default": null,
      "name": "nested",
      "type": [
        "null",
        {
          "connect.name": "io.confluent.ksql.avro_schemas.KsqlDataSourceSchema_nested",
          "fields": [
            {
              "default": null,
              "name": "count",
              "type": [
                "null",
                "int"
              ]
            },
            {
              "default": null,
              "name": "hash",
              "type": [
                "null",
                "string"
              ]
            }
          ],
          "name": "KsqlDataSourceSchema_nested",
          "type": "record"
        }
      ]
    }
  ],
  "name": "KsqlDataSourceSchema",
  "namespace": "io.confluent.ksql.avro_schemas",
  "type": "record"
}
```

## 3 - Nested Record Schema Field Options
This is similar to the above, however each field now has `arg.properties`
To run it, execute the following

```
ksql-datagen \
 bootstrap-server=localhost:9092 \
 schema=3-nested-record-field-options.avsc \
 value-format=avro \
 schemaRegistryUrl=http://localhost:8081 \
 iterations=10 \
 topic=DATAGEN-TEST-3 \
 key=id
```

You should get an output similar to the below

```
[2023-11-28 20:15:27,480] INFO [Producer clientId=KSQLDataGenProducer] Instantiated an idempotent producer. (org.apache.kafka.clients.producer.KafkaProducer:596)
[2023-11-28 20:15:27,511] INFO These configurations '[ksql.schema.registry.url]' were supplied but are not used yet. (org.apache.kafka.clients.producer.ProducerConfig:379)
[2023-11-28 20:15:27,764] WARN [Producer clientId=KSQLDataGenProducer] Error while fetching metadata with correlation id 1 : {DATAGEN-TEST-3=LEADER_NOT_AVAILABLE} (org.apache.kafka.clients.NetworkClient:1272)
[2023-11-28 20:15:27,765] INFO [Producer clientId=KSQLDataGenProducer] Cluster ID: R7v1IUZhQ72E17NYEWUD-g (org.apache.kafka.clients.Metadata:287)
[2023-11-28 20:15:27,766] INFO [Producer clientId=KSQLDataGenProducer] ProducerId set to 26 with epoch 0 (org.apache.kafka.clients.producer.internals.TransactionManager:441)
['QeBjFHakIW'] --> ([ 'QeBjFHakIW' | Struct{count=3,hash=ICsXQdBRon} ]) ts:1701166527867
['sjIDMjELCwzaK'] --> ([ 'sjIDMjELCwzaK' | Struct{count=2,hash=pKFZvHqv} ]) ts:1701166527964
['MYpBCGCmddjt'] --> ([ 'MYpBCGCmddjt' | Struct{count=4,hash=nEAVGeDlk} ]) ts:1701166527966
['jmuKrHKlBh'] --> ([ 'jmuKrHKlBh' | Struct{count=9,hash=wJVpWeqP} ]) ts:1701166527967
['AnsXXyuErMTR'] --> ([ 'AnsXXyuErMTR' | Struct{count=4,hash=RlVPYiXNlGwI} ]) ts:1701166527967
['HiKAeuADCG'] --> ([ 'HiKAeuADCG' | Struct{count=6,hash=ksQiCNODd} ]) ts:1701166527967
['MrYRCVnbZSmUr'] --> ([ 'MrYRCVnbZSmUr' | Struct{count=1,hash=CkEVZBuNsTFQLbl} ]) ts:1701166527968
['xoGBJOXbgutTMT'] --> ([ 'xoGBJOXbgutTMT' | Struct{count=3,hash=CxwxSABeNu} ]) ts:1701166527968
['nztAXfVWin'] --> ([ 'nztAXfVWin' | Struct{count=2,hash=npjFfwIgi} ]) ts:1701166527969
['gvvDWncL'] --> ([ 'gvvDWncL' | Struct{count=3,hash=TmgFFHGbpn} ]) ts:1701166527969
[2023-11-28 20:15:27,975] INFO [Producer clientId=KSQLDataGenProducer] Closing the Kafka producer with timeoutMillis = 9223372036854775807 ms. (org.apache.kafka.clients.producer.KafkaProducer:1311)
```

The schema for this is the same as the previous 

## 4 - Simple Array
This contains a very simple string array
To run it, execute the following

```
ksql-datagen \
 bootstrap-server=localhost:9092 \
 schema=4-simple-array.avsc \
 value-format=avro \
 schemaRegistryUrl=http://localhost:8081 \
 iterations=10 \
 topic=DATAGEN-TEST-4 \
 key=id
```

You should get an output similar to the below

```
[2023-11-28 20:30:06,272] INFO [Producer clientId=KSQLDataGenProducer] Instantiated an idempotent producer. (org.apache.kafka.clients.producer.KafkaProducer:596)
[2023-11-28 20:30:06,307] INFO These configurations '[ksql.schema.registry.url]' were supplied but are not used yet. (org.apache.kafka.clients.producer.ProducerConfig:379)
[2023-11-28 20:30:06,546] INFO [Producer clientId=KSQLDataGenProducer] Cluster ID: R7v1IUZhQ72E17NYEWUD-g (org.apache.kafka.clients.Metadata:287)
[2023-11-28 20:30:06,547] INFO [Producer clientId=KSQLDataGenProducer] ProducerId set to 30 with epoch 0 (org.apache.kafka.clients.producer.internals.TransactionManager:441)
['test_heQcznMs'] --> ([ 'test_heQcznMs' | [thing1, thing2, thing3, thing4] ]) ts:1701167406546
['test_pAAUAhpuq'] --> ([ 'test_pAAUAhpuq' | [thing1, thing2] ]) ts:1701167406643
['test_oPKvYNFvG'] --> ([ 'test_oPKvYNFvG' | [thing1, thing2, thing3, thing4] ]) ts:1701167406643
['test_tOJujwez'] --> ([ 'test_tOJujwez' | [thing1, thing2, thing3] ]) ts:1701167406643
['test_aVhaztfy'] --> ([ 'test_aVhaztfy' | [thing1, thing2, thing3, thing4] ]) ts:1701167406644
['test_TtSdXJUdZ'] --> ([ 'test_TtSdXJUdZ' | [thing1, thing2] ]) ts:1701167406644
['test_jVfuzcaM'] --> ([ 'test_jVfuzcaM' | [thing1, thing2, thing3] ]) ts:1701167406644
['test_vXNRRMHX'] --> ([ 'test_vXNRRMHX' | [thing1, thing2, thing3] ]) ts:1701167406644
['test_uaaYqbdTO'] --> ([ 'test_uaaYqbdTO' | [thing1, thing2, thing3] ]) ts:1701167406645
['test_kzlDZFaJj'] --> ([ 'test_kzlDZFaJj' | [thing1, thing2, thing3, thing4] ]) ts:1701167406645
[2023-11-28 20:30:06,655] INFO [Producer clientId=KSQLDataGenProducer] Closing the Kafka producer with timeoutMillis = 9223372036854775807 ms. (org.apache.kafka.clients.producer.KafkaProducer:1311)
```

Note that the schema that was created in the registry looks like this

```
{
  "connect.name": "io.confluent.ksql.avro_schemas.KsqlDataSourceSchema",
  "fields": [
    {
      "default": null,
      "name": "id",
      "type": [
        "null",
        "string"
      ]
    },
    {
      "default": null,
      "name": "things",
      "type": [
        "null",
        {
          "items": [
            "null",
            "string"
          ],
          "type": "array"
        }
      ]
    }
  ],
  "name": "KsqlDataSourceSchema",
  "namespace": "io.confluent.ksql.avro_schemas",
  "type": "record"
}
```

## 5 Complex Array
This is intended to have an array of records
To run it, execute the following

```
ksql-datagen \
 bootstrap-server=localhost:9092 \
 schema=5-complex-array.avsc \
 value-format=avro \
 schemaRegistryUrl=http://localhost:8081 \
 iterations=10 \
 topic=DATAGEN-TEST-5 \
 key=id
```

This results in the error
```
java.lang.RuntimeException: Invalid option for array schema: type java.util.LinkedHashMap, value '{count=1}'
```
This can be traced to this ticket: https://github.com/confluentinc/kafka-connect-datagen/issues/70
