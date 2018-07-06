# basics & basics_2
- Python
- Lists
- Numpy

# intermediate 
- dicts 
- pandasintro

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

brics["name_length"] = brics["country"].apply(len) ```
