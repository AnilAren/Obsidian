
| Type      | Access                | Search                | Insert                      | Update                | Delete                |
| --------- | --------------------- | --------------------- | --------------------------- | --------------------- | --------------------- |
| **list**  | O(1) by index         | O(n) by value         | O(1)* (end) / O(n) (middle) | O(1)                  | O(n)                  |
| **tuple** | O(1)                  | O(n)                  | ❌                           | ❌                     | ❌                     |
| **set**   | N/A                   | O(1) avg / O(n) worst | O(1) avg / O(n) worst       | O(1) avg / O(n) worst | O(1) avg / O(n) worst |
| **dict**  | O(1) avg / O(n) worst | O(1) avg / O(n) worst | O(1) avg / O(n) worst       | O(1) avg / O(n) worst | O(1) avg / O(n) worst |

>1.  Why does dict/set have O(1) time complexity?
> 
> “Because it uses a hash table — it computes a hash of the key, which gives a direct index to access the value, leading to O(1) average lookup and insertion time. Collisions are handled efficiently through open addressing and resizing, keeping it near-constant in practice.

In words:
	Collisions happens when two different keys get the same hash key ( because of limited spavce).
	In python colisions are rarely seen beacusae of good hash functions, dynamic resixing of hadh memorey every time hash gets 2/3 rd full python resizes it.also hash uses a salt with random seed so thats why it is rarely possible.
	But if  it happens then if the bucket is full is full it will get the next bucket via deterministic pattern
	Thus we have avg and worst case for set and dict
	Bucket is a slot/place where we store one key value pair in hash table array




