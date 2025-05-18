#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/stat.h>

#define MAX 100
#define FOLDER "RDS_Database"
#define FILE_PATH "RDS_Database/students.txt"
#define TEMP_FILE "RDS_Database/temp.txt"
#define ADMIN_USER "admin"
#define ADMIN_PASS "password123"

// Structure for student info
typedef struct {
    char id[20];
    char name[50];
    char dob[15];
    char department[30];
    float cgpa;
} Student;

void createFolderIfNotExists() {
    struct stat st = {0};
    if (stat(FOLDER, &st) == -1) {
        mkdir(FOLDER);
        printf("Database folder created.\n");
    }
}

int login() {
    char user[50], pass[50];
    printf("Enter Admin Username: ");
    scanf("%s", user);
    printf("Enter Admin Password: ");
    scanf("%s", pass);

    if (strcmp(user, ADMIN_USER) == 0 && strcmp(pass, ADMIN_PASS) == 0) {
        printf("Login successful.\n");
        return 1;
    } else {
        printf("Invalid credentials!\n");
        return 0;
    }
}

void displayMenu() {
    printf("\n--- Student Database Menu ---\n");
    printf("1. Add New Student\n");
    printf("2. View All Students\n");
    printf("3. Search Student by ID\n");
    printf("4. Delete Student\n");
    printf("5. Update Student Record\n");
    printf("6. Exit\n");
}

void addStudent() {
    FILE *fp = fopen(FILE_PATH, "a");
    if (fp == NULL) {
        printf("Error opening file.\n");
        return;
    }

    Student s;
    printf("Enter Student ID: ");
    scanf("%s", s.id);
    printf("Enter Full Name: ");
    getchar();
    fgets(s.name, sizeof(s.name), stdin);
    s.name[strcspn(s.name, "\n")] = 0;
    printf("Enter Date of Birth (DD/MM/YYYY): ");
    scanf("%s", s.dob);
    printf("Enter Department: ");
    scanf("%s", s.department);
    printf("Enter CGPA: ");
    scanf("%f", &s.cgpa);

    fprintf(fp, "%s|%s|%s|%s|%.2f\n", s.id, s.name, s.dob, s.department, s.cgpa);
    fclose(fp);

    printf("Student added successfully!\n");
}

void viewStudents() {
    FILE *fp = fopen(FILE_PATH, "r");
    if (fp == NULL) {
        printf("No student records found.\n");
        return;
    }

    Student s;
    printf("\n--- All Student Records ---\n");
    while (fscanf(fp, "%[^|]|%[^|]|%[^|]|%[^|]|%f\n", s.id, s.name, s.dob, s.department, &s.cgpa) != EOF) {
        printf("\nID: %s\nName: %s\nDOB: %s\nDept: %s\nCGPA: %.2f\n", s.id, s.name, s.dob, s.department, s.cgpa);
    }

    fclose(fp);
}

void searchStudent() {
    char targetID[20];
    printf("Enter Student ID to search: ");
    scanf("%s", targetID);

    FILE *fp = fopen(FILE_PATH, "r");
    if (fp == NULL) {
        printf("File not found.\n");
        return;
    }

    Student s;
    int found = 0;
    while (fscanf(fp, "%[^|]|%[^|]|%[^|]|%[^|]|%f\n", s.id, s.name, s.dob, s.department, &s.cgpa) != EOF) {
        if (strcmp(s.id, targetID) == 0) {
            printf("\n--- Student Found ---\n");
            printf("ID: %s\nName: %s\nDOB: %s\nDept: %s\nCGPA: %.2f\n", s.id, s.name, s.dob, s.department, s.cgpa);
            found = 1;
            break;
        }
    }

    if (!found) {
        printf("Student ID not found.\n");
    }

    fclose(fp);
}

void deleteStudent() {
    char targetID[20];
    printf("Enter Student ID to delete: ");
    scanf("%s", targetID);

    FILE *fp = fopen(FILE_PATH, "r");
    FILE *temp = fopen(TEMP_FILE, "w");
    if (fp == NULL || temp == NULL) {
        printf("File error.\n");
        return;
    }

    Student s;
    int found = 0;
    while (fscanf(fp, "%[^|]|%[^|]|%[^|]|%[^|]|%f\n", s.id, s.name, s.dob, s.department, &s.cgpa) != EOF) {
        if (strcmp(s.id, targetID) == 0) {
            found = 1;
            continue; // skip writing to delete
        }
        fprintf(temp, "%s|%s|%s|%s|%.2f\n", s.id, s.name, s.dob, s.department, s.cgpa);
    }

    fclose(fp);
    fclose(temp);

    remove(FILE_PATH);
    rename(TEMP_FILE, FILE_PATH);

    if (found) {
        printf("Student record deleted successfully.\n");
    } else {
        printf("Student ID not found.\n");
    }
}

void updateStudent() {
    char targetID[20];
    printf("Enter Student ID to update: ");
    scanf("%s", targetID);

    FILE *fp = fopen(FILE_PATH, "r");
    FILE *temp = fopen(TEMP_FILE, "w");
    if (fp == NULL || temp == NULL) {
        printf("File error.\n");
        return;
    }

    Student s;
    int found = 0;
    while (fscanf(fp, "%[^|]|%[^|]|%[^|]|%[^|]|%f\n", s.id, s.name, s.dob, s.department, &s.cgpa) != EOF) {
        if (strcmp(s.id, targetID) == 0) {
            found = 1;
            printf("Enter New Full Name: ");
            getchar();
            fgets(s.name, sizeof(s.name), stdin);
            s.name[strcspn(s.name, "\n")] = 0;
            printf("Enter New DOB (DD/MM/YYYY): ");
            scanf("%s", s.dob);
            printf("Enter New Department: ");
            scanf("%s", s.department);
            printf("Enter New CGPA: ");
            scanf("%f", &s.cgpa);
        }
        fprintf(temp, "%s|%s|%s|%s|%.2f\n", s.id, s.name, s.dob, s.department, s.cgpa);
    }

    fclose(fp);
    fclose(temp);

    remove(FILE_PATH);
    rename(TEMP_FILE, FILE_PATH);

    if (found) {
        printf("Student record updated successfully.\n");
    } else {
        printf("Student ID not found.\n");
    }
}

int main() {
    createFolderIfNotExists();

    if (!login()) return 0;

    int choice;
    do {
        displayMenu();
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                addStudent();
                break;
            case 2:
                viewStudents();
                break;
            case 3:
                searchStudent();
                break;
            case 4:
                deleteStudent();
                break;
            case 5:
                updateStudent();
                break;
            case 6:
                printf("Exiting...\n");
                break;
            default:
                printf("Invalid option!\n");
        }

    } while (choice != 6);

    return 0;
}
