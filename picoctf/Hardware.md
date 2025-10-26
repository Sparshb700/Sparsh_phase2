
# 2. I like Logic
>i like logic and i like files, apparently, they have something in common, what should my next step be.

## Solve:
- The challenge made me download a `.sal` file. On further looking on google, I found that `.sal` files were produced by a software called `Saleae Logic Software`. So I installed the software on my system and opened the file.
	![saleae.png](images/saleae.png)
- I had no idea how to proceed further. I googled around and landed on a youtube video titled `Decoding UART, I2C and a non-standard signal // With Saleae logic analyzer and temperature sensors`.
- Through that I was able to analyze that this was a `UART` signal, and could be analyzed through a Analyzer in the software called "Async Serial".
	![saleae2.png](images/saleae2.png)
- Then in the analyzer's tab, I could look at the decoded text from the signal.
	![saleae3.png](images/saleae3.png)
- The decoded text had the flag
	![saleae4.png](images/saleae4.png)

## Flag:
```
FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}
```

## Notes and Concepts Learnt:
- I learnt what are `.sal` extension files
- UART signals are used for serial data communication between devices.
- I learnt the usage of the `Saleae Logic` software and how it used to analyze signals.
