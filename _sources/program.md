# Program

Here are all the code files:

main.cpp:
```c++
#include "treap.h"
#include "interval_tree.h"
#include <iostream>
#include <vector>
#include <string>

// represents an employee with a name and total hours worked
struct Employee{
    string name;
    int totalHoursWorked;
};

// insertion for the bucket sort
// @param input: The vector of Employee objects to be sorted
void insertion(std::vector<Employee>& input){
    int size = input.size();
    for (int i = 1; i < size; i++){
        Employee key = input[i];
        int j = i - 1;
	// move elements that are greater than key to one position ahead of their current position
        while (j >= 0 && input[j].totalHoursWorked > key.totalHoursWorked){
            input[j + 1] = input[j];
            j--;
        }
        input[j + 1] = key;
    }
}

// does bucket sort on a vector of employee objects based on total hours worked
// @param input: The vector of Employee objects to be sorted
// @param maxVal: The maximum total hours worked
// @param minValue: The minimum total hours worked
void bucketSort(std::vector<Employee>& input, int maxVal, int minValue){
    int size = input.size();
    // creates buckets of size for sorting
    std::vector<std::vector<Employee> > buckets(size);

    // distribute employees into appropriate buckets
    for (int i = 0; i < size; i++){
        int bucketI = (input[i].totalHoursWorked - minValue) * size / (maxVal - minValue + 1);
        buckets[bucketI].push_back(input[i]);
    }

    // sort each bucket individually using insertion sort
    for (int i = 0; i < size; i++){
        insertion(buckets[i]);
    }

    // put the sorted buckets into the original vector
    int index = 0;
    for (int i = 0; i < size; i++){
        for (int j = 0; j < buckets[i].size(); j++){
            input[index++] = buckets[i][j];
        }
    }
}

// main function for the Point of Sale (POS) system
// - Initializes necessary data structures
// - Handles user interactions with the system
int main() {
    std::cout << "POS System Initializing..." << std::endl;

    // creates an interval tree for time clock and a treap for item counts
    IntTree* timeClock = new IntTree();
    Treap* itemCounts = new Treap();

    // variables to track the max and min shift length for bucket sorting
    int maxValue = -1;
    int minValue = 1000000;

    // vector to store shift information
    std::vector<Employee> shiftLens;

    string pass = "1";

    // user interaction with the POS system
    while(pass != "0") {
        std::cout << "Please enter your password, or type 0 to quit program: " << std::endl;
        std::cin >> pass;

        bool breakKey = false;

        while (!breakKey) {
            if (pass == "employee") {

                string numEm;

                std::cout << "Please choose a function by typing the corresponding number:" << std::endl;
                std::cout << "1: Record a Shift" << std::endl;
                std::cout << "2: Record Stock Numbers" << std::endl;
                std::cout << "3: End Session" << std::endl;

                std::cin >> numEm;

                if (numEm == "1") {
		    // a new shift
                    string beginStr;
                    string endStr;
                    Employee newEmployee;
                    int begin;
                    int end;

                    std::cout << "Please enter your name:" << std::endl;
                    std::cin >> newEmployee.name;

                    std::cout << "Please enter the length of your shift (in minutes):" << std::endl;
                    std::cin >> newEmployee.totalHoursWorked;

		    // update max and min shift times for bucket sorting
                    if(newEmployee.totalHoursWorked > maxValue){
                        maxValue = newEmployee.totalHoursWorked;
                    }
                    if(newEmployee.totalHoursWorked < minValue){
                        minValue = newEmployee.totalHoursWorked;
                    }

                    shiftLens.push_back(newEmployee);

		    // sort the shifts using bucket sort
                    bucketSort(shiftLens, maxValue, minValue);

                    std::cout << "Please enter the start time of your shift.  Format should be military time with no "
                                 "colon.  " << std::endl;
                    std::cout << "Example: 2:43 pm should be entered as 1443." << std::endl;

		    // records the start and end times of the shift
                    std::cin >> beginStr;
                    begin = std::stoi(beginStr);

                    std::cout << "Enter the end time in the same format." << std::endl;

                    std::cin >> endStr;
                    end = std::stoi(endStr);

		    // insert the shift into the interval tree
                    timeClock->insert(std::make_pair(begin, end));
                }
                else if (numEm == "2") {
                    // a new stock number
		    string item;
                    int count;
                    std::cout << "Please enter the name of the item you would like to record: " << std::endl;
                    cin >> item;

                    std::cout << "Please enter the current quantity of the item: " << std::endl;
                    cin >> count;

		    // inserts item counts into the treap
                    itemCounts->insert(item, count);
                }
                else if (numEm == "3") {
                    std::cout << "Have a Nice Day!" << std::endl;
                    breakKey = true;
                }
                else {
                    std::cout << "You have entered an invalid choice" << std::endl;
                    breakKey = true;
                }
            }
            else if (pass == "manager") {

                string numMan;

                std::cout << "Please choose a function by typing the corresponding number:" << std::endl;
                std::cout << "1: Overlapping Shift Search" << std::endl;
                std::cout << "2: Check Stock Numbers" << std::endl;
                std::cout << "3: Find Longest Working Employee" << std::endl;
                std::cout << "4: Quit Program" << std::endl;

                std::cin >> numMan;

                if (numMan == "1") {
		    // checks for overlapping shifts
                    vector<pair<int, int> > intervalList;
                    int data;

                    std::cout << "Please enter the hour you would like to see shift overlaps for as a whole number:"
                    << std::endl;
                    std::cin >> data;

		    // get overlapping shifts
                    timeClock->overlap(data, intervalList);

                    if(!intervalList.empty()){
                        std::cout << "The shifts that overlap with the given time are:" << std::endl;

                        for(int i = 0; i < intervalList.size(); ++i){
                            std::cout << intervalList[i].first << " to " << intervalList[i].second << std::endl;
                        }
                    }
                    else{
                        std::cout << "There are no overlapping shifts stored" << std::endl;
                    }

                }
                else if (numMan == "2") {
                    // check stock numbers
		    if(!itemCounts->root){
                        std::cout << "There are currently no items stored." << std::endl;
                    }
                    else{
                        std::cout << "The item with the highest stock is " << itemCounts->root->key
                        << " with a stock number of " << itemCounts->root->priority << "." << std::endl;
                    }
                }
                else if (numMan == "3") {
		    // find the longest working employee
                    if(!shiftLens.empty()){
                        std::cout << "The longest working employee is " << shiftLens[shiftLens.size()-1].name << " with "
                                  << shiftLens[shiftLens.size()-1].totalHoursWorked << " minutes worked." << std::endl;
                    }
                    else{
                        std::cout << "No employees have logged their shifts yet." << std::endl;
                    }
                }
                else if (numMan == "4") {
                    std::cout << "Have a Nice Day!" << std::endl;
                    breakKey = true;
                }
                else {
                    std::cout << "You have entered an invalid choice" << std::endl;
                    breakKey = true;
                }

            } 
            else if(pass == "0"){
                std::cout << "Terminating session... Have a nice day!" << std::endl;
                breakKey = true;
            }
            else{
                std::cout << "You have entered an incorrect password" << std::endl;
                breakKey = true;
            }
        }
    }
    std::cout << "Program has been terminated..." << std::endl;
    return 0;
}
```
treap.h:
```c++
#pragma once
#include <string>

using namespace std;

// node class for treap with a key and a priority
class TreapNode {
public:
    string key;
    int priority;
    TreapNode* left;
    TreapNode* right;
    // constructor initializes a TreapNode with a given key and priority
    // @param key: Key for the treap node
    // @param priority: Priority for the node
    TreapNode(string key, int priority) : key(key), priority(priority), left(nullptr), right(nullptr) {}
};

// treap class is a self balancing binary search tree with a heap property
class Treap {
private:
public:
    // default constructor initializing an empty treap
    Treap();
    TreapNode* root;
    // rotates a node to the right and returns the new root of the subtree
    // @param node: The node to rotate
    // @return The new root of the subtree after rotation
    TreapNode* rotate_right(TreapNode* node);
    // rotates a node to the left and returns the new root of the subtree
    // @param node: The node to rotate
    // @return The new root of the subtree after rotation
    TreapNode* rotate_left(TreapNode* node);
    // inserts a node into the treap with a given key and priority
    void insert(string key, int priority);
private:
    // helper function for inserting a node into treap
    // @param node: The root of the current subtree
    // @param key: The key of the node to insert
    // @param priority: The priority for the node
    // @return The new root of the subtree after insertion
    TreapNode* insert_helper(TreapNode* node, string key, int priority);
    // helper function for searching a node with a given key in the treap
    // @param node: The root of the current subtree
    // @param key: The key to search for
    // @return The found node, or nullptr if not found
    TreapNode* search_helper(TreapNode* node, string key);
    // helper function for deleting a node with a given key from the treap
    // @param node: The root of the current subtree
    // @param key: The key to delete
    // @return The new root of the subtree after deletion
    TreapNode* delete_helper(TreapNode* node, string key);
};
```
treap.cpp:
```c++
#include "treap.h"

using namespace std;

// default constructor initializing an empty Treap
Treap::Treap() : root(nullptr) {

}

// rotates a node to the right and returns the new root of the subtree
// @param node: The node to rotate
// @return The new root of the subtree after rotation
TreapNode* Treap::rotate_right(TreapNode* node) {
    TreapNode* new_root = node->left;
    node->left = new_root->right;
    new_root->right = node;
    return new_root;
}

// rotates a node to the left and returns the new root of the subtree
// @param node: The node to rotate
// @return The new root of the subtree after rotation
TreapNode* Treap::rotate_left(TreapNode* node) {
    TreapNode* new_root = node->right;
    node->right = new_root->left;
    new_root->left = node;
    return new_root;
}

// helper function for inserting a node into the Treap
// @param node: The root of the current subtree
// @param key: The key of the node to insert
// @param priority: The priority for the node
// @return The new root of the subtree after insertion
TreapNode* Treap::insert_helper(TreapNode* node, string key, int priority) {
    if (node == nullptr)
        return new TreapNode(key, priority);
    if (key < node->key) {
        node->left = insert_helper(node->left, key, priority);
        if (node->left->priority > node->priority)
            node = rotate_right(node);
    } else {
        node->right = insert_helper(node->right, key, priority);
        if (node->right->priority > node->priority)
            node = rotate_left(node);
    }
    return node;
}

// inserts a node into the treap with a given key and priority
// @param key: The key of the node to insert
// @param priority: The priority for the node
void Treap::insert(string key, int priority){
    this->root = insert_helper(this->root, key, priority);
}

// helper function for searching a node with a given key in the treap
// @param node: The root of the current subtree
// @param key: The key to search for
// @return The found node, or nullptr if not found
TreapNode* Treap::search_helper(TreapNode* node, string key) {
    if (node == nullptr || node->key == key)
        return node;
    if (key < node->key)
        return search_helper(node->left, key);
    else
        return search_helper(node->right, key);
}

// helper function for deleting a node with a given key from the Treap
// @param node: The root of the current subtree
// @param key: The key to delete
// @return The new root of the subtree after deletion
TreapNode* Treap::delete_helper(TreapNode* node, string key) {
    if (node == nullptr)
        return node;
    if (key < node->key)
        node->left = delete_helper(node->left, key);
    else if (key > node->key)
        node->right = delete_helper(node->right, key);
    else {
        if (node->left == nullptr) {
            TreapNode* temp = node->right;
            delete node;
            return temp;
        } else if (node->right == nullptr) {
            TreapNode* temp = node->left;
            delete node;
            return temp;
        }

	// determines which side to rotate based on priorities
        if (node->left->priority < node->right->priority) {
            node = rotate_left(node);
            node->left = delete_helper(node->left, key);
        } else {
            node = rotate_right(node);
            node->right = delete_helper(node->right, key);
        }
    }
    return node;
}
```
interval_tree.h:
```c++
#include <iostream>
#include <vector>

using namespace std;

// represents a node in the interval tree with a data pair and max value
class IntNode{
private:
    // interval data as (start, end)
    std::pair<int, int> data;
    int maxValue;
    IntNode* left;
    IntNode* right;
public:
    // constructor for an interval tree node with given data
    // @param data: The interval data as a pair of start and end times
    IntNode(std::pair<int, int> data);
    // destructor for IntNode
    ~IntNode();

    friend class IntTree;
};

// interval Tree class that allows storage and query of interval data
class IntTree{
private:
    // helper function to insert an interval into the tree
    // @param data: The interval data as a pair of start and end times
    // @param root: The root of the current subtree
    // @return The new root of the subtree after insertion
    IntNode* insert(std::pair<int, int> data, IntNode* root);
    // helper function to find all intervals that overlap with a given point in the interval tree
    // @param data: The point to check for overlapping intervals
    // @param root: The root of the current subtree
    // @param intervalList: The list to store overlapping intervals
    void overlap(int data, IntNode* root, vector<std::pair<int, int> >& intervalList);
public:
    IntNode* root;
    // default constructor initializes an interval tree with no nodes
    IntTree();
    // inserts an interval into the tree
    // @param data: The interval data as a pair of start and end times
    void insert(std::pair<int, int> data);
    // clears the entire interval tree
    void clear();
    // find intervals overlapping with the given point
    // @param data: The point to check for overlapping intervals
    // @param intervalList: The list to store overlapping intervals
    void overlap(int data, vector<std::pair<int, int> >& intervalList);
};
```
interval_tree.cpp:
```c++
#include "interval_tree.h"
#include <iostream>
#include <vector>

using namespace std;

// constructor for an interval tree node with given data
// @param data: The interval data as a pair of start and end times
IntNode::IntNode(std::pair<int, int> data){
    this->data = data;
    this->maxValue = 0;
    this->left = nullptr;
    this->right = nullptr;
}

// default constructor initializes an interval tree with no nodes
IntTree::IntTree(){
    this->root = nullptr;
}

// inserts an interval into the interval tree and maintains the tree structure
// @param data: The interval data as a pair of start and end times
// @param root: The root of the current subtree
// @return The new root of the subtree after insertion
IntNode* IntTree::insert(std::pair<int, int> data, IntNode* root){
    // if root is null, create a new node
    if(!root){
        return new IntNode(data);
    }

    // insert into the left subtree if the data starts earlier
    if(root->left && data.first < root->left->data.first){
        root->left = insert(data, root->left);
    }
    else{
        root->right = insert(data, root->right);
    }

    // update the max value if the new interval ends later
    if(root->maxValue < data.second){
        root->maxValue = data.second;
    }

    return root;
}

// finds all intervals that overlap with the given point in the interval tree
// @param data: The point to check for overlapping intervals
// @param root: The root of the current subtree
// @param intervalList: The list to store overlapping intervals
void IntTree::overlap(int data, IntNode* root, vector<std::pair<int, int> >& intervalList){
    // if root is null, there are no overlapping intervals
    if(!root){
        return;
    }
    // check if the current node's interval overlaps with the given point
    if(root->data.first <= data && root->data.second >= data){
        intervalList.push_back(std::make_pair(root->data.first, root->data.second));
    }
    // check the left subtree if the point is before or at the start
    if(root->left && data <= root->data.first){
        overlap(data, root->left, intervalList);
    }
    // check the right subtree if the point is at or after the end
    if(root->right && data >= root->data.second){
        overlap(data, root->right, intervalList);
    }
}

// inserts an interval into the interval tree
// @param data: The interval data as a pair of start and end times
void IntTree::insert(std::pair<int, int> data){
    this->root = insert(data, this->root);
}

// finds all intervals that overlap with a given point
// @param data: The point to check for overlapping intervals
// @param intervalList: The list to store overlapping intervals
void IntTree::overlap(int data, vector<std::pair<int, int> >& intervalList){
    overlap(data, this->root, intervalList);
}
```
