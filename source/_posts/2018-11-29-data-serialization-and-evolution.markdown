---
layout: post
title: "Data Serialization and Evolution"
date: 2018-11-29 11:35:43 +0800
comments: true
categories: Serialization, Avro
---
When sending data over the network or storing it in a file, we need a way to encode the data into bytes. The area of data serialization has a long history, but has evolved quite a bit over the last few years. People started with programming language specific serialization such as Java serialization, which makes consuming the data in other languages inconvenient. People then moved to language agnostic formats such as JSON.
<!-- more -->
However, formats like JSON lack a strictly defined format, which has two significant drawbacks:

- **Data consumers may not understand data producers**: The lack of structure makes consuming data in these formats more challenging because fields can be arbitrarily added or removed, and data can even be corrupted. This drawback becomes more severe the more applications or teams across an organization begin consuming a data feed: if an upstream team can make arbitrary changes to the data format at their discretion, then it becomes very difficult to ensure that all downstream consumers will (continue to) be able to interpret the data. What’s missing is a “contract” (cf. schema below) for data between the producers and the consumers, similar to the contract of an API.
- **Overhead and verbosity**: They are verbose because field names and type information have to be explicitly represented in the serialized format, despite the fact that are identical across all messages.

A few cross-language serialization libraries have emerged that require the data structure to be formally defined by some sort of schemas. These libraries include Avro, Thrift, and Protocol Buffers. The advantage of having a schema is that it clearly specifies the structure, the type and the meaning (through documentation) of the data. With a schema, data can also be encoded more efficiently.

If you don’t know where to start, then we particularly recommend Avro, which is also supported in the Confluent Platform. In the next section we briefly introduce Avro, and how it can be used to support typical schema evolution.

## Avro

### Defining an Avro Schema

An Avro schema defines the data structure in a JSON format.

The following is an example Avro schema that specifies a user record with two fields: `name` and `favorite_number` of type `string` and `int`, respectively.
```json
{"namespace": "example.avro",
 "type": "record",
 "name": "user",
 "fields": [
     {"name": "name", "type": "string"},
     {"name": "favorite_number",  "type": "int"}
 ]
}
```
You can then use this Avro schema, for example, to serialize a Java object (POJO) into bytes, and deserialize these bytes back into the Java object.

One of the interesting things about Avro is that it not only requires a schema during data serialization, but also during data deserialization. Because the schema is provided at decoding time, metadata such as the field names don’t have to be explicitly encoded in the data. This makes the binary encoding of Avro data very compact.

### Schema Evolution

An important aspect of data management is schema evolution. After the initial schema is defined, applications may need to evolve it over time. When this happens, it’s critical for the downstream consumers to be able to handle data encoded with both the old and the new schema seamlessly. This is an area that tends to be overlooked in practice until you run into your first production issues. Without thinking through data management and schema evolution carefully, people often pay a much higher cost later on.

There are three common patterns of schema evolution:

- backward compatibility
- forward compatibility
- full compatibility

### Backward Compatibility
Backward compatibility means that data encoded with an older schema can be read with a newer schema.

Consider the case where all the data in Kafka is also loaded into HDFS, and we want to run SQL queries (e.g., using Apache Hive) over all the data. Here, it is important that the same SQl queries continue to work even as the data is undergoing changes over time. To support this kind of use case, we can evolve the schemas in a backward compatible way. Avro has a set of rules on what changes are allowed in the new schema for it to be backward compatible. If all schemas are evolved in a backward compatible way, we can always use the latest schema to query all the data uniformly.

For example, an application can evolve the user schema from the previous section to the following by adding a new field `favorite_color`:

```json
{"namespace": "example.avro",
 "type": "record",
 "name": "user",
 "fields": [
     {"name": "name", "type": "string"},
     {"name": "favorite_number",  "type": "int"},
     {"name": "favorite_color", "type": "string", "default": "green"}
 ]
}
```
Note that the new field `favorite_color` has the default value “green”. This allows data encoded with the old schema to be read with the new one. The default value specified in the new schema will be used for the missing field when deserializing the data encoded with the old schema. Had the default value been ommitted in the new field, the new schema would not be backward compatible with the old one since it’s not clear what value should be assigned to the new field, which is missing in the old data.

### Forward Compatibility

Forward compatibility means that data encoded with a newer schema can be read with an older schema.

Consider a use case where a consumer has application logic tied to a particular version of the schema. When the schema evolves, the application logic may not be updated immediately. Therefore, we need to be able to project data with newer schemas onto the (older) schema that the application understands. To support this use case, we can evolve the schemas in a forward compatible way: data encoded with the new schema can be read with the old schema. For example, the new user schema we looked at in the previous section on backward compatibility is also forward compatible with the old one. When projecting data written with the new schema to the old one, the new field is simply dropped. Had the new schema dropped the original field `favorite_number` (number, not color), it would not be forward compatible with the original user schema since we wouldn’t know how to fill in the value for `favorite_number` for the new data because the original schema did not specify a default value for that field.

Full Compatibility
Full compatibility means schemas are backward **and** forward compatible.

To support both previous use cases on the same data, we can evolve the schemas in a fully compatible way: old data can be read with the new schema, and new data can also be read with the old schema.