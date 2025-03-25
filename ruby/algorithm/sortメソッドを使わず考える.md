# バブルソート
https://paiza.jp/works/mondai/sort_naive/sort_naive__bubble

* まとめると以下の図のようになる
  1. 右の要素と入れ替えるため最後の要素を比較する必要はない
  2. 配列の要素の隣同士の比較の回数は「配列の要素数 - 1」となる
    * 最後の要素を比較する必要が無いため
  3. 比較の処理の回数も「配列の要素数 - 1」となる
    * 最後の要素から比較処理を始動する必要が無いため

![image](https://github.com/user-attachments/assets/c5c9687d-19d0-4996-bcd8-d84659ca7176)

# メソッドを使用しない場合のコード
## 典型的なコード
* 一旦 `temp` に代入して入れ替える値を保持する
* 二重ループで「全体の処理回数」と「配列の隣同士の比較処理の回数」を管理する

```ruby
(n-1).times do |j|
    (n-1).times do |k|
        if a_ary[k] > a_ary[k+1]
            temp = a_ary[k+1]
            a_ary[k+1] = a_ary[k]
            a_ary[k] = temp
        end
    end
end
```

## リファクタリング後コード
* 多重代入によって**同時に代入を行う**ため、一時的に値を保持するような手間が不要になる
  * https://docs.ruby-lang.org/ja/latest/doc/spec=2foperator.html#multiassign

```ruby
(n-1).times do |j|
    (n-1).times do |k|
        if a_ary[k] > a_ary[k+1]
            a_ary[k], a_ary[k+1] = a_ary[k+1], a_ary[k]
        end
    end
end
```

## 誤ったリファクタリング後コード
* 比較演算子である `<=>` を利用することでより効率的になるかと思ったがそうはならない

```ruby
(n-1).times do |j|
    (n-1).times do |k|
        if a_ary[k] <=> a_ary[k+1]
            a_ary[k], a_ary[k+1] = a_ary[k+1], a_ary[k]
        end
    end
end
```

* 原因は『`<=>` は boolean で値を返さず、 -1/0/1 の truthy/falsy なのかで値を返す』ため
  * これでは全部が true と判定されて入れ替え処理が発生してしまうのが問題

```ruby
p !!-1    #=> true
p !!0     #=> true
p !!1     #=> true
p !!false #=> false
```

# 多重配列の場合
* 参考問題
  * https://paiza.jp/works/mondai/c_rank_level_up_problems/c_rank_sort_boss

## `sort`は使用しない場合
* 多重配列の場合は『一つ目の値同士を比較 → 二つ目の値同士を比較』ではなく、『一つ目の値同士を比較 → 【二つ目の値同士を比較する条件を満たすか否か】 → 二つ目の値同士を比較』を忘れてはならない
  * → `# 【ここがPoint】`

```ruby
n = gets.to_i
result = Array.new(n)

n.times do |i|
    result[i] = gets.chomp.split(" ").map(&:to_i)
end

(n-1).times do |j|
    (n-1).times do |k|
        if result[k][1] < result[k+1][1]
            result[k], result[k+1] = result[k+1], result[k]
        elsif result[k][1] == result[k+1][1] # 【ここがPoint】
            if result[k][0] < result[k+1][0]
                result[k], result[k+1] = result[k+1], result[k]
            end
        end
    end
end

result.each do |h|
    puts h.join(" ")
end
```

## `sort_by`でソートの内容を指定する
* 

```ruby

```



### 関連リンク
* https://docs.ruby-lang.org/ja/latest/method/Array/i/sort.html
* https://paiza.jp/works/mondai/sort_naive/sort_naive__bubble
* https://docs.ruby-lang.org/ja/latest/doc/spec=2foperator.html#multiassign
* https://docs.ruby-lang.org/ja/latest/class/Comparable.html
