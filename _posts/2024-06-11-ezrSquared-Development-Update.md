---
title:  "ezr² development update (Jun 12, 2024)"
tags: [Project,Open source,CSharp]
gh-repo: Uralstech/ezrSquared
gh-badge: [star, watch, fork, follow]
readtime: true
---

Yep.

### What I have been up to.

A lot of bugfixes. That's it. I'm not going into details, but just a whole lot of bugfixes in the Parser, Lexer, Interpreter, types. Basically everything.

This post is not going to go into detail about the changes to ezr², but rather the awesome test programs I've come up with!

Considering that there are no built-in functions for the types like string.Length, string.LastIndexOf, etc. I've had to create some brute-forced solutions for that... \>:\)

### A Simple Calculator.

This is a very simple calculator I made. It's not very good, but it was good enough for some testing.

```ezrSquared
function get_input with message: "", skip_spaces: true do
    raw_input: get(message)

    input_components: []

    index: 0

    number: 0
    was_previous_number: false

    while true do
        try do
            char: raw_input < index
        catch value_out_of_range_error do
            if was_previous_number do input_components + number

            stop
        catch as other_error do
            throw_error(other_error)
        end

        if char >= 48 and char <= 57 do
            if not was_previous_number do number: 0

            number: (number * 10) + (char - 48)
            was_previous_number: true
        else do
            if was_previous_number do input_components + number

            was_previous_number: false

            if char ! ` ` or not skip_spaces do input_components + char
        end

        index :+ 1
    end

    return input_components
end

function calculate with input_components do
    result: nothing

    index: 0

    number: 0
    operator: nothing

    while true do
        try do
            component: input_components < index
        catch value_out_of_range_error do
            stop
        catch as other_error do
            throw_error(other_error)
        end

        if type_name_of(component) = "integer" do
            if result = nothing do result: component else do number: component

            if operator ! nothing do
                if operator = `+` do
                    result :+ number
                else if operator = `-` do
                    result :- number
                else if operator = `x` do
                    result :* number
                else if operator = `/` do
                    result :/ number
                end

                operator = nothing
            end
        else do
            operator: component
        end

        index:+ 1
    end

    return result
end

while true do
    input: get_input("Enter calculation: ")
    try do
        if input < 0 = `q` do stop
    end

    show(calculate(input))
end
```

### An Interpreted Calculator!

I've made a lexer, parser and interpreter in ezr²! It doesn't do much, but it can do mathematical calculations!

Warning: It's ~600 lines long!

```ezrSquared
define static
    object utils do
        function char_is_valid_letter with char do (char >= 97 and char <= 122) or (char >= 65 and char <= 90) or char = `_`

        function string_index_of with string, char, start_index do
            string_length: string_length(string)

            if start_index < 0 or start_index >= string_length do return -1
            count from start_index to string_length - start_index as i do
                if string < i = char do return i
            end

            return -1
        end

        function string_last_index_of with string, char, start_index do
            string_length: string_length(string)

            if start_index < 0 or start_index >= string_length do return -1

            last_index: -1
            count from start_index to string_length - start_index as i do
                if string < i = char do last_index: i
            end

            return last_index
        end

        function string_length with string do
            length: 0

            while true do
                try do
                    string < length
                catch value_out_of_range_error do
                    stop
                end

                length:+ 1
            end

            return length
        end

        function string_with_underline with start_position, end_position do
            text: start_position.script
            text_length: string_length(text)

            start_index: start_position.index
            if start_index < text_length and text < start_index = `\n` and end_position.index - start_index = 1 do
                start_index: if start_index - 2 < 0 do 0 else do start_index - 2
            end

            start: string_last_index_of(text, `\n`, if start_index < text_length - 1 do start_index else do text_length - 1) + 1
        
            while start < text_length and text < start in (` `, `\t`, `\n`) do
                start:+ 1
            end

            end_: string_index_of(text, `\n`, start)
            if end_ = -1 do end_: text_length - 1

            result: '  '
            result + (text < (start, end_))
            result + `\n`

            loop_iterations: (if start_position.index - start < 0 do 0 else do start_position.index - start) + 2
            
            count to loop_iterations do
                result + ` `
            end

            loop_iterations: if end_position.index - start_position.index < 1 do 1 else do end_position.index - start_position.index
            count to loop_iterations do
                result + `~`
            end

            result + `\n`
            return result
        end
    end
end

object position do
    define static
        private _none: nothing

        constant function none do
            if _none = nothing do
                _none: position(-1, -1, "", "")
            end

            return _none 
        end
    end

    constant function initialize with index, line, file, script do
        this.index: index
        this.line: line
        this.file: file
        this.script: script
    end

    function advance with current_char: `\0` do
        this.index:+ 1
        
        if current_char = `\n` do
            this.line :+ 1
        end
    end

    function to_string do type_name_of(this) + `(` + this.index + ", " + this.line + `)`
end

object token do
    constant function initialize with type, value, start_position, end_position: nothing do
        this.type: type
        this.value: value
        this.start_position: start_position

        if end_position = nothing do
            this.end_position: copy(this.start_position)
            this.end_position.advance()
        else do
            this.end_position: end_position
        end
    end

    constant function to_string do
        return type_name_of(this) + `(` + this.type + ", " + this.value + `)`
    end
end

object exception do
    define static constant
        invalid_grammar: "Invalid grammar"
        unexpected_character: "Unexpected character"
        undefined_variable: "Undefined variable"
    end

    constant function initialize with title, details, start_position, end_position: nothing do
        this.title: title
        this.details: details
        this.start_position: start_position

        if end_position ! nothing do
            this.end_position: end_position
        else do
            this.end_position: copy(start_position)
            this.end_position.advance()
        end
    end

    function to_string do this.title
        + " in " + this.start_position.file
        + ", line " + this.start_position.line
        + ": " + this.details + `\n`
        + utils.string_with_underline(this.start_position, this.end_position)
end

object lexer do
    constant function initialize with file, script do
        define private
            this.file: file
            this.script: script

            this.position: position(-1, 1, this.file, this.script)
            this.current_char: `\0`
            this.reached_end: false
        end

        advance()
    end

    private function advance do
        this.position.advance(this.current_char)

        try do
            this.current_char: this.script < this.position.index
        catch value_out_of_range_error do
            this.reached_end: true
        end
    end

    function tokenize do
        error: nothing
        tokens: []

        while not this.reached_end do
            if this.current_char = `\r`
                or this.current_char = `\t`
                or this.current_char = ` ` do
                advance()
            else if this.current_char in (`\n`, `;`) do
                tokens + compile_new_lines()
            else if this.current_char in (`+`, `-`, `*`, `/`, `%`, `^`, `(`, `)`) do
                tokens + token(this.current_char, "", copy(this.position))
                advance()
            else if this.current_char >= 48 and this.current_char <= 57 do
                tokens + compile_number()
            else if utils.char_is_valid_letter(this.current_char) do
                tokens + compile_identifier()
            else do
                error: exception(exception.unexpected_character, "Unexpected character '" + this.current_char + `'`, copy(this.position))
                stop
            end
        end

        tokens + token("endoffile", "", copy(this.position))
        return (tokens, error)
    end

    private function compile_new_lines do
        while not this.reached_end and this.current_char in (`\n`, `;`) do
            advance()
        end

        return token("newline", "", copy(this.position))
    end

    private function compile_identifier do
        identifier: ''
        start_position: copy(this.position)

        while not this.reached_end and utils.char_is_valid_letter(this.current_char) do
            identifier + this.current_char
            advance()
        end

        if identifier = "def" do
            return token(identifier, "", start_position, copy(this.position))
        else do
            return token("identifier", identifier, start_position, copy(this.position))
        end
    end

    private function compile_number do
        start_position: copy(this.position)
        number: 0
        
        while not this.reached_end and this.current_char >= 48 and this.current_char <= 57 do
            number: (number * 10) + (this.current_char - 48)
            advance()
        end

        return token("integer", number, start_position, copy(this.position))
    end
end

object node do
    constant function initialize with type, data, start_position, end_position do
        this.type: type
        this.data: data
        this.start_position: start_position
        this.end_position: end_position
    end
    
    constant function to_string do
        return type_name_of(this) + `(` + this.type + ", " + this.data + `)`
    end
end

object result do
    constant function initialize do
        reset()
    end

    function success with value do
        this.value: value
    end

    function failure with priority, error do
        if this.error = nothing or this.error_priority <= priority do
            this.error_priority: priority
            this.error: error
        end
    end

    function reset do
        this.error: nothing
        this.value: nothing
        this.advance_count: 0
        this.error_priority: 0
    end
end

object parser do
    constant function initialize with tokens do
        define private
            this.tokens: tokens
            this.current_token: nothing
            this.result: result()

            this.index: -1
            this.reached_end: false
        end

        advance()
    end

    private function advance with advance_count: 1 do
        this.index:+ advance_count
        this.result.advance_count:+ advance_count

        try do
            this.current_token: this.tokens < this.index
        catch value_out_of_range_error do
            this.reached_end: true
        end
    end

    private function reverse with reverse_count: 1 do
        advance(-reverse_count)
    end

    function parse do
        parse_statements()
        if this.result.error = nothing and this.current_token.type ! "endoffile" do
            this.result.failure(10, exception(exception.invalid_grammar, "Did not expect this!", this.current_token.start_position, this.current_token.end_position))
        end

        return this.result
    end

    private function binary_operation with left, right, operators do
        start_position: this.current_token.start_position

        left()
        if this.result.error ! nothing do return

        left_node: this.result.value
        reverse_to: this.result.advance_count

        if this.current_token.type = "newline" do advance()

        while not this.reached_end and this.current_token.type in operators do
            operator: this.current_token.type
            advance()

            if this.current_token.type = "newline" do advance()
            
            right()
            if this.result.error ! nothing do return

            right_node: this.result.value

            left_node: node("binary_operation", (left_node, right_node, operator), start_position, this.current_token.end_position)
            reverse_to: this.result.advance_count

            if this.current_token.type = "newline" do advance()
        end

        reverse(this.result.advance_count - reverse_to)
        this.result.success(left_node)
    end

    private function parse_statements do
        statements: []
        start_position: this.current_token.start_position
        
        if this.current_token.type = "newline" do advance()

        parse_expression()
        if this.result.error ! nothing do return

        statements + this.result.value
        while true do
            if this.current_token.type = "newline" do
                advance()
            else do
                stop
            end

            if this.current_token.type = "endoffile" do stop

            parse_expression()
            if this.result.error ! nothing do return
            
            statements + this.result.value
        end

        this.result.success(node("array", statements, start_position, this.current_token.end_position))
    end

    private function parse_expression do
        start_position: this.current_token.start_position

        if this.current_token.type = "def" do
            advance()

            if this.current_token.type ! "identifier" do
                this.result.failure(10, exception(exception.invalid_grammar, "Expected an identifier as the 'def' keyword was used!", this.current_token.start_position, this.current_token.end_position))
                return
            end

            identifier: this.current_token.value
            advance()

            parse_expression()
            if this.result.error ! nothing do return

            this.result.success(node("assignment", (identifier, this.result.value), start_position, this.current_token.end_position))
            return
        end

        parse_arithmetic_expression()
        if this.result.error ! nothing do
            this.result.failure(4, exception(exception.invalid_grammar, "Expected an expression!", this.current_token.start_position, this.current_token.end_position))
        end
    end

    private function parse_arithmetic_expression do
        binary_operation(parse_term, parse_term, (`+`,`-`))
    end
    
    private function parse_term do
        binary_operation(parse_factor, parse_factor, (`*`,`/`, `%`))
    end

    private function parse_factor do
        start_position: this.current_token.start_position

        operator: this.current_token.type
        if operator in (`+`, `-`) do
            advance()

            parse_factor()
            if this.result.error ! nothing do return

            this.result.success(node("factor", (operator, this.result.value), start_position, this.current_token.end_position))
            return
        end

        parse_power()
    end

    private function parse_power do
        binary_operation(parse_atom, parse_atom, (`^`,))
    end

    private function parse_atom do
        token: this.current_token

        if token.type = "identifier" do
            this.result.success(node("access", token.value, token.start_position, token.end_position))
        else if token.type = "integer" do
            this.result.success(node("integer", token.value, token.start_position, token.end_position))
        else if token.type = `(` do
            advance()

            parse_expression()
            if this.result.error ! nothing do return

            if this.current_token.type ! `)` do
                this.result.failure(10, exception(exception.invalid_grammar, "Expected ')' as you started a parenthetical expression!", this.current_token.start_position, this.current_token.end_position))
                return
            end
        else do
            this.result.failure(4, exception(exception.invalid_grammar, "Expected an integer, identifier, 'def' expression, and so on.", this.current_token.start_position, this.current_token.end_position))
            return
        end

        advance()
    end
end

object interpreter do
    constant function initialize do
        this.context: {}
        this.result: result()
    end

    function execute with node do
        this.result.reset()
        visit_node(node)

        return this.result
    end

    private function visit_node with node do
        if node.type = "integer" do
            this.result.success(node.data)
        else if node.type = "factor" do
            operator: node.data < 0
            
            visit_node(node.data < 1)
            if this.result.error ! nothing do return

            if operator = `+` do
                this.result.success(+this.result.value)
            else do
                this.result.success(-this.result.value)
            end
        else if node.type = "binary_operation" do
            visit_binary_operation(node)
        else if node.type = "array" do
            visit_array(node)
        else if node.type = "access" do
            visit_access(node)
        else if node.type = "assignment" do
            visit_assignment(node)
        else do
            throw_error(unexpected_type_error("Unexpected node type: " + node.type))
        end
    end

    private function visit_array with node do
        index: 0
        results: []

        while true do
            try do
                statement: node.data < index
            catch value_out_of_range_error do
                stop
            end

            visit_node(statement)
            if this.result.error ! nothing do return

            results + this.result.value
            index:+ 1
        end

        this.result.success(results)
    end

    private function visit_access with node do
        try do
            value: this.context < node.data
        catch key_not_found_error do
            this.result.failure(10, exception(exception.undefined_variable, "Undefined variable \"" + node.data + "\"!", node.start_position, node.end_position))
            return
        end

        this.result.success(value)
    end

    private function visit_assignment with node do
        visit_node(node.data < 1)
        if this.result.error ! nothing do return

        value: this.result.value
        this.context + (node.data < 0, value)

        this.result.success(value)
    end

    private function visit_binary_operation with node do
        visit_node(node.data < 0)
        if this.result.error ! nothing do return

        left: this.result.value

        visit_node(node.data < 1)
        if this.result.error ! nothing do return

        (right, operator): (this.result.value, node.data < 2)
        if operator = `+` do
            this.result.success(left + right)
        else if operator = `-` do
            this.result.success(left - right)
        else if operator = `*` do
            this.result.success(left * right)
        else if operator = `/` do
            this.result.success(left / right)
        else if operator = `%` do
            this.result.success(left % right)
        else if operator = `^` do
            this.result.success(left ^ right)
        else do
            throw_error(unexpected_type_error("Unexpected binary operator \"" + operator + "\"!"))
        end
    end
end

function main do
    interpreter: interpreter()

    while true do
        input: get(">> ")
        if input = `q` do stop
        
        (tokens, error): lexer("shell", input).tokenize()
        if error ! nothing do
            show("LEXER ERROR! " + error + "\nTokens: " + tokens)
        else do
            show(tokens)
        end

        parse_result: parser(tokens).parse()
        if parse_result.error ! nothing do
            show("PARSER ERROR! " + parse_result.error + "\nNode: " + parse_result.value)
        else do
            show(parse_result.value)
        end

        runtime_result: interpreter.execute(parse_result.value)
        if runtime_result.error ! nothing do
            show("RUNTIME ERROR! " + runtime_result.error + "\nValue: " + runtime_result.value)
        else do
            show(runtime_result.value)
        end
    end
end

main()
```

Yeah! That's it for the update!