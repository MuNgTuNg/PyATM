import os
import time
import sys
#insert card
    #is it valid?
        #if so pass
        #if not, hold it
    
#type in pin

#does pin match?
    #if so go to options screen
    #if not, -1 attempts and restart

#if withdrawing, check the amount against current balance
class account:
    def __init__(self,pinIn,nameIn,balanceIn,numberIN):
        self.pin = pinIn 
        self.name = nameIn
        self.balance = balanceIn
        self.number = numberIN
        self.pinAttempts = 3
        self.locked = False



def readCard(cardName):
    try:
        with open(cardName) as f:
            name = readCardValue(f)
            number = int(readCardValue(f))
            pin = int(readCardValue(f))
            balance = int(readCardValue(f))
            f.close()
            return account(pin,name,balance,number)
    except FileNotFoundError:
        print("Sorry, the file {} does not exist.".format(cardName))
        ejectCard()

def saveDetails(cardName,currAccount):
    f = open(cardName, "w")
    data = "name:({})\nnumber:({})\npin:({})\nbalance:({})".format(currAccount.name,currAccount.number,currAccount.pin,currAccount.balance)
    f.write(data)
    f.close()

    
    

def readCardValue(f):
    line = f.readline()
    return line[line.find("(")+1 : line.find(")")]

def ejectCard():
    print("Card ejected.\n")
    exit()

def printDetails(account):
    print(account.name)
    print(account.number)
    print(account.pin)
    print(account.balance)



def isAccountValid(currAccount):
    return len(str(currAccount.number)) == 9 and len(str(currAccount.pin)) == 4

def isWithdrawValid(currAccount, withdrawAmount):
    return withdrawAmount < currAccount.balance
        
def isPinValid(currAccount,input):
    if(currAccount.pinAttempts >0):
        if input == currAccount.pin:
            return True
        else:
            currAccount.pinAttempts -=1
            return False
    else:
        print("account locked.\n")
        currAccount.locked = True
        return False


def displayOptions():
    print("Type 'W' to withdraw\nType 'D' to deposit\nType 'B' for balance\nType 'E' for exit\nType 'H' for options\nType 'c' to clear screen")

def processOptions(currAccount,option, exit):
    if(option == "h"):
        displayOptions()
    
    if(option == "d"):
        depositAmount = int(input("How much to deposit? "))
        if(depositAmount > 0):
            currAccount.balance += depositAmount
            print("New account balance: {}".format(currAccount.balance))
            saveDetails(cardName,accountCLI)
        else:
            response = str(input("Would you like to withdraw instead? Y/N ")).lower()
            if(response == "y"):
                processOptions(currAccount,"w", exit)

    if(option == "w"):
        withdrawAmount = int(input("How much to withdraw? "))
        if(isWithdrawValid(currAccount,withdrawAmount) and withdrawAmount > 0):
            currAccount.balance -= withdrawAmount
            print("New account balance: {}".format(currAccount.balance))
            saveDetails(cardName,accountCLI)
        elif(withdrawAmount < 0):
            print("Do not be so cheeky  >:(")
        else:
            print("You do not have the funds for this withdraw.")
        

    if(option == "e"):
        "Exiting..."
        exit[0] = False

    if(option == "b"):
        print("Account balance: {}\n".format(currAccount.balance))
        
    if(option == "c"):
        os.system("clear")

    

def checkPin(currAccount):
    enterCondition = False
    while(not enterCondition):
        pin = int(input("Enter Pin: "))
        if(isPinValid(currAccount,pin)):
            enterCondition = True
            os.system("clear")
            print("Welcome {}!\n".format(currAccount.name))
            break
        elif(currAccount.locked):
            ejectCard()
        else:
            print("Pin invalid. Try again\n")


def mainLoop(currAccount):
    exit = [True]
    displayOptions()
    while(exit[0] == True):
        option = input("\nCommand: ").lower()
        processOptions(currAccount,option,exit)
        

#Driver code
cardName = "cards/"+str(sys.argv[1])
accountCLI = readCard(cardName)
isAccountValid(accountCLI)
checkPin(accountCLI)
mainLoop(accountCLI)

saveDetails(cardName,accountCLI)
print("Thank you! GoodBye {}!".format(accountCLI.name))
time.sleep(2)



