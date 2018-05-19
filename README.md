# Kojiro's Avro

A Python [Avro](https://avro.apache.org/docs/current/spec.html) library with type inference.

Avro is a schema-on-write serialization format. As such, most implementations of Avro seem to focus on building up a schema and then serializing data based on that schema. So you have to have a fully realized schema to write avro and you can't serialize and transmit data that doesn't match an expected schema.

This makes things simple for the reader: Produce a schema and provide it to writers, thus limiting the types of errors you will encounter.

But this isn't the only way to write data as avro. Data in Python already has a schema implicitly. Kojavro does not require you to declare the schema ahead of the data.

## Primitive types

If you have some data in Python, you can make it avro.

```python
>>> from kojavro import Avro
>>> Avro(None)
Avro(None)
>>> Avro(True)
AvroBool(True)
>>> Avro(1)
AvroInteger(1)
>>> Avro(3.14)
AvroFloat(3.14)
>>> Avro(b"abcde")
AvroBytes("abcde")
>>> Avro("pie in the sky")
AvroString("pie in the sky")
```

## Enum types

A Python Enum becomes an Avro Enum

```python
>>> from enum import Enum
>>> class Order(Enum):
...    ascending = "ascending"
...    descending = "descending"
...    ignore = "ignore"
...
>>> Avro(Order.ascending)
AvroEnum(Order.ascending)
```

## Iterable types

An iterable that has the same type for all its members is an AvroArray.

```python
>>> Avro(range(4))
AvroArray([AvroInt(0), AvroInt(1), AvroInt(2), AvroInt(3)])
```

A mapping from string to values of one type is an AvroMap.

```python
>>> Avro({"hi": 1, "bye": 2})
AvroMap({AvroString("hi"): AvroInt(1), AvroString("bye"): AvroInt(2)}
```

A mapping from string to different types of values is an AvroRecord with explicitly-named fields.

```python
>>> Avro({"fut": "conglomeration", "bal": 45.18})
AvroRecord([AvroField("fut", AvroString("conglomeration")), AvroField("bal", AvroFloat(45.18))])
```

An iterable that has different types of members is an AvroRecord with implicit field names.

```python
>>> Avro([0, "hi"])
AvroRecord([AvroField(AvroInteger(0)), AvroField(AvroString("hi"))])
```
