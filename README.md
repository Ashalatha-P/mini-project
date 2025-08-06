//mini-project
//smart library
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// ---------- Structures ----------

struct Book {
    int id;
    char title[50];
    char author[50];
    struct Book* next;
};

struct Node {
    int bookId;
    struct Node* next;
};

struct History {
    char action[100];
    struct History* next;
};

// ---------- Global Pointers ----------

struct Book* head = NULL;
struct Node* front = NULL;
struct Node* rear = NULL;
struct History* historyTop = NULL;

// ---------- Linked List (Books) ----------

void addBook(int id, char title[], char author[]) {
    struct Book* newBook = (struct Book*)malloc(sizeof(struct Book));
    newBook->id = id;
    strcpy(newBook->title, title);
    strcpy(newBook->author, author);
    newBook->next = NULL;

    if (!head)
        head = newBook;
    else {
        struct Book* temp = head;
        while (temp->next)
            temp = temp->next;
        temp->next = newBook;
    }

    printf("Book added.\n");
}

void displayBooks() {
    struct Book* temp = head;
    if (!temp) {
        printf("No books in the library.\n");
        return;
    }
    printf("Books in Library:\n");
    while (temp) {
        printf("ID: %d, Title: %s, Author: %s\n", temp->id, temp->title, temp->author);
        temp = temp->next;
    }
}

void searchBook(int id) {
    struct Book* temp = head;
    while (temp) {
        if (temp->id == id) {
            printf("Book Found - Title: %s, Author: %s\n", temp->title, temp->author);
            return;
        }
        temp = temp->next;
    }
    printf("Book not found.\n");
}

void deleteBook(int id) {
    struct Book *temp = head, *prev = NULL;

    if (temp && temp->id == id) {
        head = temp->next;
        free(temp);
        printf("Book deleted.\n");
        return;
    }

    while (temp && temp->id != id) {
        prev = temp;
        temp = temp->next;
    }

    if (!temp) {
        printf("Book not found.\n");
        return;
    }

    prev->next = temp->next;
    free(temp);
    printf("Book deleted.\n");
}

// ---------- Queue (Requests) ----------

void requestBook(int id) {
    struct Node* newNode = (struct Node*)malloc(sizeof(struct Node));
    newNode->bookId = id;
    newNode->next = NULL;

    if (!rear)
        front = rear = newNode;
    else {
        rear->next = newNode;
        rear = newNode;
    }

    printf("Book ID %d requested.\n", id);
}

void viewRequests() {
    struct Node* temp = front;
    if (!temp) {
        printf("No book requests.\n");
        return;
    }

    printf("Requested Book IDs: ");
    while (temp) {
        printf("%d ", temp->bookId);
        temp = temp->next;
    }
    printf("\n");
}

void borrowBook() {
    if (!front) {
        printf("No requests in queue.\n");
        return;
    }

    int id = front->bookId;
    struct Node* temp = front;
    front = front->next;
    if (!front) rear = NULL;
    free(temp);

    char action[100];
    sprintf(action, "Borrowed Book ID %d", id);
    
    struct History* newHist = (struct History*)malloc(sizeof(struct History));
    strcpy(newHist->action, action);
    newHist->next = historyTop;
    historyTop = newHist;

    printf("Book ID %d borrowed and added to history.\n", id);
}

void returnBook(int id) {
    char action[100];
    sprintf(action, "Returned Book ID %d", id);

    struct History* newHist = (struct History*)malloc(sizeof(struct History));
    strcpy(newHist->action, action);
    newHist->next = historyTop;
    historyTop = newHist;

    printf("Book ID %d returned and added to history.\n", id);
}

// ---------- Stack (History) ----------

void viewHistory() {
    if (!historyTop) {
        printf("No borrow/return history.\n");
        return;
    }

    printf("Borrow/Return History:\n");
    struct History* temp = historyTop;
    while (temp) {
        printf("%s\n", temp->action);
        temp = temp->next;
    }
}

// ---------- Menu ----------

void menu() {
    printf("\n--- Smart Library System ---\n");
    printf("1. Add Book\n");
    printf("2. Display Books\n");
    printf("3. Search Book by ID\n");
    printf("4. Delete Book\n");
    printf("5. Request Book\n");
    printf("6. View Requests\n");
    printf("7. Borrow Book\n");
    printf("8. Return Book\n");
    printf("9. View History\n");
    printf("0. Exit\n");
}

int main() {
    int choice, id;
    char title[50], author[50];

    do {
        menu();
        printf("Enter choice: ");
        scanf("%d", &choice);
        getchar(); // to clear newline character

        switch (choice) {
            case 1:
                printf("Enter ID: ");
                scanf("%d", &id);
                getchar();
                printf("Enter Title: ");
                fgets(title, sizeof(title), stdin);
                printf("Enter Author: ");
                fgets(author, sizeof(author), stdin);
                addBook(id, title, author);
                break;
            case 2:
                displayBooks();
                break;
            case 3:
                printf("Enter Book ID to search: ");
                scanf("%d", &id);
                searchBook(id);
                break;
            case 4:
                printf("Enter Book ID to delete: ");
                scanf("%d", &id);
                deleteBook(id);
                break;
            case 5:
                printf("Enter Book ID to request: ");
                scanf("%d", &id);
                requestBook(id);
                break;
            case 6:
                viewRequests();
                break;
            case 7:
                borrowBook();
                break;
            case 8:
                printf("Enter Book ID to return: ");
                scanf("%d", &id);
                returnBook(id);
                break;
            case 9:
                viewHistory();
                break;
            case 0:
                printf("Exiting...\n");
                break;
            default:
                printf("Invalid choice.\n");
        }

    } while (choice != 0);

    return 0;
}
