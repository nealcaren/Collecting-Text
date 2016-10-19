

```python
!jupyter nbconvert  --to markdown /Users/nealcaren/Documents/Collecting-Text/*.ipynb  --output-dir Users/nealcaren/Documents/Collecting-Text/markdown/
```

    [NbConvertApp] Converting notebook /Users/nealcaren/Documents/Collecting-Text/chapter_03.ipynb to markdown
    [NbConvertApp] Writing 45824 bytes to Users/nealcaren/Documents/Collecting-Text/markdown/chapter_03.md
    [NbConvertApp] Converting notebook /Users/nealcaren/Documents/Collecting-Text/pre_git.ipynb to markdown
    [NbConvertApp] Writing 889 bytes to Users/nealcaren/Documents/Collecting-Text/markdown/pre_git.md



```python
!rsync -va /Users/nealcaren/Documents/Collecting-Text/images /Users/nealcaren/Documents/Collecting-Text/markdown/
```

    building file list ... done
    
    sent 136 bytes  received 20 bytes  312.00 bytes/sec
    total size is 1390319  speedup is 8912.30



```python

```
