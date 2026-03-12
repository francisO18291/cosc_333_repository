# cosc_333_repository
Analysis and Design Of Algorithms Assignment

Name: Francis Miriithi
Reg No: EB3/67225/23

Algorithm Description: Spreadsort

Spreadsort is a high-performance hybrid sorting algorithm. It works by,
 1. Distribution: Partitioning the data into "buckets" based on the most significant bits (or range) of the integers.
 2. Refinement: If buckets are large, it recursively applies the distribution or switches to a comparison sort (like Insertion Sort) for small clusters.
 3. Efficiency: It avoids the O(n^2) trap of Bubble Sort by using the numerical value of the data to determine its position, aiming for O(n) performance on distributed data.


 EXAMPLE CODE

#include <iostream>
#include <vector>
#include <algorithm>
#include <cmath>

struct Stats {
    long long comparisons = 0;
    long long swaps = 0;
};

void spreadSortDescending(std::vector<int>& arr, Stats& stats) {
    if (arr.size() < 2) return;

  // Find range to determine spread
    int minVal = arr[0], maxVal = arr[0];
    for (int x : arr) {
        if (x < minVal) minVal = x;
        if (x > maxVal) maxVal = x;
    }

  if (minVal == maxVal) return;

  // Create buckets based on the range of values
    int n = arr.size();
    int bucketCount = std::sqrt(n); 
    std::vector<std::vector<int>> buckets(bucketCount);

  // Distribute elements into buckets
    for (int x : arr) {
        stats.comparisons++; 
        int bucketIdx = (long long)(x - minVal) * (bucketCount - 1) / (maxVal - minVal);
        buckets[bucketIdx].push_back(x);
    }

  // Sort buckets individually (descending) and merge
    arr.clear();
    for (int i = bucketCount - 1; i >= 0; i--) {
        // Using a basic insertion sort for small buckets to track stats
        for (size_t j = 1; j < buckets[i].size(); j++) {
            int key = buckets[i][j];
            int k = j - 1;
            while (k >= 0 && buckets[i][k] < key) {
                stats.comparisons++;
                buckets[i][k + 1] = buckets[i][k];
                stats.swaps++;
                k--;
            }
            if (k >= 0) stats.comparisons++;
            buckets[i][k + 1] = key;
        }
        for (int x : buckets[i]) arr.push_back(x);
    }
}

How Spreadsort Works

Spreadsort works by partitioning the data space. Instead of just comparing two numbers, it looks at the value of the number to decide where it belongs.
 1. Find the Range: It identifies the minimum and maximum values in the list.
 2. Calculate the Spread: It divides that range into "bins" or "buckets."
 3. Distribution: Each number is placed into a bucket based on its value. High values go into the first buckets (for descending order), and low values go into the last.
 4. Recursive/Local Sort: If a bucket has multiple items, it sorts them (often using a simpler sort or another round of Spreadsort).
 5. Concatenation: It joins the buckets back together into one sorted list.

Step-by-Step Example

List: [10, 80, 40] (Descending)

 1. Identify Range: Min = 10, Max = 80.
 2. Create Buckets: Let's say we use 2 buckets. Bucket A (high), Bucket B (low).
 3. Distribute: * 80 goes to Bucket A.
    40 goes to Bucket A.
    10 goes to Bucket B.
 4. Sort Buckets: Bucket A becomes [80, 40].
 5. Merge: [80, 40, 10].

 Complexity Analysis
 
 1. Time Complexity
Spreadsort is extremely efficient for integers because it approaches linear time.

 1. Best Case: O(n) When data is uniformly distributed, each item goes straight to its spot.
 2. Average Case: Generally faster than O(n \log n) for large integer sets.
 3. Worst Case: If all items fall into one bucket, it falls back to a standard comparison sort.

    Space Complexity
    
The space complexity is O(n).
Spreadsort requires extra memory to create the buckets and store the distributed elements before merging them back.
  
   Performance on Large Lists
   
Spreadsort can handle the 1.78 million items test case.

  Small lists (1–999): Extremely fast (nanoseconds).
  Large lists (89 thousands plus): The efficiency of the distribution means you aren't doing billions of swaps; you are mostly just moving each item once or twice.
  1.78 Million: Should complete in a few seconds on a modern machine, as opposed to other sort algorithm such as bubble which could take much longer.
