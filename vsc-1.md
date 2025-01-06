# 初期設定画面

アクセシビリティに関連した、知っておくと役に立つ情報が表示されます。情報はリスト形式で表示されていて、11個の項目があります。

初期状態はすべての項目が閉じられています。`VO + 左右カーソルキー`で各項目に移動し、`VO + Space`を実行すると項目が開閉します。

**注意点**

- リストの項目は13個あると読み上げされますが、実際は11個です。12個目の項目は「Mark Done button」13個目の項目は「Next Section  button」です。
- 一度項目を開くと、その項目名の最初に「Inspect this in the accessible view (Option+F2).」という文章が追加されます。

例

- 開閉前: Use the accessibility help dialog to learn about features collapsed button, group 1 of 13
- 開閉後: Inspect this in the accessible view (Option+F2). Use the accessibility help dialog to learn about features collapsed button, group 1 of 13

今のところ、この不具合を解消する方法を見つけられていません。不便ですが、最後まで読み上げの内容を聞くしかなさそうです。

## 各項目の内容について

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
