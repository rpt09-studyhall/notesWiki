
![](https://dzwonsemrish7.cloudfront.net/items/1b0I0b1y1G0c0h0e1t35/recursion.png "Recursion be like")

With recursive functions, most people focus on the fact that any recursive function needs to have two parts:

1. A base case, (or cases) where the function can return the result immediately
2. A recursive case, where the function _calls itself_ to break the current problem down: to work on a part of the problem that has a similar structure to the larger problem.

Two common examples:

Super basic:

```javascript
var randm = function(n) {
  if (n === 0) {
    return;
  }
  randm(n - 1);
};
```

Factorial:

```javascript
var factorial = function(n) {
  // base case
  if (n === 1 || n === 0) {
    return 1;
  }
  // recursive case
  return n * factorial(n - 1);
};
```

## <a name="theKey"></a> The key thought process

How can the recursive case be structured to accomplish what you want? Here's one author's thought process:

1. Break the problem down into a problem that is one step simpler
2. Assume that my function will work to solve the simpler problem  —  really believe it beyond any doubt
3. Ask: Since I know I can solve the simpler problem, how would I solve the more complex problem?

Example: recursive function to reverse a string:

```javascript
function reverse(string) {
  // Do recursive magic here
}
```

What would our base case be? Empty or 1 character strings can just return:

```javascript
function reverse(string) {

  // base case
  if (string.length < 2) return string;

}
```

Follow thought process above for the rest:

1. We're trying to reverse a string, a problem one step simpler would be to reverse a string that's _one letter shorter_.
2. We need to believe that our function can do the job.
3. How can we reverse the whole string? We can take all the characters except the first one, reverse those, then add the first character onto the end:

```javascript
function reverse(string) {
  // base case
  if (string.length < 2) return string;

  // recursive case
  return reverse(string.slice(1, string.length)) + string[0];
}
```

Don't think about how to break the problem down _all the way_ to the base case - that's the _function's job_. Just think about a problem that is _one step simpler_ that the problem we're really trying to solve.

Try to do simple things with recursion that you would normally do with loops. Try:

* Go through an array and print out each element
* Determine if a string is a palindrome
* Calculate _a_ raised to the power of _b_
* Or, try implementing _.map_ without loops

## Recursive data structures

Basically, a data structure that contains smaller versions of the same structure inside itself. Trees, linked lists, binary search trees, or objects that contain objects as properties are examples.

```javascript
var anObject = {
  name: {
    firstName: 'Jane',
    lastName: 'Doe',
  },
  favorites: {
    foods: [
      'kale',
      'doughnuts'
    ]
  }
}
```

A binary search tree example: each node has two child nodes, "left" and "right". The value of left must be less than the value of the parent node, and the value of the right child node must be greater than the value of the parent node.

![](http://i.imgur.com/eDw57vR.png)

A node could look like:

```javascript
function BinaryTreeNode(value) {
  this.value = value;
  this.left = null;
  this.right = null;
}
```

And a method to add a value to the tree:

```javascript
BinaryTreeNode.prototype.add = function(value) {
  // do stuff to add a value to the tree
}
```

Invoking this method repeatedly on the top node will let us build up a complete binary tree.

Using the [thought process](#theKey) strategy from above:

1. Break it down one level: add a value to the binary tree. One step smaller would be to add a new value to the _left or right sub-tree_ of the main tree - they're _smaller_.
2. Keep the faith.
3. _Build it up_: since our function can add a value to either the right or left sub-tree, we just need to decide if the value should be added to the right or left. Less than parent goes left, greater than parent goes right. Null sub-tree values get empty nodes (nodes to fill the space), and if it's not null, we _recursively call_ the method again to add the value to the appropriate sub-tree.

```javascript
function BinaryTreeNode(value) {
  this.value = value;
  this.left = null;
  this.right = null;
}

BinaryTreeNode.prototype.add = function(value) {
  // do stuff to add a value to the tree
  if (value < this.value) {
    if (this.left === null) {
      this.left = new BinaryTreeNode(value);
    } else {
      this.left.add(value);
    }
  }

  if (value > this.value) {
    if (this.right === null) {
      this.right = new BinaryTreeNode(value);
    } else {
      this.right.add(value);
    }
  }
}
```

Another string reverse example:

```javascript
function otherReverse(str) {
  if (str.length <= 1) {
    return str;
  }
  
  return otherReverse(str.substr(1)) + str[0];
}
```

Say we pass `'bar'` to our `otherReverse` function:

1. `reverse('bar')` is `reverse('ar') + 'b'`
2. `reverse('ar')` is `reverse('r') + 'a'`
3. `reverse('r')` meets our if condition, so it's just `'r'`

When the call stack unwinds, we end up with `'r' + 'a' + 'b'`.

* * *

**Sources**:

1. [Learning to think with recursion, part 1](https://medium.com/@daniel.oliver.king/getting-started-with-recursion-f89f57c5b60e)
2. [Learning to think with recursion, part 2](https://medium.com/@daniel.oliver.king/learning-to-think-with-recursion-part-2-887bd4c41274)
3. [Recursion in JavaScript](https://medium.freecodecamp.org/recursion-in-javascript-1608032c7a1f)
