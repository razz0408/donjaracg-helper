<script>
</script>

<template>
  <footer>
    <a href="#" id="page-top" class="btn-page-top"></a>

    <p class="about">
      「ドンジャラNEO アイドルマスター シンデレラガールズ」を<br class="sp-only">楽しむための非公式Webアプリです。<br>
      株式会社バンダイナムコエンターテインメント様、<br class="sp-only">株式会社バンダイ様、株式会社Cygames様などとは<br class="sp-only">一切関係ございません。
    </p>
    <p class="trademark">
      「アイドルマスター シンデレラガールズ」 は、<br class="sp-only">株式会社バンダイナムコエンターテインメント様の登録商標です。<br>
      「ドンジャラ」 および 「ドンジャラNEO」は、<br class="sp-only">株式会社バンダイ様の登録商標です。
    </p>
    <p class="thanks">
      ユニットデータ出典: <a href="https://twitter.com/nk_mirage/status/1636418358345334784">ねく (NK) 様</a>
    </p>
    <div class="link">
      <p>アイドルマスター シンデレラガールズ 関連リンク</p>
      <ul>
        <li><a href="https://idolmaster-official.jp/cinderellagirls" target="_blank">(アイドルマスター ポータル)<br class="sp-only"> アイドルマスター シンデレラガールズ</a></li>
        <li><a href="https://asobistore.jp/content/donjaracg/" target="_blank">(アソビストア) ドンジャラNEO(ネオ)<br class="sp-only"> アイドルマスター シンデレラガールズ</a></li>
        <li><a href="https://toy.bandai.co.jp/manuals/pdf.php?id=2672881" target="_blank">(BANDAI) ドンジャラNEO<br class="sp-only"> アイドルマスターシンデレラガールズ 公式説明書</a></li>
      </ul>
    </div>
    <p class="copyright">
      ©窪岡俊之 THE IDOLM@STER™& ©Bandai Namco Entertainment Inc.
    </p>
  </footer>
</template>

<style lang="scss">
// フッター
footer {
  margin-top: 15px;
  padding-top: 15px;
  padding-bottom: 5px;
  width: 100%;
  // padding-left: 5%;
  box-sizing: border-box;
  border-radius: 10px 10px 0px 0px;
  background-color: rgba(200, 200, 200, 0.7);

  p + p {
    margin-top: 10px;
  }
  .link {
    margin-top: 10px;

    p {
      font-weight: 600;
    }

    ul {
      margin-top: 10px;

      li {
        list-style-type: none;
      }
    }
    
    + .copyright {
      margin-top: 10px;
    }
  }

  p, li {
    font-size: 14px;
    color:rgba(0, 0, 0, 0.87);
  }
}

// TOPに戻るボタン
.btn-page-top {
  position: fixed;

  right: 15px;
  bottom: 15px;
  width: 60px;
  height: 60px;

  background: rgba(100, 100, 100, 0.7);
  cursor: pointer;
  transition: .3s;

  border-radius: 5px;

  &::before {
    content: "";
    position: absolute;
    top: 4px;
    right: 0;
    bottom: 0;
    left: 0;
    width: 18px;
    height: 18px;
    margin: auto;
    border-top: 6px solid #000;
    border-left: 6px solid #000;
    transform: rotate(45deg);
    box-sizing: border-box;
  }
}

// PC
@media screen and (min-width: 769px) {
}

// SP
@media screen and (max-width: 768px) {
  footer {
    p {
      font-size: 12px;
    }
  }
}
</style>