<script setup lang="ts">
//いけるやつ
//(17,29)
//

// p(17以上の素数)を用意
const p = 17n;
// q(17以上の素数)を用意
const q = 29n;
// N(pq)を用意 公開鍵
const N = p * q;
// eを用意
const e = 3n;

// d(モジュラ逆数)を用意 秘密鍵
// e・d ≡ 1(mod (q-1)(p-1))になる
const l = (p - 1n) * (q - 1n);
console.log(l);

// ユークリッドの互除法を使って互いに素になるか確認
let gcd = (e: bigint, l: bigint): bigint => {
  return e === 0n ? l : gcd(l % e, e);
};
// eとlが互いに素であるかを確認
if (gcd(e, l) === 1n) {
  console.log("eとl((p-1)(q-1))は互いに素です。");
} else {
  console.log("eとl((p-1)(q-1))は互いに素ではありません。");
}

// 拡張ユークリッド互除法を使って
// ex + ly = 1
// になるxとyを見つける。
// xが「d」になる。
const extendedGCD = (a: bigint, b: bigint): [bigint, bigint, bigint] => {
  if (b === 0n) {
    return [a, 1n, 0n];
  } else {
    const [d, x, y] = extendedGCD(b, a % b);
    return [d, y, x - (a / b) * y];
  }
};

// ex + ly = 1 の整数解 (x, y) を求める
let [, d] = extendedGCD(e, l);

// dが負数の場合、正のモジュラ逆数に変換
if (d < 0n) {
  d += l;
}

console.log("モジュラ逆数 (d):", d);

// 暗号化する文章(M)を格納する配列を用意
let M: bigint[] = [];
for (let i = 0n; i < N; i++) {
  M.push(i);
}

// 暗号化(e乗して、Nで割った余り)する
// 暗号化された文章を格納する配列を用意
let encM: bigint[] = []; // encrypted・・暗号化された
for (let i = 0n; i < M.length; i++) {
  // e乗しNで割った余りを代入
  encM.push((M[i] ** e) % N);
}
// 復号(Mをd乗し、Nで割る)する
// 復号された文章を格納する配列を用意
let decM: bigint[] = []; // Decryption・・復号
for (let i = 0; i < encM.length; i++) {
  // e乗しNで割った余りを代入
  decM.push((encM[i] ** d) % N);
}

// Vue.jsのデータ
const tableData = M.map((item, index) => ({
  M: item.toString(),
  encM: encM[index].toString(),
  decM: decM[index].toString(),
}));
</script>



<template>
  <div>
    <p>p：{{ p }}</p>
    <p>q：{{ q }}</p>
    <p>e：{{ e }}</p>
    <p>d：{{ d }}</p>
    <p>(p-1)(q-1)：{{ l }}</p>
  </div>
  <div>
    <table border="1">
      <thead>
        <tr>
          <th>M</th>
          <th>暗号化された文章</th>
          <th>復号した文章</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, index) in tableData" :key="index">
          <td>{{ row.M }}</td>
          <td>{{ row.encM }}</td>
          <td>{{ row.decM }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>