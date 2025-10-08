#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct Attendance {
    int studentID;
    char date[11];   // Format: YYYY-MM-DD
    char status[10]; // "Present" or "Absent"
    struct Attendance* next;
} Attendance;

// Head of the attendance list
Attendance* head = NULL;

// Function to create a new attendance node
Attendance* createNode(int studentID, const char* date, const char* status) {
    Attendance* newNode = (Attendance*)malloc(sizeof(Attendance));
    newNode->studentID = studentID;
    strcpy(newNode->date, date);
    strcpy(newNode->status, status);
    newNode->next = NULL;
    return newNode;
}

// Add attendance record
void addAttendance(int studentID, const char* date, const char* status) {
    Attendance* newNode = createNode(studentID, date, status);
    newNode->next = head;
    head = newNode;
    printf("Added record for student %d on %s.\n", studentID, date);
}

// Delete an incorrect attendance entry
void deleteAttendance(int studentID, const char* date) {
    Attendance *temp = head, *prev = NULL;

    while (temp != NULL) {
        if (temp->studentID == studentID && strcmp(temp->date, date) == 0) {
            if (prev == NULL) head = temp->next;
            else prev->next = temp->next;
            free(temp);
            printf("Deleted attendance for student %d on %s.\n", studentID, date);
            return;
        }
        prev = temp;
        temp = temp->next;
    }
    printf("Record not found.\n");
}

// Search attendance by Student ID and Date
void searchAttendance(int studentID, const char* date) {
    Attendance* temp = head;
    while (temp != NULL) {
        if (temp->studentID == studentID && strcmp(temp->date, date) == 0) {
            printf("Found: Student %d on %s - %s\n", studentID, date, temp->status);
            return;
        }
        temp = temp->next;
    }
    printf("No record found for student %d on %s.\n", studentID, date);
}

// Display attendance list for a specific date
void displayByDate(const char* date) {
    Attendance* temp = head;
    int found = 0;
    printf("Attendance for %s:\n", date);
    while (temp != NULL) {
        if (strcmp(temp->date, date) == 0) {
            printf("Student %d - %s\n", temp->studentID, temp->status);
            found = 1;
        }
        temp = temp->next;
    }
    if (!found)
        printf("No records found for this date.\n");
}

// Reverse display (without modifying list)
void displayReverse(Attendance* node) {
    if (node == NULL) return;
    displayReverse(node->next);
    printf("Student %d on %s - %s\n", node->studentID, node->date, node->status);
}

// Clone the attendance list (for admin review)
Attendance* cloneList() {
    if (!head) return NULL;

    Attendance* current = head;
    Attendance* cloneHead = NULL;
    Attendance* cloneTail = NULL;

    while (current != NULL) {
        Attendance* newNode = createNode(current->studentID, current->date, current->status);
        if (cloneHead == NULL) {
            cloneHead = newNode;
            cloneTail = newNode;
        } else {
            cloneTail->next = newNode;
            cloneTail = newNode;
        }
        current = current->next;
    }

    printf("Attendance list cloned successfully.\n");
    return cloneHead;
}

// Free memory
void freeList(Attendance* node) {
    Attendance* temp;
    while (node != NULL) {
        temp = node;
        node = node->next;
        free(temp);
    }
}

// Menu
void menu() {
    int choice, id;
    char date[11], status[10];
    Attendance* adminCopy = NULL;

    while (1) {
        printf("\n--- Attendance Monitoring System ---\n");
        printf("1. Add Attendance\n");
        printf("2. Delete Attendance\n");
        printf("3. Search Attendance\n");
        printf("4. Display Attendance by Date\n");
        printf("5. Display Attendance in Reverse\n");
        printf("6. Clone Attendance List\n");
        printf("7. Exit\n");
        printf("Choose an option: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                printf("Enter Student ID: ");
                scanf("%d", &id);
                printf("Enter Date (YYYY-MM-DD): ");
                scanf("%s", date);
                printf("Enter Status (Present/Absent): ");
                scanf("%s", status);
                addAttendance(id, date, status);
                break;

            case 2:
                printf("Enter Student ID: ");
                scanf("%d", &id);
                printf("Enter Date (YYYY-MM-DD): ");
                scanf("%s", date);
                deleteAttendance(id, date);
                break;

            case 3:
                printf("Enter Student ID: ");
                scanf("%d", &id);
                printf("Enter Date (YYYY-MM-DD): ");
                scanf("%s", date);
                searchAttendance(id, date);
                break;

            case 4:
                printf("Enter Date (YYYY-MM-DD): ");
                scanf("%s", date);
                displayByDate(date);
                break;

            case 5:
                printf("Reversed Attendance List:\n");
                displayReverse(head);
                break;

            case 6:
                adminCopy = cloneList();
                printf("Cloned list (for admin):\n");
                displayReverse(adminCopy);
                freeList(adminCopy);
                break;

            case 7:
                freeList(head);
                printf("Exiting...\n");
                return;

            default:
                printf("Invalid choice.\n");
        }
    }
}

// Main function
int main() {
    menu();
    return 0;
}
