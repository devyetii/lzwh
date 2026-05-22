# LZWH — Hybrid Compression Algorithm (LZW + Huffman)

A C++ experiment that aims to combine **LZW dictionary-based compression** with **Huffman entropy coding** to achieve better compression ratios than either algorithm alone.

---

## Concept

The idea behind LZWH is a two-stage compression pipeline:

1. **LZW (Lempel-Ziv-Welch)** — First pass replaces repeating patterns with dictionary codes, reducing redundancy in the input stream.
2. **Huffman Encoding** — Second pass encodes the LZW output symbols using variable-length prefix codes based on symbol frequency, further reducing the bitstream size.

By pairing LZW's pattern-matching strength with Huffman's frequency-based bit optimization, the goal is to produce a tighter compressed representation for text and binary data.

---

## Project Structure

```
.
├── compressor.cpp   # LZW encoder/decoder with 3-byte fixed-width codes
├── Node.cpp         # Huffman tree node definition (frequency, entity, left/right children)
├── lzwh.cpp         # CLI entry point — encode / decode / help
└── functions.cpp    # (Referenced in lzwh.cpp but currently missing from repo)
```

### Files

| File | Purpose |
|------|---------|
| `compressor.cpp` | Core LZW engine. Initializes a 256-entry dictionary, reads input byte-by-byte, emits 3-byte (24-bit) codes, and can reverse the process for decompression. |
| `Node.cpp` | Binary tree node used for Huffman tree construction. Stores `entity` (symbol), `freq_val` (frequency), and left/right pointers. Supports comparison and merge operations. |
| `lzwh.cpp` | Command-line interface. Accepts `e` (encode) or `d` (decode) plus a file path. Writes encoded output to `out.lzwh` and decoded output to `out`. |

---

## How It Works

### LZW Encoding (`compressor.cpp`)

- **Dictionary**: Starts with all 256 single-byte entries (`0x00`–`0xFF`).
- **Sliding Window**: Scans the input, greedily matching the longest known sequence in the dictionary.
- **Output**: Emits a 24-bit (3-byte) code for every matched string.
- **Growth**: Adds new sequences (`matched + next_char`) to the dictionary as it goes.
- **Decoding**: Reverses the process using an inverse dictionary, reconstructing the original byte stream.

### Huffman Tree Node (`Node.cpp`)

- Defines the binary tree node used to build a Huffman code tree.
- Nodes are compared and merged by frequency, enabling construction of an optimal prefix code from a frequency distribution.
- This structure is intended for the second-stage entropy encoder.

---

## Usage

```bash
# Show help
./lzwh help

# Encode (compress) a file
./lzwh e input.txt
# Output: out.lzwh

# Decode (decompress) a file
./lzwh d out.lzwh
# Output: out
```

---

## Build

```bash
g++ -std=c++17 lzwh.cpp compressor.cpp Node.cpp -o lzwh
```

> **Note:** `lzwh.cpp` includes `functions.cpp`, which is currently not present in the repository. The project may require adding the missing Huffman-tree construction and code-generation utilities before it compiles and runs end-to-end.

---

## Current State

- [x] LZW encoder/decoder with 3-byte fixed-width codes
- [x] Huffman tree node structure
- [ ] Huffman tree construction and code generation (`functions.cpp` missing)
- [ ] Integration of Huffman stage after LZW output
- [ ] Variable-width code support for the LZW dictionary

---

## License

Provided for experimental and educational purposes.
