# 2. tunn3l_v1s10n
## Solve:
- On downloading the file, I first used the `file` command to see the what sort of file it was,
```zsh
sparsh@LAPTOP-F80QI4V2 ~/ctf2 $ file tunn3l_v1s10n
tunn3l_v1s10n: data
```
- It just showed data, so I took a look at the `hexdump` of the file, as I had done some previous challenges where the header of the file had to changed or fixed to recover the file.
```
00000000   42 4D 8E 26  2C 00 00 00  00 00 BA D0  00 00 BA D0  00 00 6E 04  BM.&,.....64..64..n.
00000014   00 00 32 01  00 00 01 00  18 00 00 00  00 00 58 26  2C 00 25 16  ..2...........X&,.%.
00000028   00 00 25 16  00 00 00 00  00 00 00 00  00 00 23 1A  17 27 1E 1B  ..%...........#..'..
```
- The header signature said `BM` (`42 4D`), a quick search on google told me that it was a Windows Bitmap Image. 
- And I was the correct, I could see two "BAD0" bytes located in the hex.
- I looked around the net, and found that those bytes denoted where the pixel array starts, it should be 54 bytes and the other was the DIB header which should be 40 bytes, so I changed the bytes using `hexedit`
```
00000000   42 4D 8E 26  2C 00 00 00  00 00 36 00  00 00 28 00  00 00 6E 04  BM.&,.....6...(...n.
00000014   00 00 32 01  00 00 01 00  18 00 00 00  00 00 58 26  2C 00 25 16  ..2...........X&,.%.
```
- I could see the following image

	![tunnel1.png](images/tunnel1.png)
- The task was still not complete, the image looked incomplete, I then tried to alter the height of the image using `hexedit`, changed the height to around 900 pixels
```
00000000   42 4D 8E 26  2C 00 00 00  00 00 36 00  00 00 28 00  00 00 6E 04  BM.&,.....6...(...n.
00000014   00 00 52 03  00 00 01 00  18 00 00 00  00 00 58 26  2C 00 25 16  ..R...........X&,.%.
```
- Now I got the following image:

	![tunnel2.png](images/tunnel2.png)

## Flag:
```
picoCTF{qu1t3_a_v13w_2020}
```

## Notes and Concepts Learnt:
- I learnt that using `hexedit` or any other hex editing tools, I could alter the contents of the image.
- I learnt how the `magic bytes` or the header information for a `bmp` (Windows Bitmap Image) looked like


# 3. m00nwalk

> Decode this [message](https://jupiter.challenges.picoctf.org/static/fc1edf07742e98a480c6aff7d2546107/message.wav) from the moon.

## Solve:
- On downloading the file, I inspected the format, which was `wav`. So I played it on my system and could only hear static noise of a higher frequency.
- Looking at the first hint, it said "How did pictures from the moon landing get sent back to Earth?", So I googled the entire thing on google. 
- After some websites, I opened up a wikipedia with the title "Apollo 11 missing tapes", It talked about a transmission method called `SSTV` which is Slow-scan television which was used to transmit images from moon to earth.
- I searched SSTV image decoder on google and uploaded the given `wav` file on `sstv-decoder.mathieurenaud.fr`, through that, I was able to recover this image.
	![decoded-image.png](images/decoded-image.png)
- This image had the flag upside-down.
	![sstvflag.png](images/sstvflag.png)

## Flag:
```
picoCTF{beep_boop_im_in_space}
```
## Notes and Concepts Learnt:
- SSTV (Slow-scan television) was a picture transfer protocol used by NASA to transmit images during the Apollo 11 mission from moon to earth.
