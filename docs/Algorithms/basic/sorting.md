# Sorting Algorithms

## Insertion Sort

  ![](../img/insertion_sort.png)

```cpp
INSERTION-SORT(A)
    for j = 2 to A.length
        key = A[j]
        //Insert A[j] into the sorted sequence A[1...j-1]
        i = j - 1
        while i > 0 and A[i] > key
            A[i + 1] = A[i]
            i = i - 1
        A[i + 1] = key
```
Worst case running time is a quadratic function. Running time: $\Theta(n^2)$.

## Bubble Sort

```cpp
BUBBLESORT(A)
    for i = 1 to A.length - 1
        for j = A.length downto i + 1
            if A[j] < A[j - 1]
                exchange A[j] with A[j - 1]
```

## Selection Sort

```cpp

void selectionSort(int arr[], int n) 
{ 
    int i, j, min_idx; 
  
    // One by one move boundary of unsorted subarray 
    for (i = 0; i < n-1; i++) 
    { 
        // Find the minimum element in unsorted array 
        min_idx = i; 
        for (j = i+1; j < n; j++) 
        if (arr[j] < arr[min_idx]) 
            min_idx = j; 
  
        // Swap the found minimum element with the first element 
        swap(&arr[min_idx], &arr[i]); 
    } 
} 
```

## Quick Sort



## Merge Sort

## Heap Sort

  ![](../img/heap1.png)

  ![](../img/heap2.png)

  ![](../img/heap3.png) 

## Couting Sort

## Radix Sort
