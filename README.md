//# library-management-system-c
//Medium level Library Management System using C language
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Book {
    int id;
    char name[50];
    char author[50];
    int quantity;
};

void addBook();
void viewBooks();
void searchBook();
void issueBook();
void returnBook();

int main() {
    int choice;

    while (1) {
        printf("\n==============================\n");
        printf(" LIBRARY MANAGEMENT SYSTEM\n");
        printf("==============================\n");
        printf("1. Add Book\n");
        printf("2. View Books\n");
        printf("3. Search Book\n");
        printf("4. Issue Book\n");
        printf("5. Return Book\n");
        printf("6. Exit\n");
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: addBook(); break;
            case 2: viewBooks(); break;
            case 3: searchBook(); break;
            case 4: issueBook(); break;
            case 5: returnBook(); break;
            case 6: exit(0);
            default: printf("Invalid Choice!\n");
        }
    }
}

void addBook() {
    FILE *fp = fopen("books.dat", "ab");
    struct Book b;

    printf("Enter Book ID: ");
    scanf("%d", &b.id);
    printf("Enter Book Name: ");
    scanf(" %[^\n]", b.name);
    printf("Enter Author Name: ");
    scanf(" %[^\n]", b.author);
    printf("Enter Quantity: ");
    scanf("%d", &b.quantity);

    fwrite(&b, sizeof(b), 1, fp);
    fclose(fp);

    printf("Book Added Successfully!\n");
}

void viewBooks() {
    FILE *fp = fopen("books.dat", "rb");
    struct Book b;

    if (!fp) {
        printf("No records found!\n");
        return;
    }

    printf("\nID\tName\tAuthor\tQty\n");
    printf("----------------------------------\n");

    while (fread(&b, sizeof(b), 1, fp)) {
        printf("%d\t%s\t%s\t%d\n",
               b.id, b.name, b.author, b.quantity);
    }
    fclose(fp);
}

void searchBook() {
    FILE *fp = fopen("books.dat", "rb");
    struct Book b;
    int id, found = 0;

    printf("Enter Book ID to Search: ");
    scanf("%d", &id);

    while (fread(&b, sizeof(b), 1, fp)) {
        if (b.id == id) {
            printf("Book Found!\n");
            printf("Name: %s\nAuthor: %s\nQuantity: %d\n",
                   b.name, b.author, b.quantity);
            found = 1;
            break;
        }
    }
    if (!found)
        printf("Book Not Found!\n");

    fclose(fp);
}

void issueBook() {
    FILE *fp = fopen("books.dat", "rb+");
    struct Book b;
    int id;

    printf("Enter Book ID to Issue: ");
    scanf("%d", &id);

    while (fread(&b, sizeof(b), 1, fp)) {
        if (b.id == id && b.quantity > 0) {
            b.quantity--;
            fseek(fp, -sizeof(b), SEEK_CUR);
            fwrite(&b, sizeof(b), 1, fp);
            printf("Book Issued Successfully!\n");
            fclose(fp);
            return;
        }
    }
    printf("Book Not Available!\n");
    fclose(fp);
}

void returnBook() {
    FILE *fp = fopen("books.dat", "rb+");
    struct Book b;
    int id;

    printf("Enter Book ID to Return: ");
    scanf("%d", &id);

    while (fread(&b, sizeof(b), 1, fp)) {
        if (b.id == id) {
            b.quantity++;
            fseek(fp, -sizeof(b), SEEK_CUR);
            fwrite(&b, sizeof(b), 1, fp);
            printf("Book Returned Successfully!\n");
            fclose(fp);
            return;
        }
    }
    printf("Book Not Found!\n");
    fclose(fp);
}
