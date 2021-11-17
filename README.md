# Unit-Testing-on-the-Lightning-Platform
Trailhead
# Generate Data for Tests
Create tests that load data from a CSV in @testSetup method
Create and run a test class that uses a @testSetup method.

To get started

Download this CSV file.
Upload the CSV file as a static resource named Mock_Data.
Create a new Test Class named myDataGenerationTests
Create a @testSetup method that loads the Mock_Data csv into Accounts
Create a test method that asserts the presence of 15 accounts

```@isTest
private class myDataGenerationTests {
@TestSetup
    static void loadmyDataGenerationTests(){
        List<sObject> accounts = Test.loadData(Account.SObjectType, 'Mock_Data');
    }
    @isTest static void testLoadAccountsFromStaticResource() {
    List<Account> accts = [SELECT ID FROM Account];
    system.assert(accts.size() == 15, 'expected 16 accounts');
  }
}
```
## Write Positive Tests
Write a positive unit test
Open AccountWrapper_Tests.cls and add a test method that positively tests the isHighPriority() method.
Add a test method to the AccountWrapper_Tests.cls that positively tests the isHighPriority() method
Be sure to use @testSetup methods to produce your test data
Run your unit tests and make sure you have at least 85% code coverage on the AccountWrapper.cls

!!!TO SUCCESSFULLY COMPLETE THE TASK, YOU MUST DO ALL THE ACTIONS THAT ARE SPECIFIED IN THE PREVIOUS AND CURRENT MODULES!!!

```@isTest
private class AccountWrapper_Tests {
  @testSetup
  static void loadTestData(){
    List<Account> accounts = (List<Account>) Test.loadData(Account.SObjectType, 'accountData');
    List<Opportunity> opps = new List<Opportunity>();
    for(Account a : accounts){
      opps.addAll(TestFactory.generateOppsForAccount(a.id, 1000.00, 5));
    } 
    insert opps;
  }

  @isTest static void testPositiveRoundedAveragePrice() {
    List<AccountWrapper> accounts = new List<AccountWrapper>();
    for(Account a : [SELECT ID, Name FROM ACCOUNT]){
      accounts.add(new AccountWrapper(a));
    }
    // sanity check asserting that we have opportunities before executing our tested method.
    List<Opportunity> sanityCheckListOfOpps = [SELECT ID FROM Opportunity];
    System.assert(sanityCheckListOfOpps.size() > 0, 'You need an opportunity to continue');
    Test.startTest();
    for(AccountWrapper a : accounts){
      System.assertEquals(a.getRoundedAvgPriceOfOpps(), 1000.00, 'Expected to get 1000.00');
    }
    Test.stopTest();
  }
    

      @isTest static void testHighPriority() {
        List<AccountWrapper> accounts = new List<AccountWrapper>();
    	for(Account a : [SELECT ID, Name FROM ACCOUNT]){
            accounts.add(new AccountWrapper(a));}

          
            List<Opportunity> opps = new List<Opportunity>();
          	opps = [SELECT Id,Amount FROM Opportunity];
              for(Opportunity opp :opps ) {
                  opp.amount =2000000;
              }
          update opps;
	
    Test.startTest();
      for(AccountWrapper a : accounts){
      System.assertEquals(a.isHighPriority(), true, 'Priority expected to be high');
      System.debug('get rounded price of opps for accounts b:'+ a.getRoundedAvgPriceOfOpps());
    }
    Test.stopTest();
  }
}
```
