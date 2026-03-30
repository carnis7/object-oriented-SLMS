#include <iostream>
#include <vector>
#include <string>
#include <queue>
#include <stack>
#include <algorithm>

using namespace std;

int global_limit = 5;

// 1. base class

class User {
protected:
    string name;
    int id;
    string password_hash;

public:
    User(string n, int i, string pwd_h) : name(n), id(i), password_hash(pwd_h) {}
    virtual ~User() {}

    // accessors
    string getName() const { return name; }
    int getId() const { return id; }
    string getHash() const { return password_hash; }

    // virtual function REVISE***
    virtual void printRole() const = 0;

    // hash REVISE***
    static string generate_hash(const string& pwd) {
        unsigned long hash_val = 5381;
        for (char c : pwd) {
            hash_val = ((hash_val << 5) + hash_val) + c;
        }
        return to_string(hash_val);
    }
};


// derived classes

class Member : public User {
public:
    int books_issued;
    Member* next; // KEEP 

    Member(string n, int id, string pwd_h)
        : User(n, id, pwd_h), books_issued(0), next(nullptr) {
    }

    void printRole() const override {
        cout << "[Role: Library Member] - ID: " << id << endl;
    }
};

class Librarian : public User {
public:
    Librarian(string n, int id, string pwd_h)
        : User(n, id, pwd_h) {
    }

    void printRole() const override {
        cout << "[Role: Staff Librarian] - Staff ID: " << id << endl;
    }
};

// system classes

class Book {
public:
    string book_name;
    int id;
    string type;
    Book* next;
    bool is_available;

    Book(string name, int id1, string book_type = "Regular")
        : book_name(name), id(id1), next(nullptr), is_available(true), type(book_type) {
    }
};

class Transaction {
public:
    int transaction_id;
    int member_id;
    int book_id;
    string date;
    string type;

    Transaction(int tid, int mid, int bid, string d, string t)
        : transaction_id(tid), member_id(mid), book_id(bid), date(d), type(t) {
    }
};

class Library {
public:
    Book* head;
    Member* member_head;
    queue<Book*> issuedbooks;
    stack<Transaction> recent_transactions;

    Library() : head(nullptr), member_head(nullptr) {}

    ~Library() {
        Book* current_book = head;
        while (current_book) {
            Book* next_book = current_book->next;
            delete current_book;
            current_book = next_book;
        }
        Member* current_member = member_head;
        while (current_member) {
            Member* next_member = current_member->next;
            delete current_member;
            current_member = next_member;
        }
    }

    bool login_member(int& logged_id) {
        int input_id;
        string input_pwd;
        cout << "Enter member ID: ";
        cin >> input_id;
        cout << "Enter password: ";
        cin.ignore();
        getline(cin, input_pwd);

        Member* mem = member_head;
        string hashed_input = User::generate_hash(input_pwd);

        while (mem) {
            if (mem->getId() == input_id && hashed_input == mem->getHash()) {
                logged_id = input_id;
                mem->printRole();
                cout << "Login successful for " << mem->getName() << "!\n";
                return true;
            }
            mem = mem->next;
        }
        cout << "Invalid ID or password!\n";
        return false;
    }//polymorphism^

    void add_book(string name, int id, string type) {
        Book* new_book = new Book(name, id, type);
        new_book->next = head;
        head = new_book;
        cout << "Book added successfully!\n";
    }

    void remove_book(int book_id) {
        Book* temp = head;
        Book* prev = nullptr;
        while (temp) {
            if (temp->id == book_id) {
                if (prev) prev->next = temp->next;
                else head = temp->next;
                delete temp;
                cout << "Book removed successfully!\n";
                return;
            }
            prev = temp;
            temp = temp->next;
        }
        cout << "Book not found!\n";
    }

    void add_member(string name, int id, string pwd) {
        string pwd_hash = User::generate_hash(pwd);
        Member* new_member = new Member(name, id, pwd_hash);
        new_member->next = member_head;
        member_head = new_member;
        cout << "Member added successfully!\n";
    }

    void issue_book(string name, int member_id) {
        try {
            Member* mem = member_head;
            bool member_found = false;
            while (mem) {
                if (mem->getId() == member_id) {
                    member_found = true;
                    if (mem->books_issued >= global_limit) throw("MAXIMUM ISSUE LIMIT REACHED!");
                    break;
                }
                mem = mem->next;
            }
            if (!member_found) throw("INVALID MEMBER ID!");

            Book* temp = head;
            Book* prev = nullptr;
            while (temp) {
                if (temp->book_name == name && temp->is_available) {
                    if (temp == head) head = head->next;
                    else prev->next = temp->next;

                    issuedbooks.push(temp);
                    temp->is_available = false;
                    mem->books_issued++;

                    recent_transactions.push(Transaction(recent_transactions.size() + 1, member_id, temp->id, "2026-03-30", "Issue"));
                    cout << "Book issued successfully!\n";
                    return;
                }
                prev = temp;
                temp = temp->next;
            }
            throw("BOOK NOT AVAILABLE!");
        }
        catch (const char* msg) { cout << "EXCEPTION: " << msg << endl; }
    }

    void return_book(int member_id) {
        try {
            if (issuedbooks.empty()) throw("NO BOOKS TO RETURN!");

            Book* returned = issuedbooks.front();
            issuedbooks.pop();
            returned->is_available = true;

            Member* mem = member_head;
            while (mem) {
                if (mem->getId() == member_id) {
                    mem->books_issued--;
                    break;
                }
                mem = mem->next;
            }

            returned->next = head;
            head = returned;
            recent_transactions.push(Transaction(recent_transactions.size() + 1, member_id, returned->id, "2026-03-30", "Return"));
            cout << "RETURN WAS A SUCCESS!\n";
        }
        catch (const char* msg) { cout << "EXCEPTION: " << msg << endl; }
    }

    void display_books() {
        Book* temp = head;
        cout << "\nAvailable Books:\n";
        while (temp) {
            if (temp->is_available) {
                cout << "- " << temp->book_name << " (ID: " << temp->id << ", Type: " << temp->type << ")\n";
            }
            temp = temp->next;
        }
    }

    void search_book(int id1) {
        Book* temp = head;
        while (temp) {
            if (temp->id == id1) {
                cout << "Book found: " << temp->book_name << " | Status: " << (temp->is_available ? "Available" : "Borrowed") << endl;
                return;
            }
            temp = temp->next;
        }
        cout << "Book ID not found.\n";
    }
};

// shell

int main() {
    Library lib;
    Librarian admin("Staff_Admin", 1, User::generate_hash("admin123"));
    int choice;

    // Default Data 5 MINUMUM needed
    lib.add_member("josh", 1001, "password");
    lib.add_book("Vagabond Vol.1", 101, "Hardcover");
    lib.add_book("I, Robot", 102, "Normal");
    lib.add_book("Oceans", 103, "Normal");
    lib.add_book("Heaven & Earth Photography", 104, "Hardcover");
    lib.add_book("World War Z", 105, "Normal");
    lib.add_book("Marvel's Punisher", 105, "Normal");

    do {
        cout << "\n>>> Smart Library Management System  <<<" << endl;
        cout << "1. Librarian Menu\n2. Member Menu\n3. View Books\n4. Search Book\n5. Exit\nChoice: ";
        cin >> choice;

        switch (choice) {
        case 1: {
            int lChoice;
            cout << "\n1. Add Book\n2. Remove Book\n3. Add Member\n4. Back\nChoice: ";
            cin >> lChoice;
            if (lChoice == 1) {
                string n, t; int id;
                cout << "Name: "; cin.ignore(); getline(cin, n);
                cout << "ID: "; cin >> id;
                cout << "Type: "; cin >> t;
                lib.add_book(n, id, t);
            }
            else if (lChoice == 2) {
                int id; cout << "Enter Book ID: "; cin >> id;
                lib.remove_book(id);
            }
            else if (lChoice == 3) {
                string n, p; int id;
                cout << "Name: "; cin.ignore(); getline(cin, n);
                cout << "ID: "; cin >> id;
                cout << "Password: "; cin >> p;
                lib.add_member(n, id, p);
            }
            break;
        }
        case 2: {
            int logged_id;
            if (lib.login_member(logged_id)) {
                int mChoice;
                cout << "1. Issue Book\n2. Return Book\nChoice: ";
                cin >> mChoice;
                if (mChoice == 1) {
                    string bn; cout << "Book Name: "; cin.ignore(); getline(cin, bn);
                    lib.issue_book(bn, logged_id);
                }
                else if (mChoice == 2) {
                    lib.return_book(logged_id);
                }
            }
            break;
        }
        case 3: lib.display_books(); break;
        case 4: { int id; cout << "ID: "; cin >> id; lib.search_book(id); break; }
        }
    } while (choice != 5);

    return 0;
}
