import java.util.Stack;

public class Hello {

    public static double evaluateExpression(String str) {
        // Validate input
        if (str.isEmpty() || !isValidExpression(str)) {
            throw new IllegalArgumentException("Invalid Expression: Cannot start with ')' or '*' or '/'");
        }

        // Stack for numbers and operators
        Stack<Double> val = new Stack<>();
        Stack<Character> op = new Stack<>();

        for (int i = 0; i < str.length(); i++) {
            char ch = str.charAt(i);

            // Ignore whitespaces
            if (Character.isWhitespace(ch)) {
                continue;
            }

            // If it's a digit or a negative number
            if (Character.isDigit(ch) || (ch == '-' && (i == 0 || str.charAt(i - 1) == '(' || isOperator(str.charAt(i - 1))))) {
                StringBuilder sb = new StringBuilder();

                // Include the negative sign if applicable
                if (ch == '-') {
                    sb.append('-');
                    i++;
                }

                while (i < str.length() && (Character.isDigit(str.charAt(i)) || str.charAt(i) == '.')) {
                    sb.append(str.charAt(i++));
                }
                i--; // Adjust for the loop increment
                val.push(Double.parseDouble(sb.toString()));
            }
            // If it's an opening parenthesis
            else if (ch == '(') {
                op.push(ch);
            }
            // If it's a closing parenthesis
            else if (ch == ')') {
                while (!op.isEmpty() && op.peek() != '(') {
                    val.push(applyOperation(op.pop(), val.pop(), val.pop()));
                }
                if (op.isEmpty() || op.pop() != '(') {
                    throw new IllegalArgumentException("Invalid Expression: Mismatched parentheses");
                }
            }
            // If it's an operator
            else if (isOperator(ch)) {
                while (!op.isEmpty() && precedence(op.peek()) >= precedence(ch)) {
                    val.push(applyOperation(op.pop(), val.pop(), val.pop()));
                }
                op.push(ch);
            }
        }

        // Apply remaining operations
        while (!op.isEmpty()) {
            val.push(applyOperation(op.pop(), val.pop(), val.pop()));
        }

        return val.pop();
    }

    // Function to validate the input expression
    private static boolean isValidExpression(String str) {
        char firstChar = str.charAt(0);
        return !(firstChar == ')' || firstChar == '*' || firstChar == '/');
    }

    private static boolean isOperator(char ch) {
        return ch == '+' || ch == '-' || ch == '*' || ch == '/';
    }

    private static int precedence(char op) {
        if (op == '+' || op == '-') return 1;
        if (op == '*' || op == '/') return 2;
        return 0;
    }

    private static double applyOperation(char op, double b, double a) {
        switch (op) {
            case '+': return a + b;
            case '-': return a - b;
            case '*': return a * b;
            case '/': 
                if (b == 0) throw new ArithmeticException("Cannot divide by zero");
                return a / b;
        }
        return 0;
    }
}



public static String preprocessExpression(String str) {
    StringBuilder result = new StringBuilder();
    for (int i = 0; i < str.length(); i++) {
        char current = str.charAt(i);
        result.append(current);

        // Check if ')' is followed by a digit or '('
        if (current == ')' && i + 1 < str.length() &&
            (Character.isDigit(str.charAt(i + 1)) || str.charAt(i + 1) == '(')) {
            result.append('*'); // Add multiplication
        }

        // Check if a digit is followed by '('
        if (Character.isDigit(current) && i + 1 < str.length() && str.charAt(i + 1) == '(') {
            result.append('*'); // Add multiplication
        }
    }
    return result.toString();
}
