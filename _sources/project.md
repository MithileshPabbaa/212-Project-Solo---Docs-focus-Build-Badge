# The Project

## Description:
Our project aims to improve on retail POS (Point-Of-Scale) systems using Interval Trees, Bucket Sort, and Treap.
By using these three data structures we will improve search efficiency, storage managment, and organization.

Interval Trees -
Used to manage employee time records effectively. This structure enables quick retrieval of information such as who was on duty at specific times,
determining the latest staff presence (maximum interval value), and calculating total work hours per day. Organizing data in this manner,
with each tree representing a day, optimizes these processes.

Bucket Sort -
To handle stock number data efficiently, we implemented a bucket sort algorithm. Employee inputs regarding item stock numbers will be stored as structured elements in a vector.
Bucket sort will facilitate identifying necessary stock adjustments swiftly.

Treap -
Used to manage time-sensitive stock data. Once stock information is entered into the vector and sorted using bucket sort, a treap will be constructed,
prioritizing lower stock numbers. This strategy allows store owners to make informed decisions regarding item ordering efficiently.

## Usage:
This program serves two primary functions:
- Tracking employee's weekly hours
- Monitoring stock numbers

Employee's and items are represented as structs to facilitate and track storage of specific data.

Employee Struct
- Name, shift's, and total hour's worked

Item Struct
- Name, gross profit per item, and total sale information

## Real-world Problem

Our program will augment preexisting POS systems present in retail environments.
It will help improve efficiency and organization, while also adding functionality.
Some sort of software for business, tracking transactions and employee work time.  It will be a program that employees
utilize when they are leaving for the day.  Some functions will apply to employees of all shifts, such as the time clock,
however, some functions will only be utilized at the end of the day, such as inputting stock numbers for various products.

Interval Tree - Employees can enter time in/time out like a time clock.  These entries can be stored in an interval tree.
Storing this information in this way will allow business owners to search for who was working at a certain time, the latest
anyone was in the store (the maximum interval value present in each root node of each subtree), and the total number of hours
worked on a given day.  Storing the data like this, with one tree representing one day, will make all of the aforementioned
processes more efficient.

Bucket Sort - Employees will also enter data about the stock numbers of various items.  These items will be stored as structs
in a vector, which will be sorted using bucket sort in order to determine necessary stocking corrections.

Treap - Because a treap is basically a combination between a BST and a priority queue, it is the perfect data structure to use
when data that is time sensitive is being stored.  After our stock data is entered into its vector, and sorted with bucket sort,
we can construct a treap, treating lower stock numbers as a higher priority.  This will allow the owner of the store to
efficiently decide what items to prioritize when ordering.

This program will have two main functions: tracking the work times of employees and tracking stock numbers.
Both the employees and items will be structs, to allow for certain data to be stored for each.
The employee structs will track name, shifts, and total hours worked.  The item structs will track name, gross
profit per, and cumulative sales information.
