# Stack-Based-Calculator-
Advanced stack-based calculator in Python, designed to evaluate complex expressions, convert infix to postfix notation, and handle variable assignments. 

class Node:
    
    def __init__(self, value):
        self.value = value  
        self.next = None 
    
    def __str__(self):
        return "Node({})".format(self.value) 

    __repr__ = __str__
                          

class Stack:
    '''
        >>> x=Stack()
        >>> x.pop()
        >>> x.push(2)
        >>> x.push(4)
        >>> x.push(6)
        >>> x
        Top:Node(6)
        Stack:
        6
        4
        2
        >>> x.pop()
        6
        >>> x
        Top:Node(4)
        Stack:
        4
        2
        >>> len(x)
        2
        >>> x.peek()
        4
    '''
    
    def __init__(self):
        self.top = None
    
    def __str__(self):
        temp=self.top
        out=[]
        while temp:
            out.append(str(temp.value))
            temp=temp.next
        out='\n'.join(out)
        return ('Top:{}\nStack:\n{}'.format(self.top,out))

    __repr__=__str__


    def isEmpty(self):
        # YOUR CODE STARTS HERE
        #checks if there is a top pointer
        return self.top == None

    def __len__(self): 
        # YOUR CODE STARTS HERE
        count = 0
        current = self.top
        #counts the number of nodes in the stack by traversing the linked list 
        while current:
            current = current.next
            count += 1
        return count
    

    def push(self,value):
        # YOUR CODE STARTS HERE
        #creates new node
        new_node = Node(value)
        #adds new node to top of the stack
        new_node.next = self.top
        self.top = new_node

     
    def pop(self):
        # YOUR CODE STARTS HERE
        #if stack is empty return none 
        if self.isEmpty():
            return None
        #if stack isn't empty remove and return the value of the top node
        else: 
            pop_value = self.top.value
            self.top = self.top.next
            return pop_value


    def peek(self):
        # YOUR CODE STARTS HERE
        #if stack is empty return none
        if self.isEmpty():
            return None
        #if stack isnt empty return the value of top node
        else:
            return self.top.value
      
      
class Calculator:
    
    def __init__(self):
        self.__expr = None


    @property
    def getExpr(self):
        return self.__expr

    def setExpr(self, new_expr):
        if isinstance(new_expr, str):
            self.__expr=new_expr
        else:
            print('setExpr error: Invalid expression')
            return None

    def _isNumber(self, txt):
        '''
            >>> x=Calculator()
            >>> x._isNumber(' 2.560 ')
            True
            >>> x._isNumber('7 56')
            False
            >>> x._isNumber('2.56p')
            False
        '''
        # YOUR CODE STARTS HERE
        #remove whitespaces
        txt = txt.strip()
        #check if txt is empty 
        if not txt:
            return False
        
        try:
            #convert txt to float
            float(txt)
            return True
        #if a value error occurs return false
        except ValueError:
            return False
        





    def _getPostfix(self, txt):
        '''
            Required: _getPostfix must create and use a Stack object for expression processing

            >>> x=Calculator()
            >>> x._getPostfix('2 ^ 4')
            '2.0 4.0 ^'
            >>> x._getPostfix('2')
            '2.0'
            >>> x._getPostfix('2.1 * 5 + 3 ^ 2 + 1 + 4.45')
            '2.1 5.0 * 3.0 2.0 ^ + 1.0 + 4.45 +'
            >>> x._getPostfix('2 * 5.34 + 3 ^ 2 + 1 + 4')
            '2.0 5.34 * 3.0 2.0 ^ + 1.0 + 4.0 +'
            >>> x._getPostfix('2.1 * 5 + 3 ^ 2 + 1 + 4')
            '2.1 5.0 * 3.0 2.0 ^ + 1.0 + 4.0 +'
            >>> x._getPostfix('( 2.5 )')
            '2.5'
            >>> x._getPostfix('( 2 { 5.0 } )')
            '2.0 5.0 *'
            >>> x._getPostfix(' 5 ( 2 + { 5 + 3.5 } )')
            '5.0 2.0 5.0 3.5 + + *'
            >>> x._getPostfix ('( { 2 } )')
            '2.0'
            >>> x._getPostfix ('2 * ( [ 5 + -3 ] ^ 2 + { 1 + 4 } )')
            '2.0 5.0 -3.0 + 2.0 ^ 1.0 4.0 + + *'
            >>> x._getPostfix ('[ 2 * ( ( 5 + 3 ) ^ 2 + ( 1 + 4 ) ) ]')
            '2.0 5.0 3.0 + 2.0 ^ 1.0 4.0 + + *'
            >>> x._getPostfix ('( { 2 * { { 5 + 3 } ^ 2 + ( 1 + 4 ) } } )')
            '2.0 5.0 3.0 + 2.0 ^ 1.0 4.0 + + *'
            >>> x._getPostfix('2 * ( -5 + 3 ) ^ 2 + [ 1 + 4 ]')
            '2.0 -5.0 3.0 + 2.0 ^ * 1.0 4.0 + +'

            # In invalid expressions, you might print an error message, but code must return None, adjust doctest accordingly
            # If you are veryfing the expression in calculate before passing to postfix, this cases are not necessary

            >>> x._getPostfix('2 * 5 + 3 ^ + -2 + 1 + 4')
            >>> x._getPostfix('2 * 5 + 3 ^ - 2 + 1 + 4')
            >>> x._getPostfix('2    5')
            >>> x._getPostfix('25 +')
            >>> x._getPostfix(' 2 * ( 5 + 3 ) ^ 2 + ( 1 + 4 ')
            >>> x._getPostfix(' 2 * ( 5 + 3 ) ^ 2 + ( 1 + 4 ]')
            >>> x._getPostfix(' ( 2 * { 5 + 3 ) ^ 2 + ( 1 + 4 ] }')
            >>> x._getPostfix(' 2 * ( 5 + 3 ) ^ 2 + ) 1 + 4 (')
            >>> x._getPostfix('2 * 5% + 3 ^ + -2 + 1 + 4')
        '''

        # YOUR CODE STARTS HERE
        postfixStack = Stack()  # method must use postfixStack to compute the postfix expression
        #adjust the expression to handle implicit multiplication
        adjusted_expression = self._handleImplicitMultiplication(txt)  

        #check if expression is valid and returns none if invalid
        if self._validateExpressionStructure(adjusted_expression) == None:  
            return None

        #split the text into characters
        characters = adjusted_expression.split()
        postfix_expression = ""
    
        #uses a dictionary to group open paranethesis 
        open_parenthesis = {"(": ")", "{": "}", "[": "]"}

        #uses a dictionary to group closed paranthesis
        close_parenthesis = {")": "(", "}": "{", "]": "["}

        #uses a dictionary to group the operators by precedence
        operator_precedence = {"^": 4, "*": 3, "/": 3, "-": 2, "+": 2}

        for char in characters:
            #if the character is a number
            if self._isNumber(char):  
                #add it to the postfix expression
                postfix_expression += f'{float(char)} '
            
            #if character is an open parenthesis
            elif char in open_parenthesis:  
                #push it onto the postfix stack
                postfixStack.push(char)
                
            #if character is a closed parenthesis
            elif char in close_parenthesis:  
                #pop from stack until matching open parenthesis is found
                while not postfixStack.isEmpty() and postfixStack.peek() not in open_parenthesis:
                    postfix_expression += postfixStack.pop() + " "
                postfixStack.pop() 
                
            else:  
                #pop from stack according to precedence and then push the current operator
                while (not postfixStack.isEmpty() and postfixStack.peek() not in open_parenthesis and
                    operator_precedence[char] <= operator_precedence[postfixStack.peek()]):
                    postfix_expression += postfixStack.pop() + " "
                postfixStack.push(char)

        #pop any remaining operators from the stack and add them to the postfix expression
        while not postfixStack.isEmpty():
            postfix_expression += postfixStack.pop() + " "

        #return the computed postfix expression
        return postfix_expression.strip()

    #method to validate the expression's structure and syntax
    def _validateExpressionStructure(self,txt):
        lst = txt.split(" ")
    
        #define the precedence and parenthesis mappings
        precedence_mapping = {"(": 1, "{": 1, "[": 1, "<": 1, "^": 4, "/": 3, "*": 3, "-": 2, "+": 2}
        parenthesis_mapping = {"(": ")", "{": "}", "[": "]", "<": ">"}
        parenthesis_balance = Stack()

        operand_count = 0
        operator_count = 0
        previous_character = None

        for char in lst:
            if char in parenthesis_mapping:  
            #for open parentheses, push to stack
                parenthesis_balance.push(char)
            elif char in parenthesis_mapping.values():  
            #for close parentheses, pop and check for matching open parenthesis
                if parenthesis_balance.isEmpty() or parenthesis_mapping[parenthesis_balance.pop()] != char:
                    return None
            elif self._isNumber(char):  
            #count operands
                operand_count += 1
            elif char in precedence_mapping:  
            #count operators and check for consecutive operators
                operator_count += 1
                if previous_character in precedence_mapping:
                    return None
            previous_character = char

        #ensure balanced parentheses and correct number of operands and operators
        if not parenthesis_balance.isEmpty() or operand_count - 1 != operator_count:
            return None

        return True

    #method to adjust for implicit multiplication in the expression
    def _handleImplicitMultiplication(self,txt):
        #dictionary mapping open and closed paranethesis 
        parenthesis_mapping = {"(": ")", "{": "}", "[": "]", "<": ">"}

        #split the input text into a list of single characters
        lst = txt.split(" ")

        #creates a new list to store modified characters
        new_lst = []


        for i in range(len(lst)):
            #copies each character to the new list 
            new_lst.append(lst[i])
        
        
            if i < len(lst) - 1:
                #add multiplication operator between a number and an open parenthesis 
                if self._isNumber(lst[i]) and lst[i + 1] in parenthesis_mapping:
                    new_lst.append("*")

                #add multiplacation operator between a close paranthesis and a number or between one closed parenthesis and one open parenthesis  
                elif lst[i] in parenthesis_mapping.values() and (self._isNumber(lst[i + 1]) or lst[i + 1] in parenthesis_mapping):
                    new_lst.append("*")

        #return the new_list as a joined string
        return " ".join(new_lst)


    def _postfixExpression(self,txt):
        #validate the scrusture of the expression 
        if self._validateExpressionStructure(txt) is None:
            return None

        #handles the implicit multiplication in the expression
        expression_text = self._handleImplicitMultiplication(txt)
        
        #split the expression into a list of characters
        lst = expression_text.split(" ")

        #initiliazes the postfix stack 
        postfixStack = Stack()

        #initializes an empty string to store the resulting postfix expression
        postfix_expression = ""
        
        #define operator precednece 
        operator_precedence = {"(": 1, "{": 1, "[": 1, "<": 1, "^": 4, "/": 3, "*": 3, "-": 2, "+": 2}
        
        #define open parenthesis mapping
        open_parenthesis = {"(": ")", "{": "}", "[": "]"}

        #define close parenthesis mapping
        close_parenthesis = {")": "(", "}": "{", "]": "["}


        #loops through each character in lst
        for char in lst:
            if char in operator_precedence:
                #if the postfixstack is empty
                if postfixStack.isEmpty():
                    #push the operator into the postfixstack
                    postfixStack.push(char)

                #if the operator is an open parentheses
                elif char == "(":
                    #push it into the postfixstack
                    postfixStack.push(char)

                #if the operator is an less than bracket
                elif char == "<":
                    #push it into the postfixstack
                    postfixStack.push(char)

                #if the operator is an open curly bracket
                elif char == "{":
                    #push it into the postfixstack
                    postfixStack.push(char)

                #if the operator is an open box bracket
                elif char == "[":
                    #push it into the postfixstack
                    postfixStack.push(char)

                #if the operator is a caret and the top of the stack is also a caret
                elif char == "^" and postfixStack.top.value == "^":
                    #push it into the postfixstack
                    postfixStack.push(char)

                #if the operator has lower or equal precedence than the top of the stack, pop the stack and add the popped value to the postfix expression
                else:
                    #push the new operator into the postfixstack
                    while (
                        postfixStack.isEmpty() == False
                        and operator_precedence[char] <= operator_precedence[postfixStack.top.value]
                    ):
                        postfix_expression += postfixStack.pop() + " "
                    postfixStack.push(char)

            #if the current char in lst is a number
            elif self._isNumber(char):
                #add its float equivalent to postfix_expression
                postfix_expression += str(float(char)) + " "

            #if the current char in lst is a closing bracket
            elif char in close_parenthesis:
                #add elements from postfix_stack to postfix_expression until a matching opening bracket is found
                top = postfixStack.top.value
                while (
                    top not in open_parenthesis
                    and not postfixStack.isEmpty()
                ):
                    postfix_expression += postfixStack.pop() + " "
                    top = postfixStack.top.value
                postfixStack.pop()

        #pop remaining operators from the stack and add them to the postfix expression
        while not postfixStack.isEmpty():
            postfix_expression += postfixStack.pop() + " "

        



    @property
    def calculate(self):
        '''
            calculate must call _getPostfix
            calculate must create and use a Stack object to compute the final result as shown in the video lectures
            

            >>> x=Calculator()
            >>> x.setExpr('4 + 3 - 2')
            >>> x.calculate
            5.0
            >>> x.setExpr('-2 + 3.5')
            >>> x.calculate
            1.5
            >>> x.setExpr('4 + 3.65 - 2 / 2')
            >>> x.calculate
            6.65
            >>> x.setExpr('23 / 12 - 223 + 5.25 * 4 * 3423')
            >>> x.calculate
            71661.91666666667
            >>> x.setExpr(' 2 - 3 * 4')
            >>> x.calculate
            -10.0
            >>> x.setExpr('7 ^ 2 ^ 3')
            >>> x.calculate
            5764801.0
            >>> x.setExpr(' 3 * ( [ ( 10 - 2 * 3 ) ] )')
            >>> x.calculate
            12.0
            >>> x.setExpr('8 / 4 * { 3 - 2.45 * [ 4 - 2 ^ 3 ] } + 3')
            >>> x.calculate
            28.6
            >>> x.setExpr('2 * [ 4 + 2 * { 5 - 3 ^ 2 } + 1 ] + 4')
            >>> x.calculate
            -2.0
            >>> x.setExpr(' 2.5 + 3 * ( 2 + { 3.0 } * ( 5 ^ 2 - 2 * 3 ^ ( 2 ) ) * [ 4 ] ) * [ 2 / 8 + 2 * ( 3 - 1 / 3 ) ] - 2 / 3 ^ 2')
            >>> x.calculate
            1442.7777777777778
            >>> x.setExpr('( 3.5 ) [ 15 ]') 
            >>> x.calculate
            52.5
            >>> x.setExpr('3 { 5 } - 15 + 85 [ 12 ]') 
            >>> x.calculate
            1020.0
            >>> x.setExpr("( -2 / 6 ) + ( 5 { ( 9.4 ) } )") 
            >>> x.calculate
            46.666666666666664
            

            # In invalid expressions, you might print an error message, but code must return None, adjust doctest accordingly
            >>> x.setExpr(" 4 + + 3 + 2") 
            >>> x.calculate
            >>> x.setExpr("4  3 + 2")
            >>> x.calculate
            >>> x.setExpr('( ( 2 ) * 10 - 3 * [ 2 - 3 * 2 ) ]')
            >>> x.calculate
            >>> x.setExpr('( 2 ) * 10 - 3 * ( 2 - 3 * 2 ) )')
            >>> x.calculate
            >>> x.setExpr('( 2 ) * 10 - 3 * / ( 2 - 3 * 2 )')
            >>> x.calculate
            >>> x.setExpr(' ) 2 ( * 10 - 3 * ( 2 - 3 * 2 ) ')
            >>> x.calculate
        '''

        if not isinstance(self.__expr,str) or len(self.__expr)<=0:
            print("Argument error in calculate")
            return None

        calcStack = Stack()   # method must use calcStack to compute the  expression

        # YOUR CODE STARTS HERE
        #convert infix notation to postfix notation
        postfix_expr = self._getPostfix(self.getExpr)

        #check if postfix conversion works 
        if postfix_expr == None:
            return None
    
        #loop through the postfix expression
        for i in postfix_expr.split():
            #check if the token is a number
            if self._isNumber(i):
                #push it onto the stack as a float
                calcStack.push(float(i))

            #check if the token is an operator
            elif i in '+-*/^':
                #perform the operation and push the result back onto the stack
                operand2 = calcStack.pop()
                operand1 = calcStack.pop()
                
                #check each operator and perform the appropriate operation 
                if i == '+':
                    result = operand1 + operand2
                elif i == '-':
                    result = operand1 - operand2
                elif i == '*':
                    result = operand1 * operand2
                elif i == '/':
                    result = operand1 / operand2
                elif i == '^':
                    result = operand1 ** operand2
            
                calcStack.push(result)
    
        #return the final result by popping the remaining value from the stack 
        return calcStack.pop()

class AdvancedCalculator:
    '''
        >>> C = AdvancedCalculator()
        >>> C.states == {}
        True
        >>> C.setExpression('a = 5;b = 7 + a;a = 7;c = a + b;c = a * 0;return c')
        >>> C.calculateExpressions() == {'a = 5': {'a': 5.0}, 'b = 7 + a': {'a': 5.0, 'b': 12.0}, 'a = 7': {'a': 7.0, 'b': 12.0}, 'c = a + b': {'a': 7.0, 'b': 12.0, 'c': 19.0}, 'c = a * 0': {'a': 7.0, 'b': 12.0, 'c': 0.0}, '_return_': 0.0}
        True
        >>> C.states == {'a': 7.0, 'b': 12.0, 'c': 0.0}
        True
        >>> C.setExpression('x1 = 5;x2 = 7 [ x1 - 1 ];x1 = x2 - x1;return x2 + x1 ^ 3')
        >>> C.states == {}
        True
        >>> C.calculateExpressions() == {'x1 = 5': {'x1': 5.0}, 'x2 = 7 [ x1 - 1 ]': {'x1': 5.0, 'x2': 28.0}, 'x1 = x2 - x1': {'x1': 23.0, 'x2': 28.0}, '_return_': 12195.0}
        True
        >>> print(C.calculateExpressions())
        {'x1 = 5': {'x1': 5.0}, 'x2 = 7 [ x1 - 1 ]': {'x1': 5.0, 'x2': 28.0}, 'x1 = x2 - x1': {'x1': 23.0, 'x2': 28.0}, '_return_': 12195.0}
        >>> C.states == {'x1': 23.0, 'x2': 28.0}
        True
        >>> C.setExpression('x1 = 5 * 5 + 97;x2 = 7 * { x1 / 2 };x1 = x2 * 7 / x1;return x1 ( x2 - 5 )')
        >>> C.calculateExpressions() == {'x1 = 5 * 5 + 97': {'x1': 122.0}, 'x2 = 7 * { x1 / 2 }': {'x1': 122.0, 'x2': 427.0}, 'x1 = x2 * 7 / x1': {'x1': 24.5, 'x2': 427.0}, '_return_': 10339.0}
        True
        >>> C.states == {'x1': 24.5, 'x2': 427.0}
        True
        >>> C.setExpression('A = 1;B = A + 9;C = A + B;A = 20;D = A + B + C;return D - A')
        >>> C.calculateExpressions() == {'A = 1': {'A': 1.0}, 'B = A + 9': {'A': 1.0, 'B': 10.0}, 'C = A + B': {'A': 1.0, 'B': 10.0, 'C': 11.0}, 'A = 20': {'A': 20.0, 'B': 10.0, 'C': 11.0}, 'D = A + B + C': {'A': 20.0, 'B': 10.0, 'C': 11.0, 'D': 41.0}, '_return_': 21.0}
        True
        >>> C.states == {'A': 20.0, 'B': 10.0, 'C': 11.0, 'D': 41.0}
        True
        >>> C.setExpression('A = 1;B = A + 9;2C = A + B;A = 20;D = A + B + C;return D + A')
        >>> C.calculateExpressions() is None
        True
        >>> C.states == {}
        True
    '''
    
    def __init__(self):
        self.expressions = ''
        self.states = {}

    def setExpression(self, expression):
        self.expressions = expression
        self.states = {}

    def _isVariable(self, word):
        '''
            >>> C = AdvancedCalculator()
            >>> C._isVariable('volume')
            True
            >>> C._isVariable('4volume')
            False
            >>> C._isVariable('volume2')
            True
            >>> C._isVariable('vol%2')
            False
        '''
        # YOUR CODE STARTS HERE
        #returns true if all characters in word are alphabetic as well as at least one character and starts with an alphabetic character 
        return word.isalnum() and word[0].isalpha()
       

    def _replaceVariables(self, expr):
        '''
            >>> C = AdvancedCalculator()
            >>> C.states = {'x1': 23.0, 'x2': 28.0}
            >>> C._replaceVariables('1')
            '1'
            >>> C._replaceVariables('105 + x')
            >>> C._replaceVariables('7 ( x1 - 1 )')
            '7 ( 23.0 - 1 )'
            >>> C._replaceVariables('x2 - x1')
            '28.0 - 23.0'
        '''
        # YOUR CODE STARTS HERE
        #split the input expression into a list
        lst = expr.split()

        #checks for invalid characters and undefined variables
        for char in lst:
            if (self._isVariable(char) and char not in self.states) or (not self._isVariable(char) and char.isalnum() and not char.isdigit()):
                return None
        
        #replaces variables in the expression with their values from self.states
        for char in lst:
            if self._isVariable(char) and char in self.states:
                expr = expr.replace(char,str(self.states[char]))
        
        #return updated expression
        return expr

    
    def calculateExpressions(self):
        self.states = {} 
        calcObj = Calculator()     # method must use calcObj to compute each expression
        # YOUR CODE STARTS HERE
        #initialize an empty dictionary to store the progression of calculations
        calculation_progression = {}

        #split the expressions into a list seperated by semicolons
        expressions = self.expressions.split(';')

        #loop through each expression
        for expression in expressions:
            #split the expression into the left side and right side
            split_exp = expression.split('=')

            #checks if expression is not in the form current_var = expr
            if len(split_exp) != 2:
                split_exp = ['','']
                split_exp[0] = expression[:len('return')]
                split_exp[1] = expression[len('return'):]
            
            #strips the left side variables
            var = split_exp[0].strip()

            #replace any variables in the expression with their corresponding values
            updated_expression = self._replaceVariables(split_exp[1].strip())

            #checks if the variables weren't replaced
            if updated_expression == None:
                #if there is an issue replacing variables return none
                self.states = {}
                return None

            #sets the expression in the calcObj
            calcObj.setExpr(updated_expression)
            
            #calculate the expression and get the value 
            value = calcObj.calculate
            

            #If the expression is not a return statements
            if var != 'return':
                #store its value in the states dictionary
                self.states[var] = value
                #store the current states in the calculation progress dictionary
                calculation_progression[expression] = self.states.copy()
            
            #stores the value in the '_return_' key
            calculation_progression['_return_'] = value

        #return final calculation progress
        return calculation_progression


def run_tests():
    
    import doctest

    #- Run tests in all docstrings
    doctest.testmod(verbose=True)
    
    #- Run tests per class - Uncomment the next line to run doctest by function. Replace Stack with the name of the function you want to test
    doctest.run_docstring_examples(Stack, globals(), name='HW3',verbose=True)   

if __name__ == "__main__":
    
    run_tests()
