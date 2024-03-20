#include <iostream>
#include <thread>
#include <vector>
#include <numeric>

// Function to calculate sum of array elements in a given range
int sumRange(const std::vector<int>& arr, int start, int end) {
    int sum = 0;
    for (int i = start; i < end; ++i) {
        sum += arr[i];
    }
    return sum;
}

// Function to be executed by each thread
void threadFunction(const std::vector<int>& arr, int start, int end, int& result) {
    result = sumRange(arr, start, end);
}

int main() {
    // Initialize an array with a large number of elements
    constexpr int ARRAY_SIZE = 1000000; // Change this value as needed
    std::vector<int> arr(ARRAY_SIZE);
    std::iota(arr.begin(), arr.end(), 1); // Fill the array with values 1, 2, 3, ..., ARRAY_SIZE

    // Define the number of threads
    constexpr int NUM_THREADS = 4; // Change this value as needed

    // Calculate segment size for each thread
    int segmentSize = ARRAY_SIZE / NUM_THREADS;

    // Vector to store thread objects
    std::vector<std::thread> threads(NUM_THREADS);

    // Vector to store partial sums
    std::vector<int> partialSums(NUM_THREADS);

    // Create threads and assign them segments of the array
    for (int i = 0; i < NUM_THREADS; ++i) {
        int start = i * segmentSize;
        int end = (i == NUM_THREADS - 1) ? ARRAY_SIZE : (start + segmentSize);
        threads[i] = std::thread(threadFunction, std::ref(arr), start, end, std::ref(partialSums[i]));
    }

    // Join threads
    for (int i = 0; i < NUM_THREADS; ++i) {
        threads[i].join();
    }

    // Compute total sum
    int totalSum = std::accumulate(partialSums.begin(), partialSums.end(), 0);

    // Display the total sum
    std::cout << "Total sum of the array: " << totalSum << std::endl;

    return 0;
}
