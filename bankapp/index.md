# BankApp


# Idea
The idea came to me totally random, while I wasn't doing a damn thing :joy:
I saw some videos on youtube and I decided to approach this "challenge" (let's put it in quotes because then we'll see that it's not a real challenge)
The idea behind it is very simple, an interface (let's call it HOME) where there are links to other interfaces: Withdrawal, Deposit, User Shows and so on...
# Services
The services offered by this app are:
- Withdrawal of money from the account
- Deposit money into the account
- User shows
- Account search by account name
- Change account number by username
# Realization
The app was created using the Java language, and uses a MySql database for data storage and modification
The ide used is Ecplise
All the interfaces were created using Java AWT, so the code will be unclear (I attach images of the interfaces)
# Code
The code of this application can be found all on my GitHub
Link here --> [Bank App](https://github.com/francosalvucci14/Bank-App)

This is the screenshots of the interafaces<br/>
Home Page:<br/>
{{< figure src="/images/BankApp/Home.png" title="Home page" >}}<br/>

Change Account Details:<br/>
{{< figure src="/images/BankApp/ChangeAccountDet.png" title="Change account details page" >}}<br/>

Withdrawal:<br/>
{{< figure src="/images/BankApp/Prelievo.png" title="Withdrawal page" >}}<br/>

Register User:<br/>
{{< figure src="/images/BankApp/RegisterUser.png" title="Register User page" >}}<br/>
Show User:<br/>
{{< figure src="/images/BankApp/ShowUser.png" title="Search User page" >}}<br/>

# Database
The database was created on DBMS **MySql**, and is organized as follows:

- There are two tables, "Users" and "Credit"
  - In the Users table there are the following fields:
    - UID -> User ID (**primary key**)
    - User -> Username
    - Pass -> Password
    - NConto -> Current account number
  - In the Credit table there are the following fields:
    - CID -> Account ID (**primary key**)
    - UserID -> User ID (subkey related to the UID key of the Users table)
    - Credit -> Account credit

Every time a user is entered in the scheme, he is given an account number in a random way, with credit 0, and in the DB he is linked to the Credit table
The tables are connected by a **1:1** relationship

{{< typeit code=java >}}
public class Thanks{
  public static void main(String[] args){
    System.out.println("Thanks for reading :smile:");
  }
}
{{< /typeit >}}
