# コマンドと

## 使用できるコマンド名を一覧表示
- bashの`compgen`を利用する
- zshで使えるようにするには、`~/.zshrc`に、
```
autoload -Uz bashcompinit
bashcompinit
```
を記述する

- 登録されてるコマンド確認
```
compgen -c
```

### 使用できるコマンドのうち、manが存在するもののみをランダムで取り出す。
- manコマンドで中身を勉強しようと思ったが、manが存在しないのが多かったので、それを除くためのコマンド
```
compgen -c | shuf | xargs -I@ zsh -c 'man @ &> /dev/null; echo $? @' | awk '$1==0{print $2}'
```
- これで生成されるものは man で確認できることが保証されているので、そこから勉強が捗る（！？）


## jsonを綺麗に表示
- jq
    - brew install jq
- 例
```
cat hoge.json | jq
```


## n列で表示
- xargs,
```
seq 100000 | xargs -n 5
> seq 100000  0.02s user 0.00s system 0% cpu 23.087 total
> xargs -n 5  7.37s user 17.16s system 87% cpu 28.080 total
```

- paste,
```
seq 1000000 | paste - - - - -
> seq 1000000  0.29s user 0.01s system 16% cpu 1.815 total
> paste - - - - -  0.71s user 0.17s system 48% cpu 1.829 total
```

- awk,
```
time seq 1000000 | awk '{if(NR%5==0){print $1}else{printf $1" "}}'
> seq 1000000  0.25s user 0.01s system 20% cpu 1.250 total
> awk '{if(NR%5==0){print $1}else{printf $1" "}}'  1.01s user 0.15s system 90% cpu 1.270 total
```

