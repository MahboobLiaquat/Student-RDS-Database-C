# 🧾 Student Database Management System in C

This project is a **console-based student record management system** written in C. It allows an administrator to manage student records securely using text file storage. The project was originally built in 2020 during my undergraduate studies at **North South University**, Bangladesh, and has now been recreated for GitHub as a portfolio piece.

It includes the following features:

- 🔐 Admin login system
- 📁 Automatic folder creation (`RDS_Database/`)
- 🗃️ Text file-based data storage
- 🧑‍🎓 Student record operations:
  - Add a new student
  - View all students
  - Search student by ID
  - Delete student
  - Update student details

Each student record contains:
- Student ID
- Full Name
- Date of Birth (DD/MM/YYYY)
- Department
- CGPA

## How to Compile and Run

Make sure you have GCC installed.

```bash
gcc main.c -o studentDB
./studentDB
