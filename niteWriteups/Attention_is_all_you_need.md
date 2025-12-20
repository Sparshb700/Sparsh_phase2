# Attention is all you need

>"put it on the gpu," they said. "no one will find it," they said. now it just sits there, paying way too much attention to everything you type. sigh... god forbid it remembers something it shouldn't.

> I was provided with a binary `attention_is_all_you_need`
## Solve:
- Executing the binary, I was shown this,
```
sparsh@LAPTOP-F80QI4V2 /mnt/c/Users/Sparsh Bansal/Downloads/handoutrev2 $ ./attention_is_all_you_need
════════════════════════════════════════════════════════════
          NEURAL FLAG CHECKER v3.0 (ENCRYPTED)
════════════════════════════════════════════════════════════

GPU: NVIDIA GeForce RTX 4060 Laptop GPU
Compute Capability: 8.9

🔐 Decrypting neural network weights on GPU...
  ✓ Embedding weights
  ✓ Position embeddings
  ✓ Position FC1 weights
  ✓ Position FC1 bias
  ✓ Position FC2 weights
  ✓ Position FC2 bias
  ✓ Global FC1 weights
  ✓ Global FC1 bias
  ✓ Global FC2 weights
✓ All weights decrypted and loaded

Flag length: 29
Valid characters: abcdefghijklmnopqrstuvwxyz0123456789{}_

Enter your flag (or 'quit' to exit):

> nite{aaaaaaaaaaaaaaaaaaaaaaa}
Score: -7.6248
→ Try something different
```
- The flag it asked me was supposed to be of `29` characters and valid characters, `abcdefghijklmnopqrstuvwxyz0123456789{}_`
- I solved it using brute-force, first taking `nite{aaaaaaaaaaaaaaaaaaaaaaa}` as the backbone, I replaced every `a` with all other possible characters to check if the score was increased and replaced it with the highest scoring character
- This is the `solve.py` script
```python
import subprocess

VALID_CHARS = "abcdefghijklmnopqrstuvwxyz0123456789_"

def test_flag(flag_str):
    try:
        proc = subprocess.Popen(
            ['./attention_is_all_you_need'],
            stdin=subprocess.PIPE,
            stdout=subprocess.PIPE,
            stderr=subprocess.PIPE,
            text=True
        )
        output, _ = proc.communicate(input=flag_str + "\nquit\n", timeout=10)

        for line in output.split('\n'):
            if 'Score:' in line:
                return float(line.split(':')[1].strip())
        return 0.0
    except Exception as e:
        print(f"Error: {e}")
        return 0.0


current = list("nite{aaaaaaaaaaaaaaaaaaaaaaa}")

print(f"Starting: {''.join(current)}")

initial_score = test_flag(''.join(current))
print(f"Initial score: {initial_score:.4f}")
print()

for pos in range(5, 28):
	print(f"Position {pos}...")

	best_char = current[pos]
	best_score = test_flag(''.join(current))

	for char in VALID_CHARS:
		if char == best_char:
			continue

		current[pos] = char
		score = test_flag(''.join(current))

		if score > best_score:
			best_score = score
			best_char = char

	current[pos] = best_char
	print(f"  Best: '{best_char}' | Flag: {''.join(current)} | Score: {best_score:.4f}")
	print()

	if best_score >= 9.9:
		print("Flag found: ")
		print(''.join(current))
		print(f"Score: {best_score:.4f}")

final_score = test_flag(''.join(current))
print(f"Best found: {''.join(current)}")
print(f"Final score: {final_score:.4f}")

if final_score >= 9.9:
	print("This is the correct flag")

```
`
```bash
Position 26...
  Best: 'i' | Flag: nite{0ops_i_ov3rf1tt3d_ag4ia} | Score: 9.1982

Position 27...
  Best: 'n' | Flag: nite{0ops_i_ov3rf1tt3d_ag4in} | Score: 9.9891

Flag found:
nite{0ops_i_ov3rf1tt3d_ag4in}
Score: 9.9891
Best found: nite{0ops_i_ov3rf1tt3d_ag4in}
Final score: 9.9891
This is the correct flag
```
- Through this I was able to produce this flag `nite{0ops_i_ov3rf1tt3d_ag4in}`

## Flag:
```
nite{0ops_i_ov3rf1tt3d_ag4in}
 ```