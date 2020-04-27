- #[[Missing Semester]] #Lecture
- **Link**: https://missing.csail.mit.edu/2020/data-wrangling/
- **Notes**
    - A pager allows you to visualize content that doesn't fit on your screen in a terminal window, with navigation via vim-like bindings
    - `less` lets you view a file in a pager
    - Data wrangling is manipulating data from a given format into another format
    - `sed` - stream editor
        - modification of `ed`
    - `sed` lets you make changes to the contents of a stream. You can think about it like replacements, but it's almost an entire programming language.
    - `cat ssh.log | sed 's/.*Disconnected from //' | less`
        - pipes the output of a log file through the stream editor `sed` with a function that replaces the first argument (`.*Disconnected from`) with the second arg, an empty string
    - Regular expressions have a number of special characters that match to patterns:
        - #Regex
        - `.` matches any character
        - `*` 0 or more of whatever expression it follows
        - `+` 1 or more of whatever expression it follows
        - `[]` a disjunctive (OR) operator from pattern to input
            - eg. `echo 'bba' | sed 's/[ab]//'` will return `ba`
        - `g` modifier - repeat the replace action ad infinitum
            - eg. `echo 'bba' | sed 's/[ab]//g'` will return ``
        - `()` a conjunctive (AND) operator from pattern to input
            - eg. `echo 'abcaba' | sed -E 's/(ab)*//g'` will return `ca`
            - The `-E` allows `sed`, which is an older tool, to use more modern regex syntax
        - `|` a disjunctive operator relating to the internal pattern
            - eg. `echo 'abcabbc' | sed -E 's/(ab|bc)*//g'` will return `c`
        - `?` 0 or one of whatever expression it follows
            - Also used to modify a `+` or `*` to make it use a non-greedy capture method (ie. only match the first instance of the pattern)
        - `^` matches the beginning of the line
        - `$` matches the end of the line
            - using both `^` and `$` in a regex is called anchoring, which is something you'd want to do to ensure you're matching only discrete lines
    - Regular expressions are hard to debug just by looking at them - use a tool to aid you
        - [Regex 101](regex101.com) is a great tool for debugging
    - Capture groups are a way to remember and reuse a value later in regexes
        - Any group that's in brackets is a capture group
        - They're referenced by `\#` where `#` is the ordinal, 1-indexed, capture group number
    - Regexes don't match across lines
        - `sed` operates on a per-line basis
    - Regexing emails and urls is a nontrivial endeavour, and isn't currently a solved problem
    - `wc` is a word count program
        - adding the `-l` flag (ie `wc -l`) counts lines instead of words
    - `sed` is mostly good for search and replace
    - `sort` sorts a text stream in alphabetical order
        - `sort -u` will omit non-unique lines
    - `awk` is a __column-based__ stream editor, as opposed to sed which is line-based
        - `awk` will parse its input in whitespace-separated columns and operate on those columns separately
        - `awk '{print $2}'` will print only the second column
        - ```BEGIN { rows = 0 }
$1 == 1 && $2 ~ /^c[^ ]*e$/ { rows += $1 }
END { print rows }```
            - Because `awk` is a programming language, we can actually write entire programs using it! Above is a function that counts the number of instances in an input stream where the first column is 1 and the second column starts with an c and ends with an e
    - `bc -l` will evaluate a mathematical input and print it on the next line
        - basically a command line calculator
    - `xargs` takes lines of inputs and turns them into arguments
- **Exercises & Solutions**
    - 1. Take this [short interactive regex tutorial](https://regexone.com/)
        - **ans:** Answers available in the tutorial
    - 2. Find the number of words (in /usr/share/dict/words) that contain at least three `a`s and don’t have a `'s` ending. What are the three most common last two letters of those words? `sed`’s `y` command, or the `tr` program, may help you with case insensitivity. How many of those two-letter combinations are there? And for a challenge: which combinations do not occur?
        - **ans:** This one was a challenge. Still unsure how to get that `'s` - based on a few StackOverflow threads and some docs, it seems as though this requires using a (lookbehind assertion)[https://www.regular-expressions.info/lookaround.html]. Either way, I think I got a good chunk of it, and am satisfied with the knowledge I gained getting this far. Will have to look more into the lookahead/behind anchoring stuff, because it looks to be quite powerful. In the meantime, here was my solution:

Three most common:
```cat /usr/share/dict/words 
| grep -E "([Aa]+.*){3,}" 
| sed -E 's/.*(..)/\1/' 
| uniq -c 
| sort 
| tail -n3```

Number of two letter combos:
```cat /usr/share/dict/words
| grep -E "([Aa]+.*){3,}" 
| sed -E 's/.*(..)/\1/' 
| uniq 
| wc -l```
    - 3. To do in-place substitution it is quite tempting to do something like `sed s/REGEX/SUBSTITUTION/ input.txt > input.txt`. However this is a bad idea, why? Is this particular to `sed`? Use `man sed` to find out how to accomplish this.
        - **ans:** It's a bad idea to use the above method for in-place substitution because it endangers the contents of the original file. Per `sed`'s `man` page, "you risk corruption or partial content in situations where disk space is exhausted, etc." This danger can be circumvented by using the `-i` flag, which will create a backup copy of your original file before attempting to replace in-place
