1. What is a word, and why is it hard to define? 
A word is usually a string separated by spaces but it's hard to define because of punctuation, capitalization, contractions like "I'm" speech fillers or fragments like "uh" or "main-" and languages like Chinese or Japanese that have no spaces at all. 

2. What is the difference between word types and word instances? 
Word instances (N) are the total number of words counting repeats. Word types (|V|) are only the distinct words with repeats counted once. 

3. Why does the vocabulary keep growing (Heaps'/Herdan's Law)? 
New content words like names, slang, and technical terms keep appearing while function words stay mostly fixed. This follows Heaps' Law: |V| = k·N^β with β between 0.44 and 0.56 so types grow much slower than instances. 

4. What are morphemes, and why do we need units smaller than words? 
A morpheme is the smallest unit of meaning in a word ("cats" = "cat" + "-s"). We need smaller units because we can't list every word in a dictionary we'll always see unknown words and breaking them into morphemes or subwords lets models build and understand new ones. 

5. How do computers store characters (Unicode, UTF-8)? 
Unicode gives every character a unique code point like ==> U+1F600. UTF-8 encodes those code points into bytes using 1 byte for English letters and up to 4 bytes for others making it compact and universal. 

6. How do we find patterns in text with regular expressions? 
Regular expressions (regex) are patterns used to search, match, or replace text. Symbols like *, +, ?, [], |, and \d or \w let us match specific patterns and in Python we use things like re.search() or re.sub() to apply them. They are often used for pre-tokenization in NLP.
