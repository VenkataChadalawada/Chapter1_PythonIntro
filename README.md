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
    - try except
    ``` python
    try:
        col = df[col_name]
        for entry in col:
            if entry in cols_count.keys():
                cols_count[entry] += 1
            else:
                cols_count[entry] = 1
        return cols_count
    except:
        print('The DataFrame does not have a '+ col_name + ' column.')
    ```
    - raise ValueError
    ``` python
     # Raise a ValueError if col_name is NOT in DataFrame
    if col_name not in df.columns:
        raise ValueError('The DataFrame does not have a '+col_name+' column.')
    ```
    # Python toolbox 2
    - user defined functions
    - iterators
    - List comprehensions
    - Generators
    
    # Importing Data Python - 1
    - textfiles
    ``` javascript
    import numpy as np
    import matplotlib.pyplot as plt
    # Assign filename: file
    file = 'sea_slug.txt'

    # Import file: data
    data = np.loadtxt(file, delimiter='\t', dtype=str)
    ```
    - Flatfiles CSV
    ``` javascript
    import pandas as pd
    import numpy as np
    # Assign the filename: file
    file = 'titanic.csv'

    # Read the first 5 rows of the file into a DataFrame: data
    data = pd.read_csv(file,nrows=5,header=None).head()

    # Build a numpy array from the DataFrame: data_array
    data_array = np.array(data)
    ```
    - Pickle files
    ``` javascript
    import pickle
    with open('pickled_fruit.pkl', 'rb') as file:
        data = pickle.load(file)
    print(data)
    ```
    - Excel Spread sheets
    ``` javascript
    # Import pandas
    import pandas as pd

    # Assign spreadsheet filename: file
    file = 'battledeath.xlsx'

    # Load spreadsheet: xl
    xl = pd.ExcelFile(file)

    # Print sheet names
    print(xl.sheet_names)
    ```
    - SAS file
    ``` javascript
    import pandas as pd
    # before this ensure you have installed sas7bdat -  pip install sas7bdat
    from sas7bdat import SAS7BDAT
    with SAS7BDAT('sales.sas7bdat') as file:
        df_sas = file.to_data_frame()
    ```
    - Stata
    ``` javascript
    # Import pandas
    import pandas as pd

    # Load Stata file into a pandas DataFrame: df
    df = pd.read_stata('disarea.dta')

    # Print the head of the DataFrame df
    print(df.head())
    ```

    - Matlab file
    ``` javascript
    import scipy.io
    import matplotlib.pyplot as plt
    import numpy as np
    # Print the keys of the MATLAB dictionary
    mat = scipy.io.loadmat('albeck_gene_expression.mat')
    print(mat.keys())
    data = mat['CYratioCyt'][25, 5:]
    ```
    - HDF5
    ``` javascript
    import h5py
    # Get the HDF5 group: group
    data = h5py.File('LIGO_data.hdf5','r')
    group = data['strain']

    # Check out keys of group
    for key in group.keys():
        print(key)

    # Set variable equal to time series data: strain
    strain = data['strain']['Strain'].value
    print(strain[0:5])
    ```
    
    # Importing Data Python - 1 DBs
    - Querying SQL type DB's
    
    ``` javascript
    from sqlalchemy import create_engine
    import pandas as pd
    engine= create_engine('sqlite:///Chinook.sqlite')
    #table_names = engine.table_names()
    con = engine.connect()
    rs = con.execute('SELECT * FROM Album')
    df = pd.DataFrame(rs.fetchall())
    con.close()
    print(df.head())
    ```
    - pandas way
    ``` javascript
    # Execute query and store records in DataFrame: df
    df = pd.read_sql_query('SELECT * FROM PlaylistTrack INNER JOIN Track ON PlaylistTrack.TrackId = Track.TrackId WHERE Milliseconds <250000', engine)

    # Print head of DataFrame
    print(df.head())
    ```
