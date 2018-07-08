# basics & basics_2
- Python
- Lists
- Numpy

# intermediate 
- dicts
``` python
langs_count = {}
col = df['lang'] # say col is a column from df dataframe
for entry in col:
    # If the language is in langs_count, add 1
    if entry in langs_count.keys():
        langs_count[entry] = langs_count[entry]+1;
    # Else add the language to langs_count, set the value to 1
    else:
        langs_count[entry] = 1
```
- pandasintro
``` python
# Extract column from DataFrame: col
col = df['lang']
print(list(df.columns.values)) # to print column names in pandas data frame
print(df.iloc[0]) # to see first row how it looks
```

# intermediate_2
- conditions
``` python
ends= np.array(ends)
print(len(ends[ends>=60]))
```
- loops
- Dictionary = for key,val in my_dict.items():
- Numpy Array = for val in np.nditer(my_array):
- Data Frame = for lab, row in brics.iterrows() :
sometimes we prefer apply function for creation of new columns
``` python 
for lab, row in brics.iterrows() :
    brics.loc[lab, "name_length"] = len(row["country"])

brics["name_length"] = brics["country"].apply(len) 
```
columns check in data frame
 ``` if col_name not in df.columns: 
 ```
# Python toolbox 1
- Functions
- Tuples & in Functions
- Functions in dataframs by Pandas
- Functions - variables in Local & global scope
``` python def func1():
    num = 3
    print(num)

def func2():
    global num
    double_num = num * 2
    num = 6
    print(double_num) 
```
- Nested Functions
``` python
# Define echo
def echo(n):
    """Return the inner_echo function."""
    def inner_echo(word1):
        """Concatenate n copies of word1."""
        echo_word = word1 * n
        return echo_word
    return inner_echo
twice = echo(2)
two = echo(2)('hello') # => we can also do currying
thrice = echo(3)
three = echo(3)('hello')
print(twice('hello'), thrice('hello')) #closures
print(two, three)
```
- Scopes
    - Local Scope
    - Enclosing Functions (nonlocal to change variable even in outer enclosing function level)
    - Global ( to change variable in global level from a function)
    - Built in
    
- Default & Flexible arguments
``` python
# default
def shout_echo(word1, echo=1, intense=False):
...
with_big_echo = shout_echo('Hey',5,True)
big_no_echo = shout_echo('Hey',intense=True)


# multiple at once
def gibberish(*args):
...
many_words = gibberish("luke", "leia", "han", "obi", "darth")


# need key values both from assignment
def report_status(**kwargs):
...
report_status(name="luke", affiliation="jedi", status="missing")

```
- Lambda functions
``` python
lambda item1,item2: item1+item2
lambda member: len(member)>6
lambda item: item+"!!!"
```
- error handling

    
