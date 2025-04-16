You can use the `glob` module with regex-like patterns to find files in a directory. 

Example:

```python
import glob

files = glob.glob('/path/to/dir/*.txt')  # Finds all .txt files
```

For more advanced regex, use `os` and `re`:

```python
import os
import re

pattern = re.compile(r'^file_\d+.txt$')
files = [f for f in os.listdir('/path/to/dir') if pattern.match(f)]
```
