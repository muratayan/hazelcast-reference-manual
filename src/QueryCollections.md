
## Querying in Collections and Arrays

Hazelcast enables querying in collections and arrays.

Let's have a look at the following data structure:

```java
class Swap {
   Leg legs[2];
}

class Leg {
   String currency;
}
```

In order to query a single element of a collection/array, you can execute the following query:

```java
IMap<Integer, Swaps> swap = getMap();
// this matches all swaps where the 1st leg's currency is 'EUR'
Predicate p = Predicates.equals('legs[0].currency', 'EUR');
Collection<Swap> result = map.values(p);
```

It is also possible to query a collection/array usint the `any` semantics as shown below:

```java
IMap<Integer, Swaps> swap = getMap();
// this matches all swaps where any leg's currency is 'EUR'
Predicate p = Predicates.equals('legs[any].currency', 'EUR');
Collection<Swap> result = map.values(p);
```

The exact same query may be executed using the `SQLPredicate` as shown below:

```
Predicate p = new SQLPredicate('legs[any].currency = EUR');
Collection<Swap> result = map.values(p);
```

`[]` notation applies to both collections and arrays.

### Indexing in Collections and Arrays

You can also create indexes in collections/arrays.
Note that the attribute used in the index definition has to be the same as the one used in the query.

Let's assume you have the following index definition:

```xml
<indexes>
  <index ordered="false">leg[any].currency</index>
</indexes>
```

The following predicate will use the index:

```java
Predicate p = Predicates.equals('leg[any].currency', 'EUR');
```


The following predicate, however, will NOT use the index:

```java
Predicates.equals('leg[0].currency', 'EUR')
```

In order to use the index in the above mentioned case, you would have to create another index as shown below:

```xml
<indexes>
  <index ordered="false">leg[0].currency</index>
</indexes>
```