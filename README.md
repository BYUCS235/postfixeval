# postfixeval
In this learning activity, we will look at the code from chapter 5 of the book and see how we can use it to solve the postfixEvaluate() function in the lab.  If you didnt finish the [expressions learning activity](https://github.com/BYUCS235/expressions), download the code or create a new workspace from this git repo.

Now download the code samples from [chapter 5 of the book](http://bcs.wiley.com/he-bcs/Books?action=chapter&bcsId=2949&itemId=0471467553&chapterId=21529) and look at the file "Postfix_Evaluator.cpp".  There are two functions in this file, eval() and eval_op().  These functions will combine to implement the postfixEvaluate function in the lab.  

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
int Postfix_Evaluator::eval_op(char op) {
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
