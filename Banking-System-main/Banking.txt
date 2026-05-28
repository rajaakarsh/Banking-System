// DynamicBankingSystem
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include<ctype.h>

// Define a structure to represent a bank account
struct BankAccount {
    char accountNumber[15];
    char accountName[50];
    double balance;
};

// Function to create a new bank account
struct BankAccount createAccount(char accNumber[], char accName[], double initialBalance) {
    struct BankAccount newAccount;
    strcpy(newAccount.accountNumber,accNumber);
    strcpy(newAccount.accountName,accName);
    newAccount.balance=initialBalance;
    return newAccount;
}

// Function to deposit money into an account
void deposit(struct BankAccount *account, double amount) {
    account->balance += amount;
}

// Function to withdraw money from an account
int withdraw(struct BankAccount *account, double amount) {
    if (account->balance>=amount) {
        account->balance-=amount;
        return 1; // Withdrawal successful
    } else {
        return 0; // Insufficient balance
    }
}

// Function to check the balance of an account
double checkBalance(struct BankAccount account) {
    return account.balance;
}

int main() {
    struct BankAccount *accounts=NULL; // Dynamic array to store bank accounts
    int numAccounts=0; // Initialize the number of accounts to 0

    while (1) {
        printf("Choose an option:\n");
        printf("1. Create Account\n");
        printf("2. Deposit\n");
        printf("3. Withdraw\n");
        printf("4. Check Balance\n");
        printf("5. Exit\n");

        int choice,invalid=0;
        scanf("%d", &choice);

        switch (choice) {
            case 1: {
                // Create a new account
                char accNumber[15];
                char accName[50];
                double initialBalance;

                printf("Enter account number: ");
                scanf("%s",accNumber);
                for(int i=0;accNumber[i]!='\0';i++){
                    if(isalpha(accNumber[i]) ){
                        printf("Enter Valid Number!!\n");
                        for (int i=0;i<50;i++) {
                            accNumber[i]=0;
                        }
                       invalid=1;
                    }
                }

                if(invalid==1){
                    break;
                }
                printf("Enter account name: ");
                scanf("%s",accName);
                for(int i=0;accName[i]!='\0';i++){
                    if(isdigit(accName[i]) ){
                        printf("Enter Valid Name!!\n");
                        for (int i=0;i<50;i++) {
                            accName[i]=0;
                        }
                        invalid=1;
                    }
                }

                if(invalid==1){
                    break;
                }

                printf("Enter initial balance: ");
                scanf("%lf",&initialBalance);
                if(initialBalance<=100){
                    printf("Minimum Balance of Rs.100/- is Required!!\n");
                    break;
                }
                // Allocate memory for a new account
                struct BankAccount newAccount = createAccount(accNumber, accName, initialBalance);
                numAccounts++;

                // Reallocate memory for the updated accounts array
                accounts = realloc(accounts, numAccounts * sizeof(struct BankAccount));
                accounts[numAccounts - 1] = newAccount;

                printf("Account created successfully!\n");
                break;
            }

            case 2: {
                // Deposit money into an account
                if (numAccounts == 0) {
                    printf("No accounts found. Please create an account first.\n");
                    break;
                }

                char accNumber[15];
                double amount;

                printf("Enter account number: ");
                scanf("%s", accNumber);

                int accountIndex = -1;
                for (int i = 0; i < numAccounts; i++) {
                    if (strcmp(accounts[i].accountNumber, accNumber) == 0) {
                        accountIndex = i;
                        break;
                    }
                }

                if (accountIndex == -1) {
                    printf("Account not found.\n");
                } else {
                    printf("Enter amount to deposit: ");
                    scanf("%lf", &amount);
                    deposit(&accounts[accountIndex], amount);
                    printf("Deposit successful. New balance: Rs.%.2f\n", checkBalance(accounts[accountIndex]));
                }
                break;
            }

            case 3: {
                // Withdraw money from an account
                if (numAccounts == 0) {
                    printf("No accounts found. Please create an account first.\n");
                    break;
                }

                char accNumber[15];
                double amount;

                printf("Enter account number: ");
                scanf("%s", accNumber);

                int accountIndex = -1;
                for (int i = 0; i < numAccounts; i++) {
                    if (strcmp(accounts[i].accountNumber, accNumber) == 0) {
                        accountIndex = i;
                        break;
                    }
                }

                if (accountIndex == -1) {
                    printf("Account not found.\n");
                } else {
                    printf("Enter amount to withdraw: ");
                    scanf("%lf", &amount);
                    int result = withdraw(&accounts[accountIndex], amount);
                    if (result) {
                        printf("Withdrawal successful. New balance: Rs.%.2f\n", checkBalance(accounts[accountIndex]));
                    } else {
                        printf("Insufficient balance.\n");
                    }
                }
                break;
            }

            case 4: {
                // Check the balance of an account
                if (numAccounts == 0) {
                    printf("No accounts found. Please create an account first.\n");
                    break;
                }

                char accNumber[15];

                printf("Enter account number: ");
                scanf("%s", accNumber);

                int accountIndex = -1;
                for (int i = 0; i < numAccounts; i++) {
                    if (strcmp(accounts[i].accountNumber, accNumber) == 0) {
                        accountIndex = i;
                        break;
                    }
                }

                if (accountIndex == -1) {
                    printf("Account not found.\n");
                } else {
                    printf("Account balance: Rs.%.2f\n", checkBalance(accounts[accountIndex]));
                }
                break;
            }

            case 5: {
                // Exit the program
                free(accounts); // Free the allocated memory
                return 0;
            }

            default:
                printf("Invalid option. Please choose a valid option.\n");
        }
    }
}