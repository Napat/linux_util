
### Basic  
```
$ echo "Hello World"
Hello World
```

### Enable interpretation of backslash escapes
```
$ echo -e "Hello \nWorld"
Hello
World
$
$ echo -e "Hello \nWorld" | grep orl
World
$
``` 

### Do not output the trailing newline
```
$ echo -n "Hello World"
Hello World$ echo -n "Hello World"
Hello World$
$
### use case: with md5sum (note that correct md5 of "Hello World" is b10a8db164e0754105b7a99be72e3fe5) 
$ echo "Hello World" | md5sum
e59ff97941044f85df5297e1c302d260  -
$
$ echo -n "Hello World" | md5sum
b10a8db164e0754105b7a99be72e3fe5  -
$
```

