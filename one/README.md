## 文字コード
```
echo $%((0xDFFF-0xD800+1))
echo $((0xDFFF-0xD800+1))
echo $((2**16-2048+1024*1024))
printf '%X\n' $((2**16-2048+1024*1024))
printf A | iconv -f utf-8 -t utf-16be | xxd -p -u 
printf A | iconv -f utf-8 -t utf-16le | xxd -p -u
printf A | iconv -f utf-8 -t utf-16 | xxd -p -u 
```

### utf-8
- UNIX開発者のケントンプソンによって考案された符号化方式
- ASCII文字と交換性を持たせるために、ASCIIと同じ部分は１バイト、そのほかの部分を２〜４バイトで表現する
- バイト順に影響を受けないため本来BOMは必要がない

### utf-8の符号化方式
- 1バイトの先頭の連続するビット"1"（その後にビット"0"が1つ付く）の個数で、その文字のバイト数を表現する
- 2バイト目以降はビットパターン"10"で始める
- 残りのビットで符号位置を表現する
- １バイト目と2バイト目では値の範囲が重ならないので、文字境界を確実に判定できる
```
printf aあ1午前 | iconv -f utf-8 -t utf-16be | xxd -p -u | fold -4
> 0061
> 3042
> 0031
> 5348
> 524D
printf aあ1午前 | iconv -f utf-8 -t utf-16be | xxd -p -u | fold -4 | sed '1s/^/obase=2;ibase=16;/' | bc
```

- 2真数表現に変える
```
printf aあ1午前 | iconv -f utf-8 -t utf-16be | xxd -p -u | fold -4 | sed '1s/^/obase=2;ibase=16;/' | bc | xargs printf '%016d\n'
```

- 縦線を入れる
```
printf aあ1午前 | iconv -f utf-8 -t utf-16be | xxd -p -u | fold -4 | sed '1s/^/obase=2;ibase=16;/' | bc | xargs printf '%016d\n' | sed 's/\(....\)\(......\)\(......\)/\1|\2|\3/'
```

- 先頭には1110,２文字目以降には10を与える
  - utf-8の２真数表現
```
printf aあ1午前 | iconv -f utf-8 -t utf-16be | xxd -p -u | fold -4 | sed '1s/^/obase=2;ibase=16;/' | bc | xargs printf '%016d\n' | sed 's/\(....\)\(......\)\(......\)/1110\1|10\2|10\3/'
```

```
printf aあ1午前 | iconv -f utf-8 -t utf-16be | xxd -p -u | fold -4 | sed '1s/^/obase=2;ibase=16;/' | bc | xargs printf '%016d\n' | sed 's/\(....\)\(......\)\(......\)/1110\1|10\2|10\3/' | tr -d \| | { echo 'obase=16;ibase=2'; cat; } | bc | xxd -p -r; echo
```

### utf-8の冗長性の問題
- utf-8では1つの符号位置に対応するバイト列が複数表現する

### Unicodeの初期の設計原則
- 文字コードは全て16ビットで表現する
  - すでに破綻してる
- グリフまでは扱わない
- 感じのように異なる現股間で重複している文字は統一する
- 動的な合成を可能とする
- 既存の文字コードとの間で可逆変換できるようにする



