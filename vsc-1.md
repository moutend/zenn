## はじめに

macOS版のVisual Studio CodeをVoiceOverスクリーンリーダーで操作できるか検証した結果をメモします。ある程度内容が増えたらZennのbookとして整理する予定です。

環境

- macOS Monterey 12.5.7
- Visual Studio Code  Version: 1.96.2

## インストール方法

Apple Silicon対応のバイナリがzipアーカイブで提供されている。FinderでOpenしてApplicationフォルダに移動するか、ターミナルでunzipしてmvするか、方法は何でもOK。

## アンインストール方法

挙動がおかしくなったと感じたら完全にアンインストールする。以下のスクリプトを実行する。

（現時点で確認した内容なので将来アプリケーションの構造が変化する恐れあり）

```bash
rm -rf "/Applications/Visual Studio Code.app"
rm -rf "$HOME/Library/Application Support/Code"
rm -rf "$HOME/Library/Caches/com.microsoft.VSCode"
rm -rf "$HOME/Library/Caches/com.microsoft.VSCode.ShipIt"
rm -rf "$HOME/Library/Preferences/com.microsoft.VSCode.plist"
rm -rf "$HOME/Library/Saved Application State/com.microsoft.VSCode.savedState"
```

## 起動直後の画面

アクセシビリティに関連した、知っておくと役に立つ情報が表示されます。情報はリスト形式で表示されていて、11個の項目があります。

初期状態はすべての項目が閉じられています。`VO + 左右カーソルキー`で各項目に移動し、`VO + Space`を実行すると項目が開閉します。

**注意点**

- リストの項目は13個あると読み上げされますが、実際は11個です。12個目の項目は「Mark Done button」13個目の項目は「Next Section  button」です。
- 一度項目を開くと、その項目名の最初に「Inspect this in the accessible view (Option+F2).」という文章が追加されます。

例

- 開閉前: Use the accessibility help dialog to learn about features collapsed button, group 1 of 13
- 開閉後: Inspect this in the accessible view (Option+F2). Use the accessibility help dialog to learn about features collapsed button, group 1 of 13

VS Codeを閉じるか、Go backボタンで戻った後にもう一度選択すると読み上げは元に戻ります。

## 各項目の内容について

参考までに、テキストとして残します。
見出しのレベルはVS Codeに表示されているレベルと一致させています。

## Get Started with Accessibility Features

Learn the tools and shortcuts that make VS Code accessible. Note that some actions are not actionable from within the context of the walkthrough.

### Use the accessibility help dialog to learn about features

The accessibility help dialog provides information about what to expect from a feature and the commands/keybindings to operate them.
With focus in an editor, terminal, notebook, chat response, comment, or debug console, the relevant dialog can be opened with the Open Accessibility Help command.
Open Accessibility Help button
Tip: Use keyboard shortcut Option+F1 

### Screen reader users can inspect content line by line, character by character in the accessible view.

The accessible view is available for the terminal, hovers, notifications, comments, notebook output, chat responses, inline completions, and debug console output.
With focus in any of those features, it can be opened with the Open Accessible View command.
Open Accessible View button
Tip: Use keyboard shortcut Option+F2

### Control the verbosity of aria labels

Screen reader verbosity settings exist for features around the workbench so that once a user is familiar with a feature, they can avoid hearing hints about how to operate it. For example, features for which an accessibility help dialog exists will indicate how to open the dialog until the verbosity setting for that feature has been disabled.
These and other accessibility settings can be configured by running the Open Accessibility Settings command.
Open Accessibility Settings button

### Unlock productivity with the Command Palette

Run commands without reaching for your mouse to accomplish any task in VS Code.
Open Command Palette button
Tip: Use keyboard shortcut Shift+Command+P
### Customize your keyboard shortcuts

Once you have discovered your favorite commands, create custom keyboard shortcuts for instant access.
Keyboard Shortcuts button
Tip: Use keyboard shortcut Command+K Command+S

### Fine tune which accessibility signals you want to receive via audio or a braille device

Accessibility sounds and announcements are played around the workbench for different events.
These can be discovered and configured using the List Signal Sounds and List Signal Announcements commands.
List Signal Sounds button
List Signal Announcements button

### Access the hover in the editor to get more information on a variable or symbol

While focus is in the editor on a variable or symbol, a hover can be can be focused with the Show or Open Hover command.
Show or Focus Hover button
Tip: Use keyboard shortcut Command+K Command+I

### Navigate to symbols in a file

The Go to Symbol command is useful for navigating between important landmarks in a document.
Go to Symbol button

### Use code folding to collapse blocks of code and focus on the code you're interested in.

Fold or unfold a code section with the Toggle Fold command.
Toggle Fold button
Tip: Use keyboard shortcut Command+K Command+L
Fold or unfold recursively with the Toggle Fold Recursively Command
Toggle Fold Recursively button
Tip: Use keyboard shortcut Command+K Shift+Command+L

### Use Intellisense to improve coding efficiency

Intellisense suggestions can be opened with the Trigger Intellisense command.
Trigger Intellisense button
Tip: Use keyboard shortcut Control+Space
Inline intellisense suggestions can be triggered with Trigger Inline Suggestion
Trigger Inline Suggestion button
Useful settings include editor.inlineCompletionsAccessibilityVerbose and editor.screenReaderAnnounceInlineSuggestion.

### Configure accessibility settings

Accessibility settings can be configured by running the Open Accessibility Settings command.
Open Accessibility Settings button

## Get Started with VS Code

Customize your editor, learn the basics, and start coding

### Use AI features with Copilot for free

You can use Copilot  to generate code across multiple files, fix errors, ask questions about your code and much more using natural language.
Set Up Copilot for Free button

### Choose your theme

The right theme helps you focus on your code, is easy on your eyes, and is simply more fun to use.
Browse Color Themes button
Tip: Use keyboard shortcut Command+K Command+T

### Rich support for all your languages

Code smarter with syntax highlighting, code completion, linting and debugging. While many languages are built-in, many more can be added as extensions.
Browse Language Extensions button

### Tune your settings

Customize every aspect of VS Code and your extensions to your liking.
Back up and sync button
 your essential customizations across all your devices.
Open Settings button
Tip: Use keyboard shortcut Command+,

### Unlock productivity with the Command Palette

Run commands without reaching for your mouse to accomplish any task in VS Code.
Open Command Palette button
Tip: Use keyboard shortcut Shift+Command+P

### Watch video tutorials

Watch the first in a series of short & practical video tutorials for VS Code's key features.
Watch Tutorial button

 Mark Done button, group 7 of 8
Next Section  button, group 8 of 8

### Code with extensions

Extensions are VS Code's power-ups. They range from handy productivity hacks, expanding out-of-the-box features, to adding completely new capabilities.
Browse Popular Extensions button

### Built-in terminal

Quickly run shell commands and monitor build output, right next to your code.
Open Terminal button
Tip: Use keyboard shortcut Control+`

### Track your code with Git

Set up the built-in version control for your project to track your changes and collaborate with others.
Clone Repository button

### Customize your shortcuts

Once you have discovered your favorite commands, create custom keyboard shortcuts for instant access.
Keyboard Shortcuts button
Tip: Use keyboard shortcut Command+K Command+S

## 初期画面でショートカットを実行した際の挙動

キーの内容と読み上げられるコンテンツを以下にまとめる。

### Explorer (Shift+Command+E)

You have not yet opened a folder. Open Folder Opening a folder will close all currently open editors. To keep them open, add a folder instead. You can clone a repository locally. Clone Repository To learn more about how to use Git and source control in VS Code read our docs .

### Search (Shift+Command+F)

Search: Type Search Term and press Enter to search

### Source Control (Ctrl+Shift+G)

In order to use Git features, you can open a folder containing a Git repository or clone from a URL. Open Folder Clone Repository To learn more about how to use Git and source control in VS Code read our docs .

### Run and Debug (Shift+Command+D)

Open a file which can be debugged or run. Run and Debug To customize Run and Debug, open a folder and create a launch.json file.

### Extensions (Shift+Command+X)

No extensions found.


