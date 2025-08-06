---
mode: agent
description: Write a test for an Apex class
---

Write unit tests for the following Apex class. The test should cover all methods and include assertions to verify the expected behavior.
Use the `@isTest` annotation and follow best practices for writing unit tests in Apex.

Avoid using `System.assertEquals` and `System.assertNotEquals` for assertions. Instead, use the `Assert` namespace for assertions.
The test class should be self-contained and not depend on any external data or state. Use `Test.startTest()` and `Test.stopTest()` to manage test context and governor limits.

Utilize `Test.setMock` to mock any HTTP callouts or other external dependencies.

Utilize `TestFactory` class for fast and flexible test data creation of SObjects.

Avoid if possible doing any DML operations in the test class. Instead, use `Mocker` for stubbing classes and `TypeFactory` for dependency injection.

example of a test method:

```Apex
public static void testMethodWithMocking() {
    // Arrange
    TestFactory factory = new TestFactory('Account')
        .createRecords(10)
        .setField('Name', 'Test Account')
        .setField('Industry', List<String>{'Test Industry', 'Test Industry 2'});

    List<Account> accounts = (List<Account>) factory.getRecords();

    Mocker mocker = new Mocker();

    MyRepo mockRepo = mocker.mock('MyRepo');
    mocker.stub(mockRepo, 'getAccounts').thenReturn(accounts);

    TypeFactory.setMock('MyRepo', mockRepo);

    Test.startTest();
    MyClass.methodCallingGetAccounts();
    Test.stopTest();

    mocker.assertMethodCalled(mockRepo, 'getAccounts').once().verify();
}
```

Run the tests and analyze the output to identify any failing tests and their associated messages. This will help you understand what needs to be fixed in the code.
Next step is to fix the failing tests or the code under test based on the output from the test run.

Note that some tests may fail due to issues in the code under test, while others may fail due to missing or incorrect test data. I want you to fix any simple errors related to the following:

- Syntax errors in the test code
- Incorrect assertions
- Missing test data (forgotten sObject fields etc.)
- Incorrect test setup
- Incorrectly mocked dependencies
- Incorrect exception handling in the scenarios where exceptions are expected

If the issue is found to be more complex, leave the work to a developer who is familiar with the codebase do more in depth analysis and debugging. Do not make any larger changes to the code being tested, as this may lead to unintended side effects or break existing functionality.
Smaller changes are okay, e.g. such as fixing syntax, any renamed variables or methods, or adding missing fields to sObjects.

If any updates are made to the code, it must be redeployed to the org

After deploying the changes, you can re-iterate and run the tests again to ensure that all tests pass successfully. If all tests pass, you can proceed with the deployment of the code changes.

Create a final report in table format of which tests passed and which tests failed, along with the reasons for failure if applicable. Also include which tests were fixed how they were fixed and if they were re-deployed.
Lastly, include which tests have been left for manual debugging and any useful instructions for the developer.

```markdown
| Test Class Name             | Test Method Name                                     | Outcome | Reason for Failure                                                                  | Fixed | Re-deployed | Notes                                                 |
| --------------------------- | ---------------------------------------------------- | ------- | ----------------------------------------------------------------------------------- | ----- | ----------- | ----------------------------------------------------- |
| ProductAttributeServiceTest | testAssignEmptyList                                  | Pass    | N/A                                                                                 | No    | No          |                                                       |
| ProductAttributeServiceTest | testAssignExistingAttribute_ExistingProductAttribute | Fail    | System.IllegalArgumentException: Id cannot be null and must be a valid Product2 Id. | Yes   | Yes         | Fixed the test data by providing a valid Product2 Id. |
