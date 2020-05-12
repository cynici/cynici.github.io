# Getting started with Test Driven Development

*[an excerpt]*

When discussing TDD, we consider a task to be a subset of a requirement
that can be implemented in a few days or less (Beck and Fowler2001).
TDD software engineers develop production code through rapid iterations
of the following:

## Minute-by-minute cycles:

– Writing a small number of new and failing automated unit test case(s)
  for the task at hand

–Implementing code which should allow the new unit tests cases to pass

– Re-running the new unit test cases to ensure they now pass with the
  new code

##  Per-task cycles:

– Integrate code and tests for new code into existing code base

– Re-running all the test cases in the code base to ensure the
  new code does not break any previously running test cases

– Refactoring the implementation or test code (as necessary)

– Re-running all tests in the code base to ensure that the
  refactored code does not break any previously passing test cases

In TDD, code development is kept within the developer’s intellectual
control because he or she is continuously making small design and
implementation decisions and increasing functionality at a
relatively consistent rate. New functionality is not considered
properly implemented unless the new unit test cases and every other
unit test cases written for the code base run properly.


*Go on to the source for some case studies and benefits of TDD,
<https://www.microsoft.com/en-us/research/wp-content/uploads/2009/10/Realizing-Quality-Improvement-Through-Test-Driven-Development-Results-and-Experiences-of-Four-Industrial-Teams-nagappan_tdd.pdf>*
