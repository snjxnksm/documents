# atomのスタイルシート

Windows版の場合は、インストール直後は中華フォントになっているのでそのあたりを手当てします。  
メニューの「ファイル」→「スタイルシートを開く」で、styles.lessが開くので、以下を上書きしてみましょう。

~~~
/*
 * Your Stylesheet
 *
 * This stylesheet is loaded when Atom starts up and is reloaded automatically
 * when it is changed and saved.
 *
 * Add your own CSS or Less to fully customize Atom.
 * If you are unfamiliar with Less, you can read more about it here:
 * http://lesscss.org
 */


/*
 * Examples
 * (To see them, uncomment and save)
 */

// style the background color of the tree view
.tree-view {
  font-family: "Meiryo";
}

// style the background and foreground colors on the atom-text-editor-element itself
atom-text-editor {
  font-family: "ＭＳ ゴシック";
}

// To style other content in the text editor's shadow DOM, use the ::shadow expression
atom-text-editor::shadow .cursor {
  font-family: "ＭＳ ゴシック";
}

atom-workspace {
  font-family: "Meiryo";
}

.tooltip {
  font-family: "Meiryo";
}

* {
  font-family: "Meiryo";
}

.markdown-preview {
  h1, h2, h3, h4, h5, h6{
    font-family: "Meiryo";
  }
}
~~~
