# postfixeval
In this learning activity, we will look at approaches to solve the postfixEvaluate() function in the lab.  If you didnt finish the [expressions learning activity](https://github.com/BYUCS235/expressions), download the code or create a new workspace from this git repo.

The algorithm for evaluating postfix expressions is:

1. Empty the operand stack.
2. while there are more tokens
3. Get the next token.
4. if the first character of the token is a digit 
5. Push the integer onto the stack. 
6. else if the token is an operator 
7. Pop the right operand off the stack.
8. Pop the left operand off the stack.
9. Evaluate the operation.
10. Push the result onto the stack.
11. Pop the stack and return the result.

Now download the [code samples](http://higheredbcs.wiley.com/legacy/college/koffman/0471467553/student_source/ch05.zip.gz) from the book for chapter 5 and look at the file "Postfix_Evaluator.cpp".  There are two functions in this file, eval() and eval_op().  These functions will combine to implement the postfixEvaluate function in the lab.  The function eval() implements this algorithm.

First add eval_op() and is_operator() as a private function in ExpressionManager.h along with the string OPERATORS and the operand_stack.  Notice that the example code uses the std:: notation since they dont have the line "using namespace std".  It will work fine even if you have already done this.

```c++
private:
    /** Evaluates the current operator.
        This function pops the two operands off the operand
        stack and applies the operator.
        @param op A character representing the operator
        @throws Syntax_Error if top is attempted on an empty stack
    */
    int eval_op(char op);

    /** Determines whether a character is an operator.
        @param ch The character to be tested
        @return true if the character is an operator
    */
    bool is_operator(char ch) const {
      return OPERATORS.find(ch) != std::string::npos;
    }

    // Data fields
    static const std::string OPERATORS;
    std::stack<int> operand_stack;
```
Now add the implementations to "ExpressionManager.cpp". 
```c++
const std::string ExpressionManager::OPERATORS = "+-*/";
/** Evaluates the current operator.
    This function pops the two operands off the operand
    stack and applies the operator.
    @param op A character representing the operator
    @throws Syntax_Error if top is attempted on an empty stack
*/
int ExpressionManager::eval_op(char op) {
  if (operand_stack.empty()) 
    throw Syntax_Error("Stack is empty");
  int rhs = operand_stack.top();
  operand_stack.pop();
  if (operand_stack.empty())
    throw Syntax_Error("Stack is empty");
  int lhs = operand_stack.top();
  operand_stack.pop();
  int result = 0;
  switch(op) {
  case '+' : result = lhs + rhs;
             break;
  case '-' : result = lhs - rhs;
             break;
  case '*' : result = lhs * rhs;
             break;
  case '/' : result = lhs / rhs;
             break;
  }
  return result;
}
```
The body of eval() in "Postfix_Evaluator.cpp" will become the body of postfixEvaluate() in ExpressionManager.cpp.  Notice that this code uses an istringstream so you will need to include sstream.
```c++
#include <sstream>
#include <cctype>
string ExpressionManager::postfixEvaluate(string postfixExpression)
{
  cout << "In postfixEvaluate"<<endl;
// Be sure the stack is empty
  while (!operand_stack.empty())
    operand_stack.pop();

  // Process each token
  istringstream tokens(expression);
  char next_char;
  while (tokens >> next_char) {
    if (isdigit(next_char)) {
      tokens.putback(next_char);
      int value;
      tokens >> value;
      operand_stack.push(value);
    } else if (is_operator(next_char)) {
      int result = eval_op(next_char);
      operand_stack.push(result);
    } else {
      throw Syntax_Error("Invalid character encountered");
    }
  }
  if (!operand_stack.empty()) {
    int answer = operand_stack.top();
    operand_stack.pop();
    if (operand_stack.empty()) {
      return answer;
    } else {
      throw Syntax_Error("Stack should be empty");
    }
  } else {
    throw Syntax_Error("Stack is empty");
  }
}
```
Notice that eval_op() throws and exception when there are not enough entries on the stack.  In order to make this code work, you need to catch the exception.  Lets change the exception throwing code in eval_op() to:
```c++
  if (operand_stack.empty()) 
    throw 1;
  int rhs = operand_stack.top();
  operand_stack.pop();
  if (operand_stack.empty())
    throw 2;
```
And now we need to catch the exception in postfixEvaluate() to
```c++
      try {
        result = eval_op(next_char);
      }
      catch(int which) {
          cout << "not enough operands";
          return("invalid");
      }
```
You will need to change all the places where you throw an exception in postfixEvaluate to just return("invalid").  And the answer returned should be a string instead of an int, so you will need to use to_string() to convert the integer to a string before you return it.  

The last change you will need to make is to add the modulo operator to the list of operators "%".  You should add % to the operators string in "ExpressionManager.cpp".
```c++
const std::string ExpressionManager::OPERATORS = "+-*/%";
```

And add % to the switch statement in "ExpressionManager.cpp".
```c++
switch(op) {
  case '+' : result = lhs + rhs;
             break;
  case '-' : result = lhs - rhs;
             break;
  case '*' : result = lhs * rhs;
             break;
  case '/' : result = lhs / rhs;
             break;
  case '%' : result = lhs % rhs;
             break;
  }
```

This is a good example of refactoring code to accomplish a new task.
