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
## Write Negative Tests 
Write positive and negative unit tests
Everyone loves a good calculator. In your Trailhead Playground is the calculator.cls. Write positive and negative unit tests for the methods found inside.
Create a new class named Calculator_Tests
Write positive and negative tests for the Calculator class
Be sure to use @testSetup methods where appropriate
Run your unit tests and confirm that the code coverage for calculator.cls is 100%
```@isTest
public class Calculator_Tests {

//positive addition
    @isTest public static void additionTest()
    {
       Integer addition= Calculator.addition(6, 7);
        system.assertEquals(addition,13);
    }

//positive substraction
    @isTest public static void substractionTest()
    {
        Integer substraction= Calculator.subtraction(12, 7);
        system.assertEquals(substraction,5); 
    }

//positive Multiplication
    @isTest public static void PositiveMultiplicationTest()
    {
        Integer multi= Calculator.multiply(12, 7);
        system.assertEquals(multi,84); 
    }


//positive division
    @isTest public static void DivisionTest()
    {
        Decimal div= Calculator.divide(12, 7);
        system.assertNotEquals(div,0);  
    }


//multiplication by zero exceptional case
    @isTest public static void multiplicationExceptionTest()
    { 
        List<Boolean> exceptions = new List<Boolean>();
        try{
        Integer multi= Calculator.multiply(12, 0);
           } 
     catch(Calculator.CalculatorException awe){
  {
          if(awe.getMessage().equalsIgnoreCase('It doesn\'t make sense to multiply by zero')){
            exceptions.add(true);
          }
  }   
     
    system.assertNotEquals(null, exceptions, 'expected at least one exception to have occured');}
    }


//Negative Division result exceptional case
        @isTest public static void NegativeResultExceptionTest()
    { 
        List<Boolean> exceptions = new List<Boolean>();
        try{
       Calculator.divide(-12, 3);
           } 
     catch(Calculator.CalculatorException awe){
  
          if(awe.getMessage().equalsIgnoreCase('Division returned a negative value.-4')){
            exceptions.add(true);
          }
  }
           system.assertNotEquals(null, exceptions, 'expected at least one exception to have occured');
     
    }


//Division by zero exceptional case
            @isTest public static void DivisionByZeroExceptionTest()
    { 
        List<Boolean> exceptions = new List<Boolean>();
        try{
       Calculator.divide(12, 0);
           } 
     catch(Calculator.CalculatorException awe){
  {
          if(awe.getMessage().equalsIgnoreCase('you still can\'t divide by zero')){
            exceptions.add(true);
          }
  }
           system.assertNotEquals(null, exceptions, 'expected at least one exception to have occured');}
    }

}
```
## Write Permission-Based Tests
Create a permission-based test
Create a Custom User profile in your Trailhead Playground and give it View All permission on Private_object__c records. Write a positive permission test demonstrating that users with the Custom User profile can view records that they don’t own.
To get started
In Setup, clone the Custom: Support Profile profile and name it ‘Custom User’.
Give the Custom User profile View All permissions on the custom object Private_Object__c. (Listed as Private Objects in the user interface)
Create a new test class named PositivePermission_tests
Write a positive permission unit test showing that users with the Custom User profile can access Private_Object__c records that they do not own
Execute your unit tests and ensure that they all pass
```@isTest
private class PositivePermission_tests {
  @testSetup
  static void testSetup(){
    Account a = TestFactory.getAccount('View For You!', true);
    Private_Object__c po = new Private_Object__c(account__c = a.id, notes__c = 'foo');
    insert po;
  }
  @isTest
  static void PermissionSetTest_Positive() {
    User u = new User(
      ProfileId = [SELECT Id FROM Profile WHERE Name = 'Custom User'].Id,
      LastName = 'last',
      Email = 'Cpt.Awesome@awesomesauce.com',
      UserName = 'Cpt.Awesome.' + DateTime.now().getTime() + '@awesomesauce.com',
      Alias = 'alias',
      TimeZoneSidKey = 'America/Los_Angeles',
      EmailEncodingKey = 'UTF-8',
      LanguageLocaleKey = 'en_US',
      LocaleSidKey = 'en_US'
    );
    System.runAs(u){
      Private_Object__c[] pos;
      Test.startTest();
        pos = [SELECT Id, Account__c, notes__c FROM Private_Object__c];
      Test.stopTest();
      system.assert(pos.size()!= 0, 'a user with the permission set can see respective records');
    }
  }
}
```
## Use Mocks and Stub Objects
Write tests using mocks and stubs
Not every call to a third party service succeeds. So, we need to test both success and failure responses. Write a unit test to cover the ExternalSearch classes’ handling of response codes higher than 200.
Add a unit test method to ExternalSearch_tests.cls that uses the HTTPMockFactory class to return a 500 response
Make sure you have 100% code coverage on the ExternalSearch class
!!!ExternalSearch.apex!!!
```public class ExternalSearch {
	public class ExternalSearchException extends Exception{}
  
  public static string googleIt(String query){
    Http h = new Http();
    HttpRequest req = new HttpRequest();
    req.setEndpoint('https://www.google.com?q='+query);
    req.setMethod('GET');
    HttpResponse res = h.send(req);
    return res.getBody();
  }
}
```
!!!ExternalSearch_Tests.apex!!!
```@isTest
private class ExternalSearch_Tests {
  @isTest
  static void test_method_one() {
    HttpMockFactory mock = new HttpMockFactory(200, 'OK', 'I found it!', new Map<String,String>());
    Test.setMock(HttpCalloutMock.class, mock);
    String result;
    Test.startTest();
      result = ExternalSearch.googleIt('epic search');
    Test.stopTest();
    system.assertEquals('I found it!', result);
  }
     @isTest static void test_method_two() {
    HttpMockFactory mock = new HttpMockFactory(500, 'Internal Server Error', 'server issue!', new Map<String,String>());
    Test.setMock(HttpCalloutMock.class, mock);
    String result;
    Test.startTest();
      result = ExternalSearch.googleIt('server issue');
    Test.stopTest();
    system.assertEquals('server issue!', result); 
  }
}
```


