
>Look at my digital gurdian. I built it using my custom made neural network written in C. Bad move? eh well.
## Solve

- The challenge has a small neural network written in C which takes 15 float values as input, using which it computes a probability, which on when matching the target value, will print the flag.
- The network has 3 layers, and the activation on input depends on index%4
- i%4 == 0: xor_activate(x, key) converts x to an integer and XORs it with key and then converts it back to double.
- i%4 == 1: tanh(x)
- i%4 == 2: cos(x)
- i%4 == 3: sinh(x/10.0)
- TARGET_PROBABILITY = 0.7331337420 and has a error range of 1e-5.
- We empoly a script to solve this

```
import random, math

INPUT_SIZE = 15
TARGET = 0.7331337420
EPSILON = 1e-5

XOR_KEYS = [
    0x42,0x13,0x37,0x99,0x21,0x88,0x45,0x67,
    0x12,0x34,0x56,0x78,0x9A,0xBC,0xDE
]

W1 = [
 [0.523,-0.891,0.234,0.667,-0.445,0.789,-0.123,0.456],
 [-0.334,0.778,-0.556,0.223,0.889,-0.667,0.445,-0.221],
 [0.667,-0.234,0.891,-0.445,0.123,0.556,-0.789,0.334],
 [-0.778,0.445,-0.223,0.889,-0.556,0.234,0.667,-0.891],
 [0.123,-0.667,0.889,-0.334,0.556,-0.778,0.445,0.223],
 [-0.891,0.556,-0.445,0.778,-0.223,0.334,-0.667,0.889],
 [0.445,-0.123,0.667,-0.889,0.334,-0.556,0.778,-0.234],
 [-0.556,0.889,-0.334,0.445,-0.778,0.667,-0.223,0.123],
 [0.778,-0.445,0.556,-0.667,0.223,-0.889,0.334,-0.445],
 [-0.223,0.667,-0.778,0.334,-0.445,0.556,-0.889,0.778],
 [0.889,-0.334,0.445,-0.556,0.667,-0.223,0.123,-0.667],
 [-0.445,0.223,-0.889,0.778,-0.334,0.445,-0.556,0.889],
 [0.334,-0.778,0.223,-0.445,0.889,-0.667,0.556,-0.123],
 [-0.667,0.889,-0.445,0.223,-0.556,0.778,-0.334,0.667],
 [0.556,-0.223,0.778,-0.889,0.445,-0.334,0.889,-0.556]
]

B1 = [0.1,-0.2,0.3,-0.15,0.25,-0.35,0.18,-0.28]

W2 = [
 [0.712,-0.534,0.823,-0.445,0.667,-0.389],
 [-0.623,0.889,-0.456,0.734,-0.567,0.445],
 [0.534,-0.712,0.389,-0.823,0.456,-0.667],
 [-0.889,0.456,-0.734,0.567,-0.623,0.823],
 [0.445,-0.667,0.823,-0.389,0.712,-0.534],
 [-0.734,0.623,-0.567,0.889,-0.456,0.389],
 [0.667,-0.389,0.534,-0.712,0.623,-0.823],
 [-0.456,0.823,-0.667,0.445,-0.889,0.734]
]

B2 = [0.05,-0.12,0.18,-0.08,0.22,-0.16]

W3 = [0.923,-0.812,0.745,-0.634,0.856,-0.723]
B3 = 0.42


def xor_activate(x, key):
    v = int(x * 1_000_000)
    v ^= key
    return v / 1_000_000.0


def forward(inputs):
    h1 = []
    for j in range(8):
        s = 0
        for i in range(15):
            if i % 4 == 0:
                a = xor_activate(inputs[i], XOR_KEYS[i])
            elif i % 4 == 1:
                a = math.tanh(inputs[i])
            elif i % 4 == 2:
                a = math.cos(inputs[i])
            else:
                a = math.sinh(inputs[i] / 10.0)
            s += a * W1[i][j]
        h1.append(math.tanh(s + B1[j]))

    h2 = []
    for j in range(6):
        s = sum(h1[i] * W2[i][j] for i in range(8))
        h2.append(math.tanh(s + B2[j]))

    out = sum(h2[i] * W3[i] for i in range(6)) + B3
    return 1 / (1 + math.exp(-out))


best = None
best_vec = None

for _ in range(2_000_000):
    v = [random.uniform(-5, 5) for _ in range(15)]
    o = forward(v)
    if best is None or abs(o - TARGET) < best:
        best = abs(o - TARGET)
        best_vec = (v, o)
    if best < EPSILON:
        break

print("OUTPUT:", best_vec[1])
print("INPUTS:")
for x in best_vec[0]:
    print(x)
```

## Flag

```
nite{br0_i5_n0t_g0nn4_b3_t4K1n6_any1s_j0bs_34x}
```