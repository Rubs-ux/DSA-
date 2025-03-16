# DSA assignment/project-
#include <iostream>
#include <vector>
#include <algorithm>
#include <random>
#include <chrono>
#include <unordered_set>

using namespace std;
using namespace chrono;  

// ===================== SORTING ALGORITHMS =====================

void selectionSort(vector<int>& arr) {
    for (size_t i = 0; i < arr.size() - 1; i++) {
        size_t minIdx = i;
        for (size_t j = i + 1; j < arr.size(); j++) {
            if (arr[j] < arr[minIdx]) minIdx = j;
        }
        swap(arr[i], arr[minIdx]);
    }
}

void merge(vector<int>& arr, int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;
    vector<int> L(n1), R(n2);

    for (int i = 0; i < n1; i++) L[i] = arr[left + i];
    for (int i = 0; i < n2; i++) R[i] = arr[mid + 1 + i];

    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j]) arr[k++] = L[i++];
        else arr[k++] = R[j++];
    }

    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];
}

void mergeSort(vector<int>& arr, int left, int right) {
    if (left >= right) return;
    int mid = left + (right - left) / 2;
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, mid, right);
}

// ===================== SEARCHING ALGORITHMS =====================

int binarySearch(const vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] == target) return mid;
        else if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}

int interpolationSearch(const vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;
    while (left <= right && target >= arr[left] && target <= arr[right]) {
        if (arr[left] == arr[right]) {
            if (arr[left] == target) return left;
            return -1;
        }
        int pos = left + ((right - left) * (target - arr[left])) / (arr[right] - arr[left]);
        if (pos < left || pos > right) return -1;

        if (arr[pos] == target) return pos;
        if (arr[pos] < target) left = pos + 1;
        else right = pos - 1;
    }
    return -1;
}

// ===================== UTILITY FUNCTIONS =====================

void printArray(const vector<int>& arr) {
    for (int num : arr) {
        cout << num << " ";
    }
    cout << "\n";
}

void generateUniqueRandomNumbers(vector<int>& arr, int N) {
    unordered_set<int> uniqueNumbers;
    random_device rd;
    mt19937 gen(rd());
    uniform_int_distribution<> distrib(1, N + 1); // Generate numbers in range [1, N+1]

    while (uniqueNumbers.size() < arr.size()) {
        uniqueNumbers.insert(distrib(gen));
    }

    arr.assign(uniqueNumbers.begin(), uniqueNumbers.end());
    shuffle(arr.begin(), arr.end(), gen);
}

void displayTime(long long duration) {
    cout << "Execution Time: " << duration << " microsecond" << (duration == 1 ? "" : "s") << "\n";
}

// ===================== MAIN FUNCTION =====================

int main() {
    int N, target, choice;
    
    cout << "Enter number of elements: ";
    cin >> N;

    if (N <= 0) {
        cout << "Invalid input! Exiting...\n";
        return 1;
    }
    
    vector<int> numbers(N);
    generateUniqueRandomNumbers(numbers, N);

    cout << "Generated array: \n";
    printArray(numbers);

    cout << "Choose sorting algorithm (1 for Merge Sort, 2 for Selection Sort): ";
    cin >> choice;

    auto startSort = high_resolution_clock::now();

    if (choice == 1) {
        mergeSort(numbers, 0, numbers.size() - 1);
    } else if (choice == 2) {
        selectionSort(numbers);
    } else {
        cout << "Invalid choice! Exiting...\n";
        return 1;
    }

    auto endSort = high_resolution_clock::now();
    long long sortDuration = duration_cast<microseconds>(endSort - startSort).count();
    cout << "Sorted array: \n";
    printArray(numbers);
    displayTime(sortDuration);

    cout << "Enter target number to search: ";
    cin >> target;

    auto startBS = high_resolution_clock::now();
    int index = binarySearch(numbers, target);
    auto endBS = high_resolution_clock::now();
    long long bsDuration = duration_cast<microseconds>(endBS - startBS).count();

    cout << "Binary Search Result: " << (index != -1 ? "Found at index " + to_string(index) : "Not found") << endl;
    displayTime(bsDuration);

    auto startIS = high_resolution_clock::now();
    index = interpolationSearch(numbers, target);
    auto endIS = high_resolution_clock::now();
    long long isDuration = duration_cast<microseconds>(endIS - startIS).count();

    cout << "Interpolation Search Result: " << (index != -1 ? "Found at index " + to_string(index) : "Not found") << endl;
    displayTime(isDuration);

    return 0;
}
