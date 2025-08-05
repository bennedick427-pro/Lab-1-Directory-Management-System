#include <iostream>
#include <filesystem>
#include <string>

namespace fs = std::filesystem;
using namespace std;

void listAllFiles(const fs::path& path);
void listFilesByExtension(const fs::path& path);
void listFilesByPattern(const fs::path& path);
void listFiles();

void createDirectory();
void changeDirectory();
void mainMenu();

fs::path currentPath = fs::current_path();

int main() {
    mainMenu();
    return 0;
}

void mainMenu() {
    int choice;

    do {
        cout << "\nCURRENT DIRECTORY: " << currentPath << "\n";
        cout << "MAIN MENU:\n";
        cout << "[1] List Files\n";
        cout << "[2] Create Directory\n";
        cout << "[3] Change Directory\n";
        cout << "[4] Exit\n";
        cout << "Enter choice: ";
        
        if (!(cin >> choice)) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Invalid input. Please enter a number.\n";
            continue;
        }

        cin.ignore();

        switch (choice) {
            case 1: listFiles(); break;
            case 2: createDirectory(); break;
            case 3: changeDirectory(); break;
            case 4: cout << "Exiting program.\n"; break;
            default: cout << "Invalid choice. Try again.\n";
        }

    } while (choice != 4);
}

void listFiles() {
    int choice;
    cout << "\nLIST FILES SUBMENU:\n";
    cout << "[1] List All Files\n";
    cout << "[2] List Files by Extension (e.g., .txt)\n";
    cout << "[3] List Files by Pattern (e.g., moha)\n";
    cout << "Enter choice: ";
    
    if (!(cin >> choice)) {
        cin.clear();
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
        cout << "Invalid input.\n";
        return;
    }

    cin.ignore(); 

    switch (choice) {
        case 1: listAllFiles(currentPath); break;
        case 2: listFilesByExtension(currentPath); break;
        case 3: listFilesByPattern(currentPath); break;
        default: cout << "Invalid choice.\n";
    }
}

void listAllFiles(const fs::path& path) {
    cout << "\nListing all files:\n";
    for (const auto& entry : fs::directory_iterator(path)) {
        if (fs::is_regular_file(entry))
            cout << entry.path().filename().string() << '\n';
    }
}

void listFilesByExtension(const fs::path& path) {
    string ext;
    cout << "Enter file extension (include the dot, e.g., .txt): ";
    getline(cin, ext);

    cout << "\nFiles with extension " << ext << ":\n";
    for (const auto& entry : fs::directory_iterator(path)) {
        if (fs::is_regular_file(entry) && entry.path().extension() == ext)
            cout << entry.path().filename().string() << '\n';
    }
}

void listFilesByPattern(const fs::path& path) {
    string pattern;
    cout << "Enter filename pattern (e.g., moha): ";
    getline(cin, pattern);

    cout << "\nFiles matching pattern '" << pattern << "':\n";
    for (const auto& entry : fs::directory_iterator(path)) {
        if (fs::is_regular_file(entry)) {
            string filename = entry.path().filename().string();
            if (filename.find(pattern) != string::npos)
                cout << filename << '\n';
        }
    }
}

void createDirectory() {
    string dirname;
    cout << "Enter name for new directory: ";
    getline(cin, dirname);

    if (dirname.empty()) {
        cout << "Error: Directory name cannot be empty.\n";
        return;
    }

    fs::path dirPath = currentPath / dirname;

    if (fs::exists(dirPath)) {
        cout << "Error: Directory already exists.\n";
    } else {
        try {
            if (fs::create_directory(dirPath)) {
                cout << "Directory created successfully.\n";
            } else {
                cout << "Error: Failed to create directory.\n";
            }
        } catch (const fs::filesystem_error& e) {
            cout << "Exception: " << e.what() << '\n';
        }
    }
}

void changeDirectory() {
    string newPath;
    cout << "Enter path to change directory: ";
    getline(cin, newPath);

    fs::path target = newPath;

    if (fs::exists(target) && fs::is_directory(target)) {
        currentPath = fs::canonical(target);
        cout << "Directory changed to: " << currentPath << '\n';
    } else {
        cout << "Error: Directory does not exist or is invalid.\n";
    }
}
