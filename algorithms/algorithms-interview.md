### 1. Sort algorithms

> [Array Sorting Algorithms Complexities](http://bigocheatsheet.com/)

- Bubble Sort - [Best: Ω(n) | Worst: O(n^2)]

> Compare the two adjacent numbers in order. If not meet the rules, swap their positions. This will make sure the max(or min) number is in the end position of the sequence. Repeat this process(expect for the last number of the sequence).

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

> Similar as bubble sort, but the difference is that it will not swap the positions after each comparation. Instead, it will find the max(or min) number after one round comparation and put it to the correct position and no change for other positions.

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

> It divides the array into two parts: sorted and unsorted. In the beginning, the sorted part only contains one element, and we need to insert the next element from unsorted part to sorted part(compare these two elements and adjust their positions). Therefore, one element will be added to sorted part and removed from unsorted part each time.

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

- Mergesort - [Best: Ω(n log(n)) | Worst: O(n log(n))]

> The basic idea is to combine two sorted arrays. Therefore, the original array could be divided into n arrays and each of them only contains one element, then merge each two of them till the end.

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

- Quicksort - [Best: Ω(n log(n)) | Worst: O(n^2)]

> Define a pivot, and put all the smaller elements on the left side while all the larger elements on the right of this pivot. Repeat this process for both sides till finish.

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

### 2. Search algorithms

- Recursive Binary Search - O(logN)

> Search started from the middle element of an array. If the middle element is the traget, return. Otherwise, if your target is larger than the middle element, restart the search process from the middle element of right(or left) partition, and repeat this process till the target is found(recursive).

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

### 3. Binary Search three

> For each node X in the binary search tree, all the values of its children in the left partition are smaller than X's value, while all the values of its children in the right partition are larger than X's value.

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
