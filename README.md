# Bank-Account-System
import pandas as pd
import random 
import os

class Banking_System:
    def __init__(self, Name, Balance):
        self.name = Name
        self.Balance = Balance
        Acc_no = random.randint(111111,999999)
        acc = random.randint(111111,999999)
        final_acc = str(Acc_no)+str(acc)

        dataframe = pd.read_excel("Bank Account DataBase.xlsx", dtype={"Account": str})
        new_df = pd.DataFrame({"Account Holder Name":[self.name], "Account":[final_acc], "Bank Balance":[self.Balance]})
        df_final = pd.concat([dataframe, new_df], ignore_index=True)
        df_final.to_excel("Bank Account DataBase.xlsx", index=False)

        print(f"Your Account no. is:-> {final_acc}\n")

    def file_Checker(file_name):
        if not os.path.exists(file_name):        
            if file_name == "Bank Account DataBase.xlsx":     
                columns = pd.DataFrame(columns=["Account Holder Name", "Account", "Bank Balance"])
            else:
                columns = pd.DataFrame(columns=["Account Number", "Transaction Type", "Amount", "Balance"])
            columns.to_excel(file_name, index=False)


    def Existing_Acc(choice2, purpose=None, database ="Bank Account DataBase.xlsx", key = "Account"):
        
        content = pd.read_excel(database, dtype={key: str})
        is_presence = (content[key] == choice2)

        if is_presence.any():
            row = content[is_presence]
            if purpose == "View Account Details" or purpose == "View Transaction Details":
                print(f"\nHere's your Account Details:->\n {row}\n")
        else:
            print(f"Sorry Your Account no. is not exist in Our '{database}' Data-Base! Please Create a New Account!")
            return "Not found"
        
    def Transaction_History(Acc_no):
        value = Banking_System.Existing_Acc(Acc_no,"View Transaction Details", "Transaction History.xlsx", "Account Number")

    def Credit_and_Debit(Amount, Acc_no, Type):
        val = Banking_System.Existing_Acc(Acc_no)
        if val == None:
            
        # Creditting or Debitting the Amount of User's Account
            df = pd.read_excel("Bank Account DataBase.xlsx", dtype={"Account":str})
            df2 = pd.read_excel('Transaction History.xlsx',dtype={"Account Number":str})
            idx = df.index[df["Account"] == Acc_no][0]
            balance = df.at[idx,"Bank Balance"]
            if Type == "Credit":
                
                # Updating the Bank Balance by adding the Credit Amount
                idx = df.index[df["Account"] == Acc_no][0] # Finding the index of the row where Account Number matches
                df.at[idx,"Bank Balance"] += Amount # Updating the Bank Balance by adding the Credit Amount
                df.to_excel("Bank Account DataBase.xlsx", index = False) # Saving the updated DataFrame to excel file

                # Creating new and updated DataFrame for Transaction History
                new_df = pd.DataFrame({"Account Number":[Acc_no], "Transaction Type":["Credit"], "Amount":[Amount], "Balance":[balance+Amount]}) # Creating new and updated DataFrame for Transaction History
                df_final = pd.concat([df2,new_df], ignore_index=True) # Concatenating both DataFrames (old and new excel files)
                df_final.to_excel("Transaction History.xlsx", index=False) # Saving the updated DataFrame to excel file
                print("Your Amount has been Credited Successfully!\n")

            elif Type == "Debit":
                
                if (Amount < balance or Amount == balance):
                    # Updating the Bank Balance by subtracting the Debit Amount
                    df.loc[df["Account"] == Acc_no, "Bank Balance"] -= Amount # Updating the Bank Balance by subtracting the Debit Amount
                    df.to_excel("Bank Account DataBase.xlsx", index = False) # Saving the updated DataFrame to excel file

                    # Creating new and updated DataFrame for Transaction History
                    new_df = pd.DataFrame({"Account Number":[Acc_no], "Transaction Type":["Debit"], "Amount":[Amount], "Balance":[balance-Amount]}) # Creating new and updated DataFrame for Transaction History
                    df_final = pd.concat([df2,new_df], ignore_index=True) # Concatenating both DataFrames (old and new excel files)
                    df_final.to_excel("Transaction History.xlsx", index=False) # Saving the updated DataFrame to excel file
                    print("Your Amount has been Debited Successfully!\n")
                else:
                    print("Sorry!! You don't have enough Balance in Your Bank Account!!")
     
    def New_Acc():
        person_info1 = input("Enter your name:-> ")
        try:
            person_info2 = int(input("How much money do you want to keep in your Bank Account:-> "))
        except Exception as e:
            print("Error:-> ", e)

        if (person_info2 > 0 ):
            b1 = Banking_System(person_info1, person_info2) # Object
        else:
            print("Please Enter the Amount which is greater than zero")

    def Reset_History(acc, purpose=None):
        val = Banking_System.Existing_Acc(str(acc), "Reset History", "Transaction History.xlsx", "Account Number")
        if val == None:
            df = pd.read_excel("Transaction History.xlsx", dtype={"Account Number":int})
            x = df[df['Account Number'] != int(acc)].reset_index(drop=True)
            x.to_excel("Transaction History.xlsx", index=False)
            print("Your Transaction History has been cleared successfully!\n")

    def Delete_Acc(acc):
        val = Banking_System.Existing_Acc(acc, "Delete Transaction History")
        if val == None:
            df = pd.read_excel("Bank Account DataBase.xlsx", dtype={"Account":int})
            x=df[df['Account'] != int(acc)].reset_index(drop=True)
            x.to_excel("Bank Account DataBase.xlsx", index=False)
            Banking_System.Reset_History(acc)
            print("Your Account has been deleted successfully!\n")

    def input_method():
        try:
            acc = int(input("Enter Your Account Number:-> "))
        except Exception:
            print("Invalid Input!")
            return "Invalid Input!"
        if len(str(acc)) != 12:
            print("Invalid Account Number")
            return "Invalid Account Number"
        return str(acc)

while True:
    Banking_System.file_Checker("Bank Account DataBase.xlsx")
    Banking_System.file_Checker("Transaction History.xlsx")
    choice1 = input("\nPress 1 for Create a New Account:\nPress 2 for access your existing account:\nPress 3 for Debit:\nPress 4 for Credit:\nPress 5 for View Transaction History:\nPress 6 for clear your Transaction History\nPress 7 for Delete your Account:\nPress 8 for Exit the Program:-> ")

    # Creating a New Bank Account
    if choice1 == "1":
        Banking_System.New_Acc()

    # Access the User's existing account through his Acc No. :->
    elif choice1 == "2":
        acc = Banking_System.input_method()
        if acc != "Invalid Input!" and acc != "Invalid Account Number":
            choice_four = Banking_System.Existing_Acc(acc, "View Account Details")

    elif choice1 == "3":
        acc = Banking_System.input_method()
        if acc != "Invalid Input!" and acc != "Invalid Account Number":
            debit = int(input("Enter the amount to debit:-> "))
            if debit > 0:
                Banking_System.Credit_and_Debit(debit, acc, "Debit")
            else:
                print("Please Input the amount which is greater than zero!\n")

    elif choice1 == "4":
        acc = Banking_System.input_method()
        if acc != "Invalid Input!" and acc != "Invalid Account Number":
            error = None
            try:
                credit = int(input("Enter the amount to credit:-> "))
            except Exception:
                print("Invalid Input!")
                error = "Invalid Input!"
            if error != "Invalid Input!":
                if credit > 0:
                    Banking_System.Credit_and_Debit(credit, acc, "Credit")
                else:
                    print("Please Input the amount which is greater than zero!\n")

    elif choice1 == "5":
        acc = Banking_System.input_method()
        if acc != "Invalid Input!" and acc != "Invalid Account Number":
            Banking_System.Transaction_History(str(acc))
    
    elif choice1 == "6":
        acc = Banking_System.input_method()
        if acc != "Invalid Input!" and acc != "Invalid Account Number":
            Banking_System.Reset_History(acc)
    
    elif choice1 == "7":
        acc = Banking_System.input_method()
        if acc != "Invalid Input!" and acc != "Invalid Account Number":
            Banking_System.Delete_Acc(acc)

    elif choice1 == "8":
        break
    else:
        print("Sorry!! Your Choice is Not Valid!!")
