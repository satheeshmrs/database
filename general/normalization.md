# Normalization:
Normalization in a Database Management System (DBMS) is a structured process for organizing data into tables and relationships to minimize data redundancy and improve data integrity, 
preventing issues like insertion, deletion, and update anomalies. This is achieved by applying a series of rules called normal forms (such as 1NF, 2NF, 3NF, and BCNF) to break down large, complex tables into smaller, more manageable ones that are logically linked. The core goal is to ensure each piece of data is stored in only one place, creating a more consistent, efficient, and maintainable database. 

## Why Normalization is Important
- Reduces Data Redundancy: By storing data in a single location, normalization minimizes repetitive information, saving storage space. 
- Improves Data Integrity: It ensures data accuracy and reliability by minimizing inconsistencies that arise from duplicate data. 
- Prevents Data Anomalies: It addresses problems such as:
  - Insertion Anomalies: The inability to add new data without having to repeat existing information. 
  - Deletion Anomalies: The unintentional loss of related data when deleting another piece of information. 
  - Update Anomalies: Inconsistent updates if data exists in multiple locations and only some instances are modified. 
- Enhances Maintainability: Changes to data only need to be made in one place, simplifying database maintenance and reducing the risk of errors. 
- Improves Query Performance: Breaking down data into smaller, specific tables can make queries simpler and faster.

## The Process of Normalization
Normalization is a step-by-step process involving the decomposition of tables and the application of normal forms: 
- First Normal Form (1NF): Ensures that all attributes (columns) contain only atomic (indivisible) values.
    -  All columns contain atomic values (i.e., indivisible values).
    -  Each row is unique (i.e., no duplicate rows).
    -  Each column has a unique name.
    -  The order in which data is stored does not matter.
    -  Example of 1NF Violation: If a table has a column "Phone Numbers" that stores multiple phone numbers in a single cell, it violates 1NF. To bring it into 1NF, you need to separate phone numbers into individual rows.
- Second Normal Form (2NF): Requires the table to be in 1NF and that all non-key attributes are fully dependent on the entire candidate key.
      - For a composite key (StudentID, CourseID), if the "StudentName" depends only on "StudentID" and not on the entire key, it violates 2NF. To normalize, move StudentName into a separate table where it depends only on "StudentID".
- Third Normal Form (3NF): Requires the table to be in 2NF and that there are no transitive dependencies (where a non-key attribute depends on another non-key attribute).
    -   Consider a table with (StudentID, CourseID, Instructor). If Instructor depends on "CourseID", and "CourseID" depends on "StudentID", then Instructor indirectly depends on "StudentID", which violates 3NF. To resolve this, place Instructor in a separate table linked by "CourseID".
- Boyce-Codd Normal Form (BCNF): A stricter version of 3NF, ensuring that for every functional dependency, the left-hand side is a superkey.
    - If a table has a dependency (StudentID, CourseID) → Instructor, but neither "StudentID" nor "CourseID" is a superkey, then it violates BCNF. To bring it into BCNF, decompose the table so that each determinant is a candidate key.

A relation must meet the requirements of a lower normal form before it can be elevated to a higher one. Achieving BCNF or 3NF is generally considered a good standard for a well-designed database. 
