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
