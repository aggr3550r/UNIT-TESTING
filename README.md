# UNIT TESTING - Best practices and guidelines

## (1) NAMING - Naming the tests

The name of the test should consist of 3 parts:

* The name of the method being tested
* The scenario for which it is being tested
* The expected behaviour when that scenario is invoke

For example:
```typescript 
    public Add_SingleNumber_ReturnsSameNumber() {
    const stringCalculator = new StringCalculator();

    const actual = stringCalculator.Add("0");

    assert.equal(0, actual);
}
```


## (2) STRUCTURE - Arrange your tests

The _Arrange, Act, assert_ structure should be used for ease of readability 

This pattern consists of 3 main parts which are:

* Arrange: Create/instantiate your objects and set them up as necessary for testing
* Act: Act on the object to guage its response or behaviour
* assert: Make sure that the object's behaviour is what it's expected to be

For instance;

### Bad
```typescript
    public  Add_EmptyString_ReturnsZero() {
    // Arrange
    const stringCalculator = new StringCalculator();

    // assert
    assert.equal(0, stringCalculator.Add(""));
}
```

### Much better structure
```typescript
    public  Add_EmptyString_ReturnsZero()
{
    // Arrange
    const stringCalculator = new StringCalculator();

    // Act
    const actual = stringCalculator.Add("");

    // assert
    assert.equal(0, actual);
}
```


## (3) Avoid Logic in Tests

When writing your unit tests avoid manual string concatenation and logical conditions such as 
`if`, `for`, `while`, `switch`, etc.

If logic in your test seems unavoidable then the test should be further split into two or more smaller units.
This is to compconstely avoid any chance of introducing bugs into your tests. If there are any bugs in your test
suites then they are of no value.

An example of trying to use logic in a unit test would be:

### Bad 
```typescript
    public  Add_MultipleNumbers_ReturnsCorrectResults(){
    const stringCalculator = new StringCalculator();
    const expected = 0;
    const testCases = ["0,0,0", "0,1,2", "1,2,3"];
    for (const test in testCases)
    {
        assert.equal(expected, stringCalculator.Add(test));
        expected += 3;
    }
}
```

Instead, the tests cases should be parameterized. Jest supports parameterized tests and NestJS comes suited with 
Jest out of the box.
- Learn to use write parameterized tests with Jest here: [Jest Parameterized Tests](https://www.javacodegeeks.com/2021/12/parameterized-tests-in-typescript-with-jest.html)


## (4) Avoid multiple acts

The biggest reason why we would not want to test for multiple acts in our unit test is to avoid ambiguity.
When our test suite fails, even if we know which unit test is failing it would be hard to pin-point what
particular act is causing the test to fail. 

When writing your tests, try to only include one Act per test. Common approaches to using only one act include:

* Create a separate test for each act
* Better still, use parameterized tests 


## (5) Validate private methods by unit testing public ones

Private methods are an implementation detail. Usually, only helper methods will be made private and in that case, there will be a 
public facing method that will call into the private ones as part of its implementation. 
Essentially, a private method will never be in isolation so instead of testing every private method directly, we should rather find 
the public methods that use a bunch of these private methods and then test those public methods. Just because a private method returns the 
expected result, does not mean the system that eventually calls the private method uses the result correctly.

const's look at a small example:

```typescript
    public ParseLogLine(input: string): string {
    const sanitizedInput = TrimInput(input);
    return sanitizedInput;
}

    private TrimInput(input: string): string {
    return input.trim();
}
```
The necessary test here should be against `ParseLogLine` rather than against `TrimInput` because that it is the public facing method which uses
`TrimInput`and so if it can be proven to work as expected then the private method it uses is implicitly validated.

In view of this, we have:

```typescript
    public  ParseLogLine_StartsAndEndsWithSpace_ReturnsTrimmedResult()
{
    const parser = new Parser();

    const result = parser.ParseLogLine(" a ");

    assert.equals("a", result);
}
```

