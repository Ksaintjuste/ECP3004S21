# Chapter 10: Reading and Writing Files

## What Kinds of Files Are There?

For small projects, you might use a spreadsheet program or word processing software.
For anything larger than a few megabytes, these file formats are cumbersome. 
In contrast, text files are a very efficient storage medium:
they have very little overhead. 

Consider the following exercise.
Open your favorite word processor, such as Microsoft Word, 
and open a blank document and immediately save it with an appropriate extension, 
such as ```empty.docx```.
Then open a spreadsheet program and save a blank spreadsheet with a name like
```empty.xlsx``` in the same folder.
Now save a blank file in a text editor such as Notepad and call it ```empty.txt```. 
Now play a game that you might call "file storage golf."
Which one is the winner? The one with the smallest size of an empty file. 

Ten out of ten times the text file comes out ahead. 
Why? Other file formats often need to store information about
the look of the document in a header, at the top of the file, 
and a footer, at the bottom of the file. 
In contrast, text files only store the contents that you see, 
without any formatting. 

This makes text files, such as csv files, the leading choice for 
storing and processing data. 
A text file is basically a collection of strings, 
which python can handle efficiently. 
This makes python a good choice for processing a large volume
of data stored in text files. 

Another type of file is a database, which we will cover in Chapter 17. 

## Opening a File

First, let's create a simple file. 
Create a folder called ```file_examples```. 
Now create a file in a text editor with the following contents
and save it as ```file_example.txt```.

```python 
First line of text.
Second line of text.
Third line of text.
``` 

Now open Python t oread this file. 




### Specifying Which File You Want

First, you have to set the directory to the folder
in which this file is located. 
The ```os``` module interacts with the operating system
to get and set the current working directory.


```python 
>>> import os
>>> os.getcwd()
'/path/to/my/current/folder'
```

Use the function ```chdir``` to change directories. 

```python
>>> os.chdir('/path/to/my/new/folder')
>>> os.getcwd()
'/path/to/my/new/folder'
``` 

In many versions, you have to use double backslashes instead of 
forard-slashes, since those are special characters.


```python
>>> os.chdir('C:\\path\\to\\my\\new\\folder')
>>> os.getcwd()
'C:\\path\\to\\my\\new\\folder'
``` 

If the location ```my\\new\\folder``` contains the file 
```file_example.txt```, you can read the contents of this file as follows.

```python 
file = open('file_example.txt', 'r')
contents = file.read()
file.close()
print(contents)
``` 

This code block makes a connection to the file ```file_example.txt``` 
and reads those contents in one string. 
It closes the connection and prints those contents to screen. 

```python 
First line of text.
Second line of text.
Third line of text.
``` 

Instead of using the ```os``` module to set the directory, 
you can open the file with the *absolute path* and work in another location. 
To do this, you specify the path the way you would 
to navigate in a UNIX environemnt.
Some examples include:


```python 
file_1 = open('data/data1.txt', 'r')
file_2 = open('../data2.txt', 'r')
file_3 = open('../../../data/data3.txt', 'r')
``` 
In the first case, the file ```data1.txt``` is one folder down, 
within the folder ```data```. 
In the second case, the file ```data2.txt``` is one folder up: 
the folder containing the current folder. 
In the third case, to access the file ```data3.txt```, you have to 
move three folder up and then move into another folder called ```data``, 
which contains the file ```data3.txt```. 




### The ```with``` Statement

The above method ```file_1 = open('data/data1.txt', 'r')``` works, 
most of the time, but when an error occurs, the program will not execute
the ```file.close()``` command to release the file from memory. 
If your program throws an error between the ```open``` and ```close```
statements, this file connection will remain in memory, 
creating a drag on performance. 

To avoid this problem, ust the ```with``` statement. 

```python 
with open('file_example.txt', 'r') as file:
    contents = file.read()

print(contents)
``` 

This has the format of any other kind of code block, 
in which the relevant statements are indented beyond the ```with``` keyword. 
With this approach, if an error occurs, 
the file connection will automatically be released from memory.


## Techniques for Reading Files

Once you have made a connection to a file, 
there are a number of waysto read the contents. 

### The ```read``` Technique

With the ```read``` technique, 
you read the entire contents of the file into a single string. 

We used this method above with 

```python 
with open('file_example.txt', 'r') as file:
    contents = file.read()

print(contents)
``` 

Clearly, for very large files, this can consume a lot of memory. 
It is often better to read the contents in smaller chunks. 
If an integer is passed to ```read```, 
it will read that specified number of characters. 

```python 
with open('file_example.txt', 'r') as example_file:
    first_ten_chars = example_file.read(10)
    the_rest = example_file.read()

print("The first 10 characters:", first_ten_chars)
print("The rest of the file:", the_rest)
``` 


### The ```readlines``` Technique

Instead of reading by the character, 
which may not be a convenient unit to work with, 
since you might not even know how many characters you need at a time, 
you can get the contents of the file organized 
into separate lines with the ```readlines``` function.

```python 
with open('file_example.txt', 'r') as example_file:
    lines = example_file.readlines()

print(lines)
``` 
This prints the following output. 

```python 
['First line of text.\n', 'Second line of text.\n', 'Third line of text.\n']
``` 
It gives a list of strings, each one containing a newline (```\n```) escape sequence. 

Now consider the file ```planets.txt``` that contains the following text. 

```python 
Mercury
Venus
Earth
Mars
``` 
This code block reads the file, 
prints the contents in a list and then loops through that list 
in reverse order using the built-in function ```reversed```. 

```python 
>>> with open('planets.txt', 'r') as planets_file:
...     planets = planets_file.readlines()
...
>>> planets
['Mercury\n', 'Venus\n', 'Earth\n', 'Mars\n']
>>> for planet in reversed(planets):
...     print(planet.strip())
...
Mars
Earth
Venus
Mercury
``` 

You can perform other operations on this list, 
such as sorting the lines first. 


```python 
>>> with open('planets.txt', 'r') as planets_file:
...     planets = planets_file.readlines()
...
>>> planets
['Mercury\n', 'Venus\n', 'Earth\n', 'Mars\n']
>>> for planet in sorted(planets):
...     print(planet.strip())
...
Earth
Mars
Mercury
Venus

``` 


### The ```for line in file``` Technique

Often, it is useful to process the contents of a file
one line at a time. The ```for line in file``` technique
lets you read a file with the functionality of a ```for``` loop
and the efficiency of working with the contents one line at a time. 

```python 
>>> with open('planets.txt', 'r') as data_file:
...     for line in data_file:
...         print(len(line))
...
8
6
6
5
``` 
This allows you to perform arbitrary calculations using each line in sequence. 
You can combine any other commands, potentially stripping away whitespace first. 

```python 
>>> with open('planets.txt', 'r') as data_file:
...     for line in data_file:
...         print(len(line.strip()))
...
7
5
5
4
``` 

### The ```readline``` Technique

Sometimes you want to perform different operations 
depending on the characteristics of the file. 
You could use a series if ```if``` and ```elif``` statements. 
Instead, you can instruct the python interpreter to 
read a single line of the file , without following a pattern, 
using the ```readline``` technique. 

For example, consider the following dataset, contained in
the file ```hopedale.txt```. 

```python 
Coloured fox fur production, HOPEDALE, Labrador, 1834-1842
#Source: C. Elton (1942) "Voles, Mice and Lemmings", Oxford Univ. Press
#Table 17, p.265--266
      22   
      29   
       2   
      16   
      12   
      35   
       8   
      83   
     166   
``` 

Notice that the first line is a description. 
The next two are preceeded by a ```#``` character
and the data begin on the fourth line.
The following code block reads in the data
and skips over the description in the header. 
The following script calculates the total number of fur pelts. 

```python 
with open('hopedale.txt', 'r') as hopedale_file:

    # Read and skip the description line.
    hopedale_file.readline()

    # Keep reading and skipping comment lines until we read the first piece
    # of data.
    data = hopedale_file.readline().strip()
    while data.startswith('#'):
        data = hopedale_file.readline().strip()
        # Do nothing because these lines do not have data.

    # Now we have the first piece of data.  
    # Accumulate the total number of pelts.
    # Convert the string to an integer for the first value in the sum.
    total_pelts = int(data)

    # Read the rest of the data.
    for data in hopedale_file:
        total_pelts = total_pelts + int(data.strip())

print("Total number of pelts:", total_pelts)
``` 

This script produces the following output. 

```python 
Total number of pelts: 373
``` 

We could perform any other calculations with the lines of data, as follows.

```python 
with open('hopedale.txt', 'r') as hopedale_file:

    # Read and skip the description line.
    hopedale_file.readline()

    # Keep reading and skipping comment lines until we read the first piece
    # of data.
    data = hopedale_file.readline().rstrip()
    while data.startswith('#'):
        data = hopedale_file.readline().rstrip()

    # Now we have the first piece of data.
    print(data)

    # Read the rest of the data.
    for data in hopedale_file:
        print(data.rstrip())

``` 

This produces the following output. 

```python 
      22
      29
       2
      16
      12
      35
       8
      83
     166
``` 
Notice the numbers are aligned
because we stripped the whitespace only on the right side, 
using the ```rstrip()``` function. 

## Files Over the Internet

The above examples assume the file is located on our computer system. 
You can read file located on any computer that is available on the Internet.

The ```urllib``` module has tools for reading files with a given URL.
Note that the file can be encoded in a number of ways. 
This example shows how to read a file encoded in UTF-8. 
This uses a function called ```decode``` to decode the file content 
in the form of bytes to obtain legible characters using UTF-8 encoding. 


```python 
import urllib.request
url = 'https://robjhyndman.com/tsdldata/ecology1/hopedale.dat'
with urllib.request.urlopen(url) as webpage:
    for line in webpage:
        line = line.strip()
        line = line.decode('utf-8')
        print(line)

``` 



## Writing Files



```python 
with open('topics.txt', 'w') as output_file:
    output_file.write('Computer Science')

``` 



```python 
with open('topics.txt', 'a') as output_file:
    output_file.write('Software Engineering')

``` 


```python 
from typing import TextIO
from io import StringIO

def sum_number_pairs(input_file: TextIO, output_file: TextIO) -> None:
    """Read the data from input_file, which contains two floats per line
    separated by a space. output_file for writing and, for each line in
    input_file, write a line to output_file that contains the two floats from
    the corresponding line of input_file plus a space and the sum of the two
    floats.
    """

    for number_pair in input_file:
        number_pair = number_pair.strip()
        operands = number_pair.split()
        total = float(operands[0]) + float(operands[1])
        new_line = '{0} {1}\n'.format(number_pair, total)
        output_file.write(new_line)

if __name__ == '__main__':
    with open('number_pairs.txt', 'r') as input_file, \
            open('number_pair_sums.txt', 'w') as output_file:
        sum_number_pairs(input_file, output_file)

``` 


```python 
1.3 3.4
2 4.2
-1 1

``` 

```python 
1.3 3.4 4.7
2 4.2 6.2
-1 1 0.0

``` 


## Writing Example Calls Using ```StringIO```


```python 
>>> from io import StringIO
>>> input_string = '1.3 3.4\n2 4.2\n-1 1\n'
>>> infile = StringIO(input_string)
>>> infile.readline()
'1.3 3.4\n'

``` 

```python 
>>> from io import StringIO
>>> outfile = StringIO()
>>> outfile.write('1.3 3.4 4.7\n')
12
>>> outfile.write('2 4.2 6.2\n')
10
>>> outfile.write('-1 1 0.0\n')
9
>>> outfile.getvalue()
'1.3 3.4 4.7\n2 4.2 6.2\n-1 1 0.0\n'

``` 


```python 
from typing import TextIO
from io import StringIO


def sum_number_pairs(input_file: TextIO, output_file: TextIO) -> None:
    """Read the data from input_file, which contains two floats per line
    separated by a space. output_file for writing and, for each line in
    input_file, write a line to output_file that contains the two floats from
    the corresponding line of input_file plus a space and the sum of the two
    floats.

    >>> infile = StringIO('1.3 3.4\\n2 4.2\\n-1 1\\n')
    >>> outfile = StringIO()
    >>> sum_number_pairs(infile, outfile)
    >>> outfile.getvalue()
    '1.3 3.4 4.7\\n2 4.2 6.2\\n-1 1 0.0\\n'
    """

    for number_pair in input_file:
        number_pair = number_pair.strip()
        operands = number_pair.split()
        total = float(operands[0]) + float(operands[1])
        new_line = '{0} {1}\n'.format(number_pair, total)
        output_file.write(new_line)

if __name__ == '__main__':
    with open('number_pairs.txt', 'r') as input_file, \
            open('number_pair_sums.txt', 'w') as output_file:
        sum_number_pairs(input_file, output_file)

``` 

## Writing Algorithms That Use the File-Reading Techniques


### Skipping the Header



```python 
from typing import TextIO
from io import StringIO

def skip_header(reader: TextIO) -> str:
    """Skip the header in reader and return the first real piece of data.

    >>> infile = StringIO('Example\\n# Comment\\n# Comment\\nData line\\n')
    >>> skip_header(infile)
    'Data line\\n'
    """

    # Read the description line
    line = reader.readline()

    # Find the first non-comment line
    line = reader.readline()
    while line.startswith('#'):
        line = reader.readline()

    # Now line contains the first real piece of data
    return line

def process_file(reader: TextIO) -> None:
    """Read and print the data from reader, which must start with a single
    description line, then a sequence of lines beginning with '#', then a
    sequence of data.

    >>> infile = StringIO('Example\\n# Comment\\nLine 1\\nLine 2\\n')
    >>> process_file(infile)
    Line 1
    Line 2
    """

    # Find and print the first piece of data
    line = skip_header(reader).strip()
    print(line)

    # Read the rest of the data
    for line in reader:
        line = line.strip()
        print(line)

if __name__ == '__main__':
    with open('hopedale.txt', 'r') as input_file:
        process_file(input_file)

``` 





```python 
from typing import TextIO
import time_series

def smallest_value(reader: TextIO) -> int:
    """Read and process reader and return the smallest value after the
    time_series header.

    >>> infile = StringIO('Example\\n1\\n2\\n3\\n')
    >>> smallest_value(infile)
    1
    >>> infile = StringIO('Example\\n3\\n1\\n2\\n')
    >>> smallest_value(infile)
    1
    """

    line = time_series.skip_header(reader).strip()

    # Now line contains the first data value; this is also the smallest value
    # found so far, because it is the only one we have seen.
    smallest = int(line)

    for line in reader:
        value = int(line.strip())

        # If we find a smaller value, remember it.
        if value < smallest:
            smallest = value

    return smallest

if __name__ == '__main__':
    with open('hopedale.txt', 'r') as input_file:
        print(smallest_value(input_file))

``` 




### Dealing with Missing Values in Data


```python 
Coloured fox fur production, Hebron, Labrador, 1834-1839
#Source: C. Elton (1942) "Voles, Mice and Lemmings", Oxford Univ. Press
#Table 17, p.265--266
#remark: missing value for 1836
    55 
   262 
   -   
   102 
   178 
   227 

``` 



```python 
>>> import read_smallest
>>> read_smallest.smallest_value(open('hebron.txt', 'r'))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "./read_smallest.py", line 16, in smallest_value
    value = int(line.strip())
ValueError: invalid literal for int() with base 10: '-'

``` 

```python 
from typing import TextIO
from io import StringIO
import time_series

def smallest_value_skip(reader: TextIO) -> int:
    """Read and process reader, which must start with a time_series header.
    Return the smallest value after the header.  Skip missing values, which
    are indicated with a hyphen.

    >>> infile = StringIO('Example\\n1\\n-\\n3\\n')
    >>> smallest_value_skip(infile)
    1
    """

    line = time_series.skip_header(reader).strip()
    # Now line contains the first data value; this is also the smallest value
    # found so far, because it is the only one we have seen.
    smallest = int(line)

    for line in reader:
        line = line.strip()
        if line != '-':
            value = int(line)
            smallest = min(smallest, value)

    return smallest

if __name__ == '__main__':
    with open('hebron.txt', 'r') as input_file:
        print(smallest_value_skip(input_file))

``` 


### Processing Whitepace-Delimited Data


```python 
Annual Number of Lynx Trapped, MacKenzie River, 1821-1934
#Original Source: Elton, C. and Nicholson, M. (1942)
#"The ten year cycle in numbers of Canadian lynx",
#J. Animal Ecology, Vol. 11, 215--244.
#This is the famous data set which has been listed before in
#various publications:
#Cambell, M.J. and Walker, A.M. (1977) "A survey of statistical work on
#the MacKenzie River series of annual Canadian lynx trappings for the years
#1821-1934 with a new analysis", J.Roy.Statistical Soc. A 140, 432--436.
  269.  321.  585.  871. 1475. 2821. 3928. 5943. 4950. 2577.  523.   98.        
  184.  279.  409. 2285. 2685. 3409. 1824.  409.  151.   45.   68.  213.        
  546. 1033. 2129. 2536.  957.  361.  377.  225.  360.  731. 1638. 2725.        
 2871. 2119.  684.  299.  236.  245.  552. 1623. 3311. 6721. 4245.  687.        
  255.  473.  358.  784. 1594. 1676. 2251. 1426.  756.  299.  201.  229.        
  469.  736. 2042. 2811. 4431. 2511.  389.   73.   39.   49.   59.  188.        
  377. 1292. 4031. 3495.  587.  105.  153.  387.  758. 1307. 3465. 6991.        
 6313. 3794. 1836.  345.  382.  808. 1388. 2713. 3800. 3091. 2985. 3790.        
  674.   81.   80.  108.  229.  399. 1132. 2432. 3574. 2935. 1537.  529.        
  485.  662. 1000. 1590. 2657. 3396.                                            

``` 


```python 
def find_largest(line):
    """ (str) -> int

    Return the largest value in line, which is a whitespace-delimited string
    of integers that each end with a '.'.

    >>> find_largest('1. 3. 2. 5. 2.')
    5
    """

    # The largest value seen so far.
    largest = -1

    for value in line.split():

        # Remove the trailing period.
        v = int(value[:-1])

        # If we find a larger value, remember it.
        if v > largest:
            largest = v

    return largest

``` 

```python 
from typing import TextIO
from io import StringIO
import time_series

def find_largest(line: str) -> int:
    """Return the largest value in line, which is a whitespace-delimited string
    of integers that each end with a '.'.

    >>> find_largest('1. 3. 2. 5. 2.')
    5
    """
    # The largest value seen so far.
    largest = -1
    for value in line.split():
        # Remove the trailing period.
        v = int(value[:-1])
        # If we find a larger value, remember it.
        if v > largest:
            largest = v

    return largest

def process_file(reader: TextIO) -> int:
    """Read and process reader, which must start with a time_series header.
    Return the largest value after the header.  There may be multiple pieces
    of data on each line.

    >>> infile = StringIO('Example\\n 20. 3.\\n 100. 17. 15.\\n')
    >>> process_file(infile)
    100
    """

    line = time_series.skip_header(reader).strip()
    # The largest value so far is the largest on this first line of data.
    largest = find_largest(line)

    # Check the rest of the lines for larger values.
    for line in reader:
        large = find_largest(line)
        if large > largest:
            largest = large
    return largest

if __name__ == '__main__':
    with open('lynx.txt', 'r') as input_file:
        print(process_file(input_file))

``` 


```python 
from typing import TextIO
from io import StringIO

def process_file(reader: TextIO) -> int:
    """Read and process reader, which must start with a time_series header.
    Return the largest value after the header.  There may be multiple pieces
    of data on each line.

    >>> infile = StringIO('Example\\n 20. 3.\\n')
    >>> process_file(infile)
    20
    >>> infile = StringIO('Example\\n 20. 3.\\n 100. 17. 15.\\n')
    >>> process_file(infile)
    100
    """

    # Read the description line
    line = reader.readline()

    # Find the first non-comment line
    line = reader.readline()
    while line.startswith('#'):
        line = reader.readline()

    # Now line contains the first real piece of data

    # The largest value seen so far in the current line
    largest = -1

    for value in line.split():

        # Remove the trailing period
        v = int(value[:-1])
        # If we find a larger value, remember it
        if v > largest:
            largest = v

    # Check the rest of the lines for larger values
    for line in reader:

        # The largest value seen so far in the current line
        largest_in_line = -1

        for value in line.split():

            # Remove the trailing period
            v = int(value[:-1])
            # If we find a larger value, remember it
            if v > largest_in_line:
                largest_in_line = v

        if largest_in_line > largest:
            largest = largest_in_line
    return largest

if __name__ == '__main__':
    with open('lynx.txt', 'r') as input_file:
        print(process_file(input_file))

``` 


### Multiline Records


```python 
COMPND      AMMONIA
ATOM      1  N  0.257  -0.363   0.000
ATOM      2  H  0.257   0.727   0.000
ATOM      3  H  0.771  -0.727   0.890
ATOM      4  H  0.771  -0.727  -0.890
COMPND      METHANOL
ATOM      1  C  -0.748  -0.015   0.024
ATOM      2  O  0.558   0.420  -0.278
ATOM      3  H  -1.293  -0.202  -0.901
ATOM      4  H  -1.263   0.754   0.600
ATOM      5  H  -0.699  -0.934   0.609
ATOM      6  H  0.716   1.404   0.137

``` 



```python 
from typing import TextIO

def read_molecule(reader: TextIO) -> list:
    """Read a single molecule from reader and return it, or return None to
    signal end of file.  The first item in the result is the name of the
    compound; each list contains an atom type and the X, Y, and Z coordinates
    of that atom.
    """

    # If there isn't another line, we're at the end of the file.
    line = reader.readline()
    if not line:
        return None

    # Name of the molecule: "COMPND   name"
    parts = line.split()
    name = parts[1]

    # Other lines are either "END" or "ATOM num atom_type x y z"
    molecule = [name]
    reading = True

    while reading:
        line = reader.readline()
        if line.startswith('END'):
            reading = False
        else:
            parts = line.split()
            molecule.append(parts[2:])

    return molecule

``` 



```python 
from typing import TextIO
from io import StringIO

def read_molecule(reader: TextIO) -> list:
    """Read a single molecule from reader and return it, or return None to
    signal end of file.  The first item in the result is the name of the
    compound; each list contains an atom type and the X, Y, and Z coordinates
    of that atom.

    >>> instring = 'COMPND TEST\\nATOM 1 N 0.1 0.2 0.3\\nATOM 2 N 0.2 0.1 0.0\\nEND\\n'
    >>> infile = StringIO(instring)
    >>> read_molecule(infile)
    ['TEST', ['N', '0.1', '0.2', '0.3'], ['N', '0.2', '0.1', '0.0']]
    """

    # If there isn't another line, we're at the end of the file.
    line = reader.readline()
    if not line:
        return None

    # Name of the molecule: "COMPND   name"
    parts = line.split()
    name = parts[1]

    # Other lines are either "END" or "ATOM num atom_type x y z"
    molecule = [name]

    reading = True
    while reading:
        line = reader.readline()
        if line.startswith('END'):
            reading = False
        else:
            parts = line.split()
            molecule.append(parts[2:])

    return molecule

def read_all_molecules(reader: TextIO) -> list:
    """Read zero or more molecules from reader, returning a list of the
    molecule information.

    >>> cmpnd1 = 'COMPND T1\\nATOM 1 N 0.1 0.2 0.3\\nATOM 2 N 0.2 0.1 0.0\\nEND\\n'
    >>> cmpnd2 = 'COMPND T2\\nATOM 1 A 0.1 0.2 0.3\\nATOM 2 A 0.2 0.1 0.0\\nEND\\n'
    >>> infile = StringIO(cmpnd1 + cmpnd2)
    >>> result = read_all_molecules(infile)
    >>> result[0]
    ['T1', ['N', '0.1', '0.2', '0.3'], ['N', '0.2', '0.1', '0.0']]
    >>> result[1]
    ['T2', ['A', '0.1', '0.2', '0.3'], ['A', '0.2', '0.1', '0.0']]
    """

    # The list of molecule information.
    result = []

    reading = True
    while reading:
        molecule = read_molecule(reader)
        if molecule:  # None is treated as False in an if statement
            result.append(molecule)
        else:
            reading = False
    return result

if __name__ == '__main__':
    molecule_file = open('multimol.pdb', 'r')
    molecules = read_all_molecules(molecule_file)
    molecule_file.close()
    print(molecules)

``` 



## Looking Ahead


```python 
COMPND      AMMONIA
ATOM      1  N  0.257  -0.363   0.000
ATOM      2  H  0.257   0.727   0.000
ATOM      3  H  0.771  -0.727   0.890
ATOM      4  H  0.771  -0.727  -0.890
END
COMPND      METHANOL
ATOM      1  C  -0.748  -0.015   0.024
ATOM      2  O  0.558   0.420  -0.278 
ATOM      3  H  -1.293  -0.202  -0.901
ATOM      4  H  -1.263   0.754   0.600
ATOM      5  H  -0.699  -0.934   0.609
ATOM      6  H  0.716   1.404   0.137 
END

``` 


```python 
from lookahead_2 import read_molecule
from typing import TextIO

def read_all_molecules(reader: TextIO) -> list:
    """Read zero or more molecules from reader,
    returning a list of the molecules read.
    """

    result = []
    line = reader.readline()
    while line:
        molecule, line = read_molecule(reader, line)
        result.append(molecule)

    return result

``` 

```python 
from typing import TextIO

def read_molecule(reader: TextIO, line: str) -> list:
    """Read a molecule from reader, where line refers to the first line of
    the molecule to be read. Return the molecule and the first line after
    it (or the empty string if the end of file has been reached).
    """

    fields = line.split()
    molecule = [fields[1]]


    line = reader.readline()
    while line and not line.startswith('COMPND'):
        fields = line.split()
        if fields[0] == 'ATOM':
            key, num, atom_type, x, y, z = fields
            molecule.append([atom_type, x, y, z])
        line = reader.readline()

    return molecule, line

``` 









## Additional Code Snippets





```python 
>>> file = open('planets.txt', 'r')
>>> for line in file:
...     line = line.strip()
...     print(len(line))
...
7
5
5
4

``` 

```python 
>>> output_file = open('test.txt', 'w')
>>> type(output_file)
<class '_io.TextIOWrapper'>

``` 

```python 
def read_weather_data(r):
    """ (Read weather data from reader r in fixed-width format.  
    The fields are:
         1   8   YYYYMMDD (date)
         9  14   DDMMSS   (latitude)
        15  20   DDMMSS   (longitude)
        21  26   FF.FFF   (temp, deg. C)
        27  32   FF.FFF   (humidity, %)
        33  38   FF.FFF   (pressure, kPa)
    The result is a list of tuples of tuples, 
	where each tuple of tuples is of the form:
    ((Yr, Mo, Day), (Deg, Min, Sec), (Deg, Min, Sec), (Temp, Hum, Press))
    """
    result = []
    for line in r:
        year = int(line[0:4])
        month = int(line[4:6])
        day = int(line[6:8])
        lat_deg = int(line[8:10])
        lat_min = int(line[10:12])
        lat_sec = int(line[12:14])
        long_deg = int(line[14:16])
        long_min = int(line[16:18])
        long_sec = int(line[18:20])
        temp = float(line[20:26])
        hum = float(line[26:32])
        press = float(line[32:38])
        result.append(((year, month, day),
                       (lat_deg, lat_min, lat_sec),
                       (long_deg, long_min, long_sec),
                       (temp, hum, press)))
    return result

``` 

```python 
def read_weather_data(r):
    """ (Read weather data from reader r in fixed-width format.  
    The field widths are:
        4,2,2   YYYYMMDD (date)
        2,2,2   DDMMSS   (latitude)
        2,2,2   DDMMSS   (longitude)
        6,6,6   FF.FFF   (temp, deg. C; humidity, %; pressure, kPa)
    The result is a list of tuples (not tuples of tuples), 
	where each tuple is of the form:
    (YY, MM, DD, DD, MM, SS, DD, MM, SS, Temp, Hum, Press)"""
    fields = ((4, int), (2, int), (2, int),       # date
              (2, int), (2, int), (2, int),       # latitude
              (2, int), (2, int), (2, int),       # longitude
              (6, float), (6, float), (6, float)) # data
    result = []
    # For each record
    for line in r:
        start = 0
        record = []
        # for each field in the record
        for (width, target_type) in fields:
            # convert the text
            text = line[start:start+width]
            field = target_type(text)
            # add it to the record
            record.append(field)
            # move on
            start += width
        # add the completed record to the result
        result.append(record)
    return result

``` 

```python 
   91.3   11.358  13
   96.3   11.355  12.62
   134.6  16.100  12.97
   135.8  16.315  12.02
   174.9  19.205  12.21
   173.2  20.263  11.9
   161.6  16.885  12.02
   176.8  19.441  12.01
   154.9  17.379  12.08
   159.3  16.028  11.8
   136    15.401  11.82
   108.3  13.518  11.94
   109.1  14.023  11.8
   130    14.442  11.78
   137.5  17.916  11.56
   172.7  17.655  11.55
   180.7  21.990  11.68
   184    20.036  11.61
   162.1  19.224  11.91
   147.4  19.367  11.89
   148.5  16.923  12.03
   152.3  18.413  12.27
   126.2  16.616  12.27
   98.9   14.220  12.05

``` 

```python 
def housing(reader):
    """ (file open for reading) -> tuple of (float, float)

    Return a tuple containing the the differences between the housing starts
    and construction contracts in 1983 and in 1984 from reader.
    """

    # The monthly housing starts, in thousands of units.
    starts = []

    # The construction contracts, in millions of dollars.
    contracts = []

    # Read the file, populating the lists.
    for line in reader:
        start, contract, rate = line.split()
        starts.append(float(start))
        contracts.append(float(contract))

    return (sum(starts[12:24]) - sum(starts[0:12]),
            sum(contracts[12:24]) - sum(contracts[0:12]))

if __name__ == "__main__":
    with open('housing.dat', 'r') as input_file:
        print(housing(input_file))

``` 

```python 
def read_housing_data(r):
    """ Read housing data from reader r, returning lists of starts,
    contracts, and rates."""

    starts = []
    contracts = []
    rates = []

    for line in r:
        start, contract, rate = line.split()
        starts.append(float(start))
        contracts.append(float(contract))
        rates.append(rate)

    return (starts, contracts, rates)

def process_housing_data(starts, contracts):
    """ Return the difference between the housing starts and
    construction contracts in 1983 and in 1984."""

    return (sum(starts[12:24]) - sum(starts[0:12]),
            sum(contracts[12:24]) - sum(contracts[0:12]))

if __name__ == "__main__":
    with open('housing.dat', "r") as input_file:
        starts, contracts, rates = read_housing_data(input_file)

    print(process_housing_data(starts, contracts))

``` 

```python 
inception_file = open('inception.py', 'r')
contents = inception_file.read()
print(contents)

``` 

```python 
inception_file = open('inception_10.py', 'r')
first_ten_chars = inception_file.read(10)
the_rest = inception_file.read()
print("The first 10 characters:", first_ten_chars)
print("The rest of the file:", the_rest)

``` 

```python 
inception_file = open('inception.py', 'r')
inception_contents = inception_file.read()
print(inception_contents)

``` 

```python 
import os
tmp = open('tmp.py', 'r')
print os.path.realpath(tmp.name)

``` 


```python 
>>> file = open("planets.txt", "r")

``` 

```python 
1.3 3.4 4.7
2 4.2 6.2
-1 1 0.0

``` 


```python 
input_file = open("hopedale.txt", "r")

# Skip the first line.
input_file.readline()

# Skip the comments.
line = input_file.readline()
while line.startswith('#'):
	line = input_file.readline()

# Now we want to process the rest of the lines.
for line in input_file:
    line = line.strip()
    print line
input_file.close()

``` 

```python 
input_file = open('hopedale.txt', 'r')
for line in input_file:
    line = line.strip()
    print line
input_file.close()

``` 

```python 
def process_file(filename):
    """ Open, read, and print a file."""

    input_file = open(filename, "r")
    for line in input_file:
        line = line.strip()
        print line
    input_file.close()
if __name__ == "__main__":
    process_file(open('hopedale.txt', 'r'))


``` 

```python 
import sys

def process_file(reader):
    """ Read and print the contents of reader."""

    for line in reader:
        line = line.strip()
        print line

if __name__ == "__main__":
	input_file = open('hopedale.txt', 'r')
    process_file(input_file)
    input_file.close()

``` 

```python 
import sys
import urllib.request

def process_file(reader):
    """ Read and print the contents of reader."""

    for line in reader:
        line = line.strip()
        print(line)

if __name__ == "__main__":
    webpage = urllib.request.urlopen(sys.argv[1])
    process_file(webpage)
    webpage.close()

``` 

```python 
""" Display the lines of data.txt from the given starting line number to the
given end line number.

Usage: read_lines_range.py start_line end_line """

import sys

if __name__ == '__main__':
    
    # get the start and end line numbers
    start_line = int(sys.argv[1])
    end_line = int(sys.argv[2])
    
    # read the lines of the file and store them in a list
    data = open('data.txt', 'r')
    data_list = data.readlines()
    data.close()

    # display lines within start to end range    
    for line in data_list[start_line:end_line]:
        print(line.strip())
        
``` 
