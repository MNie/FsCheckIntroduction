- title : Property-Based and Random testing
- description : Introduction to testing with FsCheck
- author : Michał Niegrzybowski
- theme : night
- transition : default

***

### What is FsCheck?

- It's a [library](https://github.com/fscheck/FsCheck) written in F# for property-based and random testing. 
- It's integrated with Xunit and Nunit.
- It's a port of Haskell's [QuickCheck library.](https://wiki.haskell.org/Introduction_to_QuickCheck1)
- It has it's own tag on [stackoverflow](http://stackoverflow.com/questions/tagged/fscheck)

![FsCheck](images/logo.png)

***

### What's the problem?

- How to test functions which arguments admit a wide range of values
- How to test functions which arguments needs to fulfill some restriction

---

#### How we solve the problem right now?

- TestSuite in nUnit for various function inputs


    [lang=cs]
    using System;
    using NUnit.Framework;

    [TestFixture]
    class TestSomething
    {
        [TestCase(1, "ene")]
        [TestCase(2, "dułe")]
        [TestCase(3, "rabe")]
        [TestCase(4, "bocian")]
        [TestCase(5, "złapał")]
        [TestCase(6, "żabę")]
        public void TestCos(int paramFirst, string ParamSecond)
        {
            //do magic
            Assert.AreEqual(result, expectedResult);
        }
    }

---

#### How we solve the problem right now?

- Multiple specs


    [lang=cs]
    using developwithpassion.specifications.rhinomocks;
    using Machine.Specifications;

    public class TestSomething : Observes
    {
        Establish that = () => //set something

        class When_something_return_this1
        {
            Because of = () => result = sut.DoSomething(1, "ene");
            It should_return_this = result.ShouldEquals(expectedResult);
        }

        class When_something_return_this2
        {
            Because of = () => result = sut.DoSomething(2, "dułe");
            It should_return_this = result.ShouldEquals(expectedResult);
        }
        //...
    }

---

#### How we solve the problem right now?

- Multiple specs (behaviours)


    [lang=cs]
    using developwithpassion.specifications.rhinomocks;
    using Machine.Specifications;

    public class TestSomething : Observes
    {
        private Establish that = () => //set something

        [Behaviors]
        class ProperTest
        {
            result.ShouldEquals(expectedResult);
        }
        class When_something_return_this1
        {
            Because of = () => result = sut.DoSomething(1, "ene");
            Behaves_like<ProperTest> a_proper_test;
        }

        class When_something_return_this2
        {
            Because of = () => result = sut.DoSomething(2, "dułe");
            Behaves_like<ProperTest> a_proper_test;
        }
        //...
    }

---

### FsCheck for a rescue!

***

### How to start with FsCheck?

- [Documentation](https://fscheck.github.io/FsCheck/)
- [Excelent blog post about how FsCheck works](http://fsharpforfunandprofit.com/posts/property-based-testing/)

---

#### What we should install to start writting tests with FsCheck?

- [FSharpTools](https://chocolatey.org/packages/visualfsharptools) in VisualStudio
- [FSharpPowerTools](https://marketplace.visualstudio.com/items?itemName=FSharpSoftwareFoundation.VisualFPowerTools)
- Get the latest version of a project :)

---

#### How syntax looks like?

    open FsCheck
    open NUnit.Framework

    [<TestFixture>]
    type CosTam() =
        [<Test>]
        member this.``here I want to test if sum works correctly``() =
            let test(input: int[]) =
                let result = input |> Seq.sum
                let reverseSum = input |> Seq.rev |> Seq.sum
                let label = sprintf "Should be %s, gets %s, SOMETHING IS WRONG" 
                    result reverseSum
                (result = reverseSum)
                    .Label(label)
            Check.QuickThrowOnFailure test

---

#### How the pipeline of writing FsCheck test looks like?

- tag class with **TestFixture** attribute
- tag tests method with **Test** attribute
- test method must be public, if it's not, then nunit will throw an exception **method is not public**
- inside test define function with input parameters which should changed in every test execution
- run test as Check.Quick(config) nameOfLocalFunction or Check.QuickThrowOnFailure nameOfLocalFunction
- by default per every test, it is run with different input values 100 times

---

#### Shrinker

- What he does

---

#### Generators

- generators for simple datatypes are available by default
- it is possible to create own generators for more complicated types
- own generators could be register globally or per test

---

#### Custom generator

    type Generators =
        static member muiKastomowyGeneratorek =
            Arb.generate<float>
            |> Gen.suchThat ((<) 0.)
            |> Arb.fromGen

---

#### Global generator registration

    Arb.register<Generators>() |> ignore

---

#### Generator per test

    Check.Quick (forAll Generators.muiKastomowyGeneratorek test)

---

#### Custom configuration

- by default test runs 100 times
- granularity of generated data could be also changed


    let config = {
        Config.Quick with
            MaxTest = 1000 //number of tests to run
            StartSize = 100 //default value is 1
            EndSize = 100 //default value is 100
    }

    Check.One(config, test)

***

### Let's write a simple test

    open FsCheck
    open NUnit.Framework

    [<TestFixture>]
    type CosTam() =
        [<Test>]
        member this.``here I want to test if sum works correctly``() =
            let test(input: int[]) =
                let result = input |> Seq.sum
                let reverseSum = input |> Seq.rev |> Seq.sum
                let label = sprintf "Should be %s, gets %s" 
                    result reverseSum
                (result = reverseSum)
                    .Label(label)
            Check.QuickThrowOnFailure test

---

#### Run

---

#### Results

![VisualStudio](images/visual.png)
![TeamCity](images/teamcity.png)
![TeamCity2](images/teamcity2.png)

***

### Thanks :)

***
