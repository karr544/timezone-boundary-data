# TZQ1 v2 — file format of `data/tzq1_d9_all.bin`

All integers are little-endian.

## Header (32 bytes)

| Offset | Size | Field |
|---|---|---|
| 0 | 4 | the magic `TZQ1` |
| 4 | 2 | the version: 2 |
| 6 | 1 | the maximum depth: 9 |
| 7 | 1 | the root cell size in degrees: 4 |
| 8 | 2 | the root rows: 45 |
| 10 | 2 | the root columns: 90 |
| 12 | 2 | the number of zones: 326 |
| 16 | 4 | the length of the zone names, in bytes |
| 20 | 4 | the root table offset |
| 24 | 4 | the chunk base |
| 28 | 4 | `26e\0`: the boundary release |

## Zone names

They start at byte 32: the IANA zone names in ASCII, joined by `\n`. A zone index refers to this list (0-based).

## Root table

The table holds `rows × cols` uint32 entries, row-major. The first row starts at latitude −90°, the first column at longitude −180°.

- **Bit 31 set:** the whole root cell is one zone. Bits 0–9 give the zone index, and bit 10 gives the ambiguous flag.
- **Otherwise:** the entry is the byte offset of the cell's chunk.

One more uint32 follows the table: the end offset of the last chunk.

A chunk ends where the next chunk listed in the table begins, or at that end offset.

## Chunk

Each chunk holds a uint32, the length of the uncompressed stream. The stream follows, compressed with raw DEFLATE (RFC 1951).

Bits are read most significant first, in preorder:

- **`1`, a split:** four children follow, in the order south-west, south-east, north-west, north-east.
- **`0`, a leaf:** then either
  - **`0`:** the same zone as the previous leaf in this chunk, not ambiguous; or
  - **`1`:** a 9-bit zone index, then a 1-bit ambiguous flag.

At depth *k* a cell's side is 4° / 2^k. Depth 9 gives 0.0078125°.

## Lookup

1. Find the root cell from `floor((lat + 90) / 4)` and `floor((lon + 180) / 4)`. Longitude 180 is read as −180.
2. If the root entry is uniform, it is the answer.
3. Otherwise inflate the chunk and walk the tree. At each split, choose the quadrant that holds the point: north if `lat ≥` the cell's middle latitude, east if `lon ≥` its middle longitude.
4. The leaf gives the zone name and the ambiguous flag.

**The ambiguous flag** marks leaves where land zones with different clocks meet. A user may need to confirm the zone there.
