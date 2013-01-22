# EZDB

EZDB provides a nice wrapper around LevelDB. Let's take a look!

##### Simple Key/Value

You can always use EZDB as a regular key/value store.

    Db ezdb = new EzLevelDb(new File("/tmp"));
    Table<Integer, Integer, Integer> table = ezdb.getTable("test", IntegerSerde.get, IntegerSerde.get, IntegerSerde.get);
    
    table.put(1, 1);
    
    System.out.println(table.get(1)); // prints 1

##### Hash/Range Combination

EZDB also supports hash/range lookups!

    Db ezdb = new EzLevelDb(new File("/tmp"));
    Table<Integer, String, Integer> table = ezdb.getTable("test", IntegerSerde.get, StringSerde.get, IntegerSerde.get);
    
    table.put(1213, "20120102-foo", 1234);
    table.put(1213, "20120102-bar", 5678);
    
    TableIterator<Integer, String, Integer> it = table.range(1213, "20120102", "20120103");
    
    while(it.hasNext()) {
      System.out.println(it.next().getValue()); // prints 1234 then 5678
    }

This functionality is very similar to DynamoDB's hash key/range key behavior. Using the hash key, you can group rows together, and then perform range queries within these buckets! Pretty cool, huh?

##### Pluggable Serialization

EZDB allows you to plug in your own serializations. The examples above show IntegerSerde and StringSerde, but you can plug in anything you want. The only thing that you need to be mindful of is how bytes are sorted (lexicographically) when plugging in a custom range serializer, as this will affect the sort order of your range queries.