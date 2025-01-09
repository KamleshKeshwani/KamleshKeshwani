- 👋 Hi, I’m @KamleshKeshwani
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

import java.util.Stack;

public class Exp {
    public static void main(String[] args) {
        String str = "(3.5+2)*(4/(2-0.5))";
        System.out.println("Result: " + evaluateExpression(str));
    }

    public static double evaluateExpression(String str) {
        Stack<Double> val = new Stack<>();
        Stack<Character> op = new Stack<>();

        for (int i = 0; i < str.length(); i++) {
            char ch = str.charAt(i);

            if (Character.isWhitespace(ch)) {
                continue; // Ignore spaces
            }

            if (Character.isDigit(ch) || ch == '.') { 
                StringBuilder sb = new StringBuilder();
                while (i < str.length() && (Character.isDigit(str.charAt(i)) || str.charAt(i) == '.')) {
                    sb.append(str.charAt(i++));
                }
                i--;
                val.push(Double.parseDouble(sb.toString()));
            } else if (ch == '(') {
                op.push(ch);
            } else if (ch == ')') {
                while (op.peek() != '(') {
                    val.push(applyOperation(op.pop(), val.pop(), val.pop()));
                }
                op.pop(); // Remove '('
            } else if (isOperator(ch)) {
                while (!op.isEmpty() && precedence(op.peek()) >= precedence(ch)) {
                    val.push(applyOperation(op.pop(), val.pop(), val.pop()));
                }
                op.push(ch);
            }
        }

        while (!op.isEmpty()) {
            val.push(applyOperation(op.pop(), val.pop(), val.pop()));
        }

        return val.pop();
    }

    private static boolean isOperator(char ch) {
        return ch == '+' || ch == '-' || ch == '*' || ch == '/';
    }

    private static int precedence(char op) {
        if (op == '+' || op == '-') {
            return 1;
        } else if (op == '*' || op == '/') {
            return 2;
        }
        return 0;
    }

    private static double applyOperation(char op, double b, double a) {
        switch (op) {
            case '+':
                return a + b;
            case '-':
                return a - b;
            case '*':
                return a * b;
            case '/':
                if (b == 0) {
                    throw new ArithmeticException("Division by zero");
                }
                return a / b;
        }
        return 0;
    }
}
