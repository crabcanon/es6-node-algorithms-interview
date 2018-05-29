### 1. Time complexity

时间复杂度两个概念：时间频度和时间复杂度。时间频度指执行算法耗费的时间。时间复杂度指耗费时间所呈现出的规律。在各种算法中，如果算法中语句执行次数为一个常数，时间复杂度为O(1)。计算时间复杂度的具体步骤：

- 找到算法中基本语句，即执行次数最多的语句。
- 计算基本语句执行次数的数量级。只要保证最高次幂正确，忽略系数和其他低次幂，简化算法分析。
- 用大写O记号表示算法的时间性能。常见复杂度有小到大为：O(1) < O(log2n) < O(nlog2n) < O(n^2) < O(n^3) < ... < O(2^n) < O(n!)。

一些基本法则：

- 简单的输入输出或是赋值语句，近似认为O(1)。
- 对于顺序执行的语句，采用『求和法则』。
- 对于选择结构（if/else），主要时间消耗在执行『then』或『else』用的时间，检验条件也需要O(1)时间。
- 对于循环结构，时间耗费在循环迭代和检验循环条件，可用『乘法法则』。
- 对于复杂的算法，可分为几个部分，分别计算。

```javascript
// O(1)
a = 2; a = b; 
```

```javascript
// O(n)
// O(1+1+n+n-1+n-1+n-1) = O(4n-1) = O(n)
a = 0; // O(1)
b = 1; // 0(1)
for (i = 1; i <= n; i++) { // O(n)
  s = a + b; // O(n-1)
  b = a; // O(n-1)
  a = s; // O(n-1)
}
```

```javascript
// O(n^2)
// 第一个for循环时间复杂度为O(n)，第二个为O(n^2)，整个算法复杂度为O(n+n^2) = O(n^2)。
for (i = 1; i < n; i++) x++;
for (i = 1; i < n; i++) {
  for (j = 1; j <= n; j++) x++;
}

// 例子2
// O(2n^2+n+1) = O(n^2)
sum = 0; // O(1）
for (i = 1; i <= n; i++) { // O(n+1)
  for (j = 1; j <= n; j++) { // O(n^2)
    sum++; // O(n^2)
  }
}
```

```javascript
// O(log2n)
i = 1; // O(1)
while (i < n) {
  i = i * 2; // 假设这句的频度是f(n), 2^f(n) <= n; f(n) <= log2n;
}
```

```javascript
// O(n^3)
// O(0+(1-1)*1/2+...+(n-1)*n/2) = O(n(n+1)(n-1)/6) = O(n^3)
for (i = 0; i < n; i++) {
  for (j = 0; j < i; j++) {
    for (k = 0; k < j; k++) {
      x = x + 2;
    }
  }
}
```

> [Array Sorting Algorithms Complexities](http://bigocheatsheet.com/)

### 2. Sort algorithms

- Bubble Sort - [Best: Ω(n) | Worst: O(n^2)]

Compare the two adjacent numbers in order. If not meet the rules, swap their positions. This will make sure the max(or min) number is in the end position of the sequence. Repeat this process(expect for the last number of the sequence).

```javascript
function swap(arr, p1, p2) {
  var tmp = arr[p1];
  arr[p1] = arr[p2];
  arr[p2] = tmp;
}

function bubbleSort(arr) {
  var len = arr.length;
  var i, j, stop;

  for (i = 0; i < len - 1; i++) {
    for(j = 0; stop = len - 1 - i; j < stop; j++) {
      if (arr[j] > arr[j + 1]) {
        swap(arr, j, j + 1);
      }
    }
  }

  return arr;
}
```

- Selection Sort - [Best: Ω(n^2) | Worst: Ω(n^2)]

Similar as bubble sort, but the difference is that it will not swap the positions after each comparation. Instead, it will find the max(or min) number after one round comparation and put it to the correct position and no change for other positions.

```javascript
function swap(arr, p1, p2) {
  var tmp = arr[p1];
  arr[p1] = arr[p2];
  arr[p2] = tmp;
}

function selectionSort(arr) {
  var len = arr.length;
  var i, j, min;

  for (i = 0; i < len - 1; i++) {
    min = i;

    for (j = i + 1; j < len - 1; j++) {
      if (arr[j] < arr[min]) {
        min = j;
      }
    }

    if (i !== min) {
      swap(arr, i, min);
    }
  }

  return arr;
}
```

- Insertion Sort - [Best: Ω(n) | Worst: O(n^2)]

It divides the array into two parts: sorted and unsorted. In the beginning, the sorted part only contains one element, and we need to insert the next element from unsorted part to sorted part(compare these two elements and adjust their positions). Therefore, one element will be added to sorted part and removed from unsorted part each time.

```javascript
function insertionSort(arr) {
  var len = arr.length;
  var i, j, value;

  for (i = 0; i < len - 1; i++) {
    value = arr[i];

    for (j = i - 1; j > -1 && arr[j] > value; j--) {
      arr[j+1] = arr[j];
    }

    arr[j+1] = value;
  }
  return arr;
}
```

- Merge Sort - [Best: Ω(n log(n)) | Worst: O(n log(n))]

The basic idea is to combine two sorted arrays. Therefore, the original array could be divided into n arrays and each of them only contains one element, then merge each two of them till the end.

```javascript
function merge(left, right) {
  var result = [],
  var il = 0;
  var ir = 0;

  while (il < left.length && ir < right.length) {
    if (left[il] < right[ir]) {
      result.push(left[il++]);
    } else {
      result.push(right[ir++]);
    }
  }

  return result.concat(left.slice(il)).concat(right.slice(ir));
}

function mergeSort(arr) {
  if (arr.length < 2) {
    return arr;
  }

  var middle = Math.floor(arr.length / 2);
  var left = arr.slice(0, middle);
  var right = arr.slice(middle);

  // return merge(mergeSort(left), mergeSort(right));
  var params = merge(mergeSort(left), mergeSort(right));
  params.unshift(0, arr.length);
  arr.splice.apply(arr, params);
  return arr;
}
```

- Quick Sort - [Best: Ω(n log(n)) | Worst: O(n^2)]

Define a pivot and the original array will be divided into two partitions based on this pivot. Then define two pointers, one points to first element of the left partition while the other one points to last element of the right partition. Compare left pointer's value with pivot's value, if smaller, move the left pointer to the next position, otherwise, stay still. Compare right pointer's value with pivot's value, if greater, move the right pointer to the previous position, otherwise, stay still. Compare positions of left and right pointers. If left pointer's position is greater than right pointer's position, this round sort stops, otherwise, swap their values. Repeat this process for left and right partitions till the end. Finally, all the smaller elements will be put on the left partition, while all the greater elements will be put on the right partition.

```javascript
function swap(arr, i1, i2) {
  var tmp = arr[i1];
  arr[i1] = arr[i2];
  arr[i2] = tmp;
}

function partition(arr, left, right) {
  var pivot = arr[Math.floor((right + left) / 2)];
  var i = left;
  var j = right;

  while (i <= j) {
    while (arr[i] < pivot) {
      i++;
    }

    while (arr[j] > pivot) {
      j--;
    }

    if (i <= j) {
      swap(arr, i, j);
      i++;
      j--;
    }
  }

  return i;
}

function quickSort(arr, left, right) {
  if (arr.length < 2) return arr;

  left = (typeof left !== 'number' ? 0 : left);
  right = (typeof right !=='number' ? arr.length - 1 : right);

  var index = partition(arr, left, right);

  if (left < index - 1) {
    quickSort(arr, left, index - 1);
  }

  if (index < right) {
    quickSort(arr, index, right);
  }

  return arr;
}
```

- Bucket Sort - [Best: Ω(n+k) | Worst: O(n^2)]

Create an array of initially empty "buckets"(a new two-dimensional array). Go over the original array and put each item in its bucket, which means push item to the second-level array(bucket) whose key in the first-level array is same as that item's Math.floor(value). Sort each non-empty bucket if not sure all the items are integers by using other sorting algorithms or by recursively applying the bucket sort. Finally, flatten this two-dimensional array(remove empty buckets at the same time).

```javascript
/**
 * Input: [5.6, 5.2, 5.1, 1, 3, 2, 6, 8, 4, 3, 2]
 * Output: [undefined, [1], [2, 2], [3, 3], [4], [5.6, 5.2, 5.1], [6], undefined, [8]]
 *
 * This function is very useful if all the items are integers in an array. Example:
 * https://www.codewars.com/kata/reviews/5834316306f227a6ac00009b/groups/58d240d9fcafd414f2001b59
 */
function createBuckets(arr) {
  let buckets = [];
  let current;

  for (let i = 0; i < arr.length; i++) {
    current = Math.floor(arr[i]);
    buckets[current] = buckets[current] || [];
    buckets[current].push(current);
  }

  return buckets;
}

function bucketSort(arr) {
  let result = [];
  let buckets = createBuckets(arr);

  for (let i = 0; i < buckets.length; i++) {
    if (buckets[i] !== undefined && buckets[i].length > 1) {
      // MergeSort, QuickSort...
      quickSort(buckets[i]);
    }
  }

  for (let j of buckets) {
    if (j !== undefined) result = result.concat(j);
  }

  return result;
}
```

### 3. Search algorithms

- QuickSelect - [Best: O(n) | Worst: O(n^2)]

Select a random element from the list(pivot), and place every element that is smaller to the left half of the array, and every element that is equal or greater to the right half of the array. As we know the location of pivot, we could easily address whether our target is on the left or right side of the array and then only recurses into one side. This actually reduces the average complexity from O(nlogN) to O(N).

```javascript

/**
 * Source: https://github.com/mourner/quickselect/blob/master/index.js
 *
 * @param {Array} arr: the array to partially sort (in place).
 * @param {Number} k: middle index for partial sorting (as defined above).
 * @param {Number} left: left index of the range to sort (0 by default).
 * @param {Number} right: right index (last index of the array by default).
 * @param {Func} compare: compare function.
 * @return Returns n-th smallest element.
 *
 * Example:
 * var arr = [65, 28, 59, 33, 21, 56, 22, 95, 50, 12, 90, 53, 28, 77, 39];
 * quickselect(arr, 8);
 * arr is [39, 28, 28, 33, 21, 12, 22, 50, 53, 56, 59, 65, 90, 77, 95]
 *                                         ^^ middle index
 *
 * Arr = [5 1 4 3 2]
 * Pivot = [4]
 *
 * Steps:
 * swap [5] and [2] as 5>=4 and 2<
 * [2 1 4 3 5]
 *
 * swap [4] and [3] as 4>=4 and 3<4
 * [2 1 3 4 5]
 *
 * When we finish with the first iteration, we know the followings:
 * All elements <4 are on the left of 4
 * All elements >=4 are on the right of 4 (including the 4 itself)
 *
 * So, if we are looking for the first 3 elements, we can stop, we found them.
 * If we are looking for the 3rd element, we need more iteration
 * but we know we must look for it in the first half of the array hence we can ignore the rest.
 */
module.exports = function (arr, k, left, right, compare) {
  quickselect(arr, k, left || 0, right || (arr.length - 1), compare || defaultCompare);
};

function quickselect(arr, k, left, right, compare) {
  while (right > left) {
    if (right - left > 600) {
      var n = right - left + 1;
      var m = k - left + 1;
      var z = Math.log(n);
      var s = 0.5 * Math.exp(2 * z / 3);
      var sd = 0.5 * Math.sqrt(z * s * (n - s) / n) * (m - n / 2 < 0 ? -1 : 1);
      var newLeft = Math.max(left, Math.floor(k - m * s / n + sd));
      var newRight = Math.min(right, Math.floor(k + (n - m) * s / n + sd));
      quickselect(arr, k, newLeft, newRight, compare);
    }

    var t = arr[k];
    var i = left;
    var j = right;

    swap(arr, left, k);
    if (compare(arr[right], t) > 0) swap(arr, left, right);

    while (i < j) {
      swap(arr, i, j);
      i++;
      j--;
      while (compare(arr[i], t) < 0) i++;
      while (compare(arr[j], t) > 0) j--;
    }

    if (compare(arr[left], t) === 0) swap(arr, left, j);
    else {
      j++;
      swap(arr, j, right);
    }

    if (j <= k) left = j + 1;
    if (k <= j) right = j - 1;
  }
}

function swap(arr, i, j) {
  var tmp = arr[i];
  arr[i] = arr[j];
  arr[j] = tmp;
}

function defaultCompare(a, b) {
  return a < b ? -1 : a > b ? 1 : 0;
}
```

- Recursive Binary Search - [Best: O(1) | Worst: O(logN)]

Search started from the middle element of an array. If the middle element is the target, return. Otherwise, if your target is greater than the middle element, restart the search process from the middle element of right(or left) partition, and repeat this process till the target is found(recursive).

```javascript
function recursiveBinarySearch(arr, key, left, right) {
  if (left > right) {
    return -1;
  }

  var middle = Math.floor((right + left) / 2);

  if (arr[middle] === key) {
    return middle;
  } else if (arr[middle] > key) {
    return recursiveBinarySearch(arr, key, left, middle - 1);
  } else {
    return recursiveBinarySearch(arr, key, middle + 1, right);
  }
}
```

### 4. Binary Search Tree

For each node X in the binary search tree, all the values of its children in the left partition are smaller than X's value, while all the values of its children in the right partition are greater than X's value.

```javascript
function Node(value, left, right, parent) {
  this.value = value;
  this.left = left;
  this.right = right;
  this.parent = parent;
}

//var node = new Node(value, left, right, parent);

// Finds the node with minimum value in given sub-tree
function findMin(node, current) {
  current = current || { value: Infinity };

  if (!node) {
    return current;
  }

  if (current.value > node.value) {
    current = node;
  }

  return findMin(node.left, current);
}

// Finds the node with maximum value in given sub-tree
function findMax(node, current) {
  current = current || { value: -Infinity };

  if (!node) {
    return current;
  }

  if (current.value < node.value) {
    current = node;
  }

  return findMax(node.right, current);
}
```
  
