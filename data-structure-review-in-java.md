---
description: 'Array, Linked List, Stack, Queen, Hashes, Tree'
---

# data structure review in java

## Array and ArrayList

## Linked List

## Stack

## Queen

## Hashes

### description

a data structure which organize data using hash functions in order to support quick insertion and search.

There are two different kinds of hash tables: hash set and hash map.

the hashset is a set, which has no repeat value in set

the hashmap is  map which insert key can find the responding value. Actually, it will translate key into a hashcode and then find the value to key.

### what is the principle of hash table

In order to support quick insertion and search, the hash function the key principle.

#### hash function

By using hash function, the search space of key can be narrow, thus, the responding value can be found quickly.

One of the hash function is module function: hashcode % table size= hash \(we could imagine the hash  is a numbers of the buckets \). Basically, both hashcode1 and hashcode2 could in the same bucket if they get same number when module by a int number.

Thus, if hashcode is 1-50, and table size is 50, then every hashcode will be store in 0-49 buckets respectively.

#### collision

If there are hashes in the same bucket,  that is collision.

How to deal with collision

* 
### how to design a hash

#### hashset

1. put key into which bucket
2. check if bucket has that key
   1. add: if bucket don't have, then add
   2. remove: if bucket have key, then remove
   3. return have or not have

```java
class MyHashSet {
    private final int MAX_LEN = 100000; // the amount of buckets
    private List<Integer>[] set;      // hash set implemented by array
    
    /** Returns the corresponding bucket index. */
    private int getIndex(int key) {
        return key % MAX_LEN;
    }
    
    /** Search the key in a specific bucket. Returns -1 if the key does not existed. */
    private int getPos(int key, int index) {
        // Each bucket contains a list.
        List<Integer> temp = set[index];
        if (temp == null) {
            return -1;
        }
        // Iterate all the elements in the bucket to find the target key.
        for (int i = 0; i < temp.size(); ++i) {
            if (temp.get(i) == key) {
                return i;
            }
        }
        return -1;
    }
    
    /** Initialize your data structure here. */
    public MyHashSet() {
        set = (List<Integer>[])new ArrayList[MAX_LEN];
    }
    
    public void add(int key) {
        int index = getIndex(key);
        int pos = getPos(key, index);
        if (pos < 0) {
            // Add new key if key does not exist.
            if (set[index] == null) {
                set[index] = new ArrayList<Integer>();
            }
            set[index].add(key);
        }
    }
    
    public void remove(int key) {
        int index = getIndex(key);
        int pos = getPos(key, index);
        if (pos >= 0) {
            // Remove the key if key exists.
            set[index].remove(pos);
        }
    }
    
    /** Returns true if this set did not already contain the specified element */
    public boolean contains(int key) {
        int index = getIndex(key);
        int pos = getPos(key, index);
        return pos >= 0;
    }
}

/**
 * Your MyHashSet object will be instantiated and called as such:
 * MyHashSet obj = new MyHashSet();
 * obj.add(key);
 * obj.remove(key);
 * boolean param_3 = obj.contains(key);
 */
```

### function

### hash in java

### important question

## Tree

