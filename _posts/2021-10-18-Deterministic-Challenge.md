# Deterministic writeup
So we get a txt like this
```txt
9 18 69
3 61 5
69 93 22
1 36 10
9 18 69
3 61 5
69 93 22
1 36 10
17 27 20
22 28 18
12 89 1
22 28 18
12 89 1
10 93 13
4 93 1
13 29 2
0 33 3
2 93 6
6 54 4
10 93 13
4 93 17
13 29 2
0 33 3
[snip]
```
and we also get a 2 hints like this:

> *The states are correct but just for security reasons, 
each character of the password is XORed with a very super secret key.*

> *"State 0: 69420, State N: 999, flag ends at state N, key length: one"..*

so from this we get an idea of what should be made so there is a liked this and the first number tells the number before it the one in the middle tells the value and the third numbers thells the first number of the next part
## Py script for automating this
```python
#!/bin/python3
import sys
sys.setrecursionlimit(1000000)
ans=[]
def loop(seed,res):
    for i in res:
        if i[0] == str(seed) and i in ans:
            return ans
        if i[0] == str(seed) and i not in ans:
            ans.append(i)
            seed=i[2]
            loop(seed,res)
    
def main():
    split2=[]
    res=[]
    seed=69420
    with open("deterministic.txt", 'r') as file:
        a=file.read()
        split=a.split('\n')
        for i in split:
            split2.append(i.split(" "))
    for i in split2:
        if i not in res:
            res.append(i)
    sorted=loop(seed,res)
    print(sorted)
    for i in sorted:
        print(i[1],end=" ")
    
if __name__ == '__main__':
    main()
```
so now running this with ``python3 script.py | tee log``
will give the following output 
```txt
48 6 28 73 4 8 7 8 14 12 13 73 29 6 73 25 8 26 26 73 29 1 27 6 28 14 1 73 8 5 5 73 29 1 12 73 10 6 27 27 12 10 29 73 26 29 8 29 12 26 73 6 15 73 29 1 12 73 8 28 29 6 4 8 29 8 73 8 7 13 73 27 12 8 10 1 73 29 1 12 73 15 0 7 8 5 73 26 29 8 29 12 71 73 36 8 7 16 73 25 12 6 25 5 12 73 29 27 0 12 13 73 29 6 73 13 6 73 29 1 0 26 73 11 16 73 1 8 7 13 73 8 7 13 73 15 8 0 5 12 13 71 71 73 38 7 5 16 73 29 1 12 73 27 12 8 5 73 6 7 12 26 73 4 8 7 8 14 12 13 73 29 6 73 27 12 8 10 1 73 29 1 12 73 15 0 7 8 5 73 26 29 8 29 12 71 73 48 6 28 73 8 5 26 6 73 15 6 28 7 13 73 29 1 12 73 26 12 10 27 12 29 73 2 12 16 73 29 6 73 13 12 10 27 16 25 29 73 29 1 12 73 4 12 26 26 8 14 12 71 73 48 6 28 73 8 27 12 73 29 27 28 5 16 73 30 6 27 29 1 16 72 72 73 48 6 28 73 26 1 6 28 5 13 73 11 12 73 27 12 30 8 27 13 12 13 73 30 0 29 1 73 29 1 0 26 73 14 0 15 29 72 73 61 1 12 73 25 8 26 26 25 1 27 8 26 12 73 29 6 73 28 7 5 6 10 2 73 29 1 12 73 13 6 6 27 73 0 26 83 73 33 61 43 18 93 28 29 89 36 93 29 93 54 93 93 27 90 54 47 28 60 28 39 54 93 7 45 54 39 89 29 54 45 88 15 47 88 10 60 5 29 72 72 20 33 61 43 18 93 28 29 89 36 93 29 93 54 93 93 27 90 54 47 28 60 28 39 54 93 7 45 54 39 89 29 54 45 88 15 47 88 10 60 5 29 72 72 20
```
now we can use cyberchef for the second part like this:
![[Pasted image 20210910214954.png]]
** the above numbers cleary represent charachters **
so then we bruteforce the xor in cyberchef founds *key=69*
![[Pasted image 20210910215057.png]]
and now we can see the flag