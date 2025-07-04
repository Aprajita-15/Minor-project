<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Infix to Postfix Converter</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="min-h-screen bg-gray-200 flex items-center justify-center p-4">
    <div class="bg-white p-6 rounded-lg shadow-lg w-full max-w-md">
        <h1 class="text-2xl font-bold text-center text-gray-800 mb-6">Infix to Postfix Converter</h1>
        <div class="mb-4">
            <label for="infix" class="block text-sm font-medium text-gray-600 mb-2">Enter Infix Expression:</label>
            <input type="text" id="infix" placeholder="e.g., (A+B)*C" 
                   class="w-full p-3 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
        </div>
        <button onclick="convert()" 
                class="w-full bg-pink-600 text-white p-3 rounded-md hover:bg-pink-700 transition duration-200">
            Convert
        </button>
        <div id="output" class="mt-4 p-4 bg-gray-50 rounded-md min-h-[60px] text-gray-800"></div>
    </div>

    <script>
        function isOperator(c) {
            return ['+', '-', '*', '/', '^'].includes(c);
        }

        function getPrecedence(op) {
            if (op === '^') return 3;
            if (op === '*' || op === '/') return 2;
            if (op === '+' || op === '-') return 1;
            return -1;
        }

        function infixToPostfix(infix) {
            let stack = [];
            let postfix = '';
            
            for (let c of infix) {
                if (!/[a-zA-Z0-9+\-*/^() ]/.test(c)) {
                    throw new Error('Invalid character in expression');
                }

                if (c === ' ') continue;

                if (/[a-zA-Z0-9]/.test(c)) {
                    postfix += c;
                }
                else if (c === '(') {
                    stack.push(c);
                }
                else if (c === ')') {
                    while (stack.length > 0 && stack[stack.length - 1] !== '(') {
                        postfix += stack.pop();
                    }
                    if (stack.length === 0) {
                        throw new Error('Mismatched parentheses');
                    }
                    stack.pop();
                }
                else if (isOperator(c)) {
                    while (stack.length > 0 && stack[stack.length - 1] !== '(' 
                           && getPrecedence(stack[stack.length - 1]) >= getPrecedence(c)) {
                        postfix += stack.pop();
                    }
                    stack.push(c);
                }
            }

            while (stack.length > 0) {
                if (stack[stack.length - 1] === '(') {
                    throw new Error('Mismatched parentheses');
                }
                postfix += stack.pop();
            }

            return postfix;
        }

        function convert() {
            const infix = document.getElementById('infix').value;
            const outputDiv = document.getElementById('output');
            
            try {
                if (!infix.trim()) {
                    throw new Error('Please enter an infix expression');
                }
                const postfix = infixToPostfix(infix);
                outputDiv.textContent = `Postfix: ${postfix}`;
                outputDiv.classList.remove('text-red-600');
            } catch (error) {
                outputDiv.textContent = `Error: ${error.message}`;
                outputDiv.classList.add('text-red-600');
            }
        }
    </script>
</body>
</html>
