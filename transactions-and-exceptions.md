### Transactions and Exceptions

Wrongo

```java
    public static void AddRemoveWrong() {
        Balance__c balanceOne = [SELECT Name, Balance__c, Comment__c FROM Balance__c WHERE Name = 'JayOne'];
        Balance__c balanceTwo = [SELECT Name, Balance__c, Comment__c FROM Balance__c WHERE Name = 'JayTwo'];

        System.debug(balanceOne.Balance__c);
        System.debug(balanceTwo.Balance__c);

        balanceOne.Balance__c = balanceOne.Balance__c + 2.00;
        balanceTwo.Balance__c = balanceTwo.Balance__c - 2.00;

        // Comment__c is a requird field, it was created to simulate a DML Exception.
        balanceOne.Comment__c = 'JayTwo Added 2 Bucks to JayOne';
        balanceTwo.Comment__c = null;

        try {
            update balanceOne;
            update balanceTwo;
        } catch (Exception e) {
            System.debug(e.getMessage());
        }


        balanceOne = [SELECT Name, Balance__c, Comment__c FROM Balance__c WHERE Name = 'JayOne'];
        balanceTwo = [SELECT Name, Balance__c, Comment__c FROM Balance__c WHERE Name = 'JayTwo'];

        System.debug(balanceOne.Balance__c);
        System.debug(balanceTwo.Balance__c);
    }
```



OK

```java
  public static void AddRemoveOk() {
        Balance__c balanceOne = [SELECT Name, Balance__c, Comment__c FROM Balance__c WHERE Name = 'JayOne'];
        Balance__c balanceTwo = [SELECT Name, Balance__c, Comment__c FROM Balance__c WHERE Name = 'JayTwo'];

        System.debug(balanceOne.Balance__c);
        System.debug(balanceTwo.Balance__c);

        balanceOne.Balance__c = balanceOne.Balance__c + 2.00;
        balanceTwo.Balance__c = balanceTwo.Balance__c - 2.00;

        // Comment__c is a requird field, it was created to simulate a DML Exception.
        balanceOne.Comment__c = 'JayOne Added 2 Bucks';
        balanceTwo.Comment__c = null;

        Savepoint sp = Database.setSavepoint();
        try {
            update balanceOne;
            update balanceTwo;
        } catch (Exception e) {
            System.debug(e.getMessage());
            Database.rollback(sp);
        }


        balanceOne = [SELECT Name, Balance__c, Comment__c FROM Balance__c WHERE Name = 'JayOne'];
        balanceTwo = [SELECT Name, Balance__c, Comment__c FROM Balance__c WHERE Name = 'JayTwo'];

        System.debug(balanceOne.Balance__c);
        System.debug(balanceTwo.Balance__c);
    }
```

Sample Code

```java
public with sharing class TransactionsDemo {
    public static void AddRemoveCorrect() {
        Balance__c balanceOne = [SELECT Name, Balance__c, Comment__c FROM Balance__c WHERE Name = 'JayOne'];
        Balance__c balanceTwo = [SELECT Name, Balance__c, Comment__c FROM Balance__c WHERE Name = 'JayTwo'];

        System.debug(balanceOne.Balance__c);
        System.debug(balanceTwo.Balance__c);

        balanceOne.Balance__c = balanceOne.Balance__c + 2.00;
        balanceTwo.Balance__c = balanceTwo.Balance__c - 2.00;

        // Comment__c is a requird field, it was created to simulate a DML Exception.
        balanceOne.Comment__c = 'JayTwo Added 2 Bucks to JayOne';
        balanceTwo.Comment__c = null;

        Savepoint sp = Database.setSavepoint();
        try {
            update balanceOne;

            try {
                update balanceTwo;
            } catch (Exception e) {
                System.debug(e.getMessage());
                Database.rollback(sp);
            }
        } catch (Exception e) {
            System.debug(e.getMessage());
            Database.rollback(sp);
        }


        balanceOne = [SELECT Name, Balance__c, Comment__c FROM Balance__c WHERE Name = 'JayOne'];
        balanceTwo = [SELECT Name, Balance__c, Comment__c FROM Balance__c WHERE Name = 'JayTwo'];

        System.debug(balanceOne.Balance__c);
        System.debug(balanceTwo.Balance__c);
    }
```

next

