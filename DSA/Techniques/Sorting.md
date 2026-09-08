## MergeSort
```java
void mergeSort(int[] arr) {  
    mergeSort(arr, 0, arr.length - 1);  
}  
  
void mergeSort(int[] arr, int left, int right) {  
    if (left < right) {  
       int mid = left + (right - left) / 2;  
       mergeSort(arr, left, mid);  
       mergeSort(arr, mid + 1, right);  
       merge(arr, left, mid, right);  
    }  
}  
  
void merge(int[] arr, int left, int mid, int right) {  
    int n1 = mid - left + 1;  
    int n2 = right - mid;  
  
    // Create temp arrays  
    int[] leftArr = new int[n1];  
    int[] rightArr = new int[n2];  
  
    for (int i = 0; i < n1; i++) {  
       leftArr[i] = arr[left+i];  
    }  
    for (int i = 0; i < n2; i++) {  
       rightArr[i] = arr[mid+i+1];  
    }  
  
    int l = 0, r = 0;  
    int k = left;  
    while (l < n1 && r < n2) {  
       if (leftArr[l] <= rightArr[r]) {  
          arr[k] = leftArr[l];  
          l++;  
       } else {  
          arr[k] = rightArr[r];  
          r++;  
       }  
       k++;  
    }  
  
    while (l < n1) {  
       arr[k] = leftArr[l];  
       l++;  
       k++;  
    }  
  
    while (r < n2) {  
       arr[k] = rightArr[r];  
       r++;  
       k++;  
    }  
}
```

### Visualization

```mermaid
---
title: Merge Sort
config:
  theme: default
  look: handDrawn
---
flowchart TD

	A["[3, 2, 1, 5, 4]"]
	A3["[3]"]
	A2["[2]"]
	A1["[1]"]
	A5["[5]"]
	A4["[4]"]
	A --> B["[3, 2, 1]"] --> D["[3, 2]"] & A1
	A --> C["[5, 4]"] --> A5 & A4
	D --> A3 & A2
	%% Sorting
	A3 & A2 --> A23["[2, 3]"]
	A1 & A23 --> A123["[1, 2, 3]"]
	A5 & A4 --> A45["[4, 5]"]
	A45 & A123 --> Sort["[1, 2, 3, 4, 5]"]
	classDef sorted fill:#90EE90,stroke:#2E8B57,stroke-width:2px,color:#000;
	class A23,A123,A45,Sort sorted;
```


### Time Complexity

- Best Case: When array is already sorted - $O(n \log{n})$
- Average Case: When array is randomly ordered - $O(n \log{n})$
- Worst Case: When array is sorted in reverse - $O(n \log{n})$

#### Space complexity

Requires $O(n)$ additional space

---
## Quick Sort

### Visualization
```mermaid
---
title: Merge Sort
config:
  theme: default
  look: handDrawn
---
flowchart TB
subgraph 0
	pivot --> J --> I
end

subgraph 1["i = -1, j = 0"]

A3[3] --- A2[2] --- A1[1] --- A5[5] --- A4[4]

end

subgraph 2["3 < 4; i++, swap(i, j)"]

B3[3] --- B2[2] --- B1[1] --- B5[5] --- B4[4]

end

  

subgraph 3["j++"]

C3[3] --- C2[2] --- C1[1] --- C5[5] --- C4[4]

end

  

subgraph 4["2 < 4; i++, swap(i, j)"]

D3[3] --- D2[2] --- D1[1] --- D5[5] --- D4[4]

end

  

subgraph 5["j++"]

E3[3] --- E2[2] --- E1[1] --- E5[5] --- E4[4]

end

  

subgraph 6["1 < 4; i++, swap(i, j)"]

F3[3] --- F2[2] --- F1[1] --- F5[5] --- F4[4]

end

  

subgraph 7["j++; 5 > 4; proceed"]

G3[3] --- G2[2] --- G1[1] --- G5[5] --- G4[4]

end

  

subgraph 8["j++; 5 > 4; proceed"]

H3[3] --- H2[2] --- H1[1] --- H5[5] --- H4[4]

end

  

subgraph 9["4 < 4; i++, swap(i, j)"]

I3[3] --- I2[2] --- I1[1] --- I5[5] --- I4[4]

end

  

subgraph 10["Final"]

J3[3] --- J2[2] --- J1[1] --- J4[4] --- J5[5]

end

  

0 --> 1 --> 2 --> 3 --> 4 --> 5 --> 6 --> 7 --> 8 --> 9 --> 10

classDef pivot fill:#90EE90,stroke:#2E8B57,stroke-width:2px,color:#000;

classDef j fill:#ffcc80,stroke:#ef6c00,stroke-width:2px;

classDef i fill:#FFF085,stroke:#F0B13B,stroke-width:2px;

class A4,B4,D4,C4,E4,F4,G4,J4,pivot pivot;

class A3,B3,C2,D2,E1,F1,G5,H4,I4,J j

class C3,E2,G1,H1,I5,I i
```
