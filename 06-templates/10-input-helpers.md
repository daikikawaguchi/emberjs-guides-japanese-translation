# INPUT HELPERS
# 入力ヘルパー

## Input Helpers
## 入力ヘルパー

The `{{input}}` and `{{textarea}}` helpers in Ember.js are the easiest way to
create common form controls. The `{{input}}` helper wraps the built-in
[Ember.TextField][1] and [Ember.Checkbox][2] views, while `{{textarea}}` wraps
[Ember.TextArea][3]. Using these helpers, you can create these views with
declarations almost identical to how you'd create a traditional `<input>` or
`<textarea>` element.

[1]: http://emberjs.com/api/classes/Ember.TextField.html
[2]: http://emberjs.com/api/classes/Ember.Checkbox.html
[3]: http://emberjs.com/api/classes/Ember.TextArea.html

Emberにおける`{{input}}`ヘルパーと`{{textarea}}`ヘルパーは一般的なフォームコントロールを作成する最も簡単な方法です。`{{input}}`ヘルパーはビルトインの[Ember.TextField][1]と[Ember.Checkbox][2]をラップしたものです。そして`{{textarea}}`ヘルパーは[Ember.TextArea][3]をラップしたものです。これらのヘルパーを使うことで、伝統的な`<input>`や`<textarea>`を作成するのとほとんど同じような宣言でこれらのビューを作成することができます。

### Text fields
### テキストフィールド

```handlebars
{{input value="http://www.facebook.com"}}
```

Will become:
上記のコードは、以下のようなHTMLになります。

```html
<input type="text" value="http://www.facebook.com"/>
```

You can pass the following standard `<input>` attributes within the input
helper:

入力ヘルパーには、以下の標準の`<input>`の属性値を渡すことができます。

<table>
  <tr><td>`readonly`</td><td>`required`</td><td>`autofocus`</td></tr>
  <tr><td>`value`</td><td>`placeholder`</td><td>`disabled`</td></tr>
  <tr><td>`size`</td><td>`tabindex`</td><td>`maxlength`</td></tr>
  <tr><td>`name`</td><td>`min`</td><td>`max`</td></tr>
  <tr><td>`pattern`</td><td>`accept`</td><td>`autocomplete`</td></tr>
  <tr><td>`autosave`</td><td>`formaction`</td><td>`formenctype`</td></tr>
  <tr><td>`formmethod`</td><td>`formnovalidate`</td><td>`formtarget`</td></tr>
  <tr><td>`height`</td><td>`inputmode`</td><td>`multiple`</td></tr>
  <tr><td>`step`</td><td>`width`</td><td>`form`</td></tr>
  <tr><td>`selectionDirection`</td><td>`spellcheck`</td><td>&nbsp;</td></tr>
</table>

If these attributes are set to a quoted string, their values will be set
directly on the element, as in the previous example. However, when left
unquoted, these values will be bound to a property on the template's current
rendering context. For example:

もしこれらの属性値が引用符（""や''）のついた文字列でセットされた場合、先ほどの例のように、それらの値はその要素に直接セットされます。しかし、引用符が付かない場合は、これらの値はテンプレートの現在のレンダリングコンテキストでのプロパティにバインドされます。例えば、

```handlebars
{{input type="text" value=firstName disabled=entryNotAllowed size="50"}}
```

Will bind the `disabled` attribute to the value of `entryNotAllowed` in the
current context.

上記の宣言は、`disabled`属性を現在のコンテキストの`entryNotAllowed`の値にバインドします。

### Actions

To dispatch an action on specific events, such as `enter` or `key-press`, use the following

`enter`や`key-press`のような、特定のイベントにおけるActionを送出するには、次のように使います。

```js
{{input value=firstName action="updateFirstName" on="key-press"}}
```

### Checkboxes
### チェックボックス

You can also use the `{{input}}` helper to create a checkbox by setting its
`type`:

`type`を"checkbox"にセットすることで、`{{input}}`ヘルパーでチェックボックスを作ることができます。

```handlebars
{{input type="checkbox" name="isAdmin" checked=isAdmin}}
```

Checkboxes support the following properties:

チェックボックスは以下のプロパティをサポートしています。

* `checked`
* `disabled`
* `tabindex`
* `indeterminate`
* `name`
* `autofocus`
* `form`


Which can be bound or set as described in the previous section.

前セクションで説明したように、これらのプロパティは値をバインドまたは直接セットすることができます。

### Text Areas
### テキストエリア

```handlebars
{{textarea value=name cols="80" rows="6"}}
```

Will bind the value of the text area to `name` on the current context.

上記の宣言は、テキストエリアの値を現在のコンテキストの`name`プロパティにバインドします。

`{{textarea}}` supports binding and/or setting the following properties:

`{{textarea}}`ヘルパーは以下のプロパティの値のバインドまたは直接セットに対応しています。

* `value`
* `name`
* `rows`
* `cols`
* `placeholder`
* `disabled`
* `maxlength`
* `tabindex`
* `selectionEnd`
* `selectionStart`
* `selectionDirection`
* `wrap`
* `readonly`
* `autofocus`
* `form`
* `spellcheck`
* `required`

### Extending Built-In Controls
### ビルトインコントロールの拡張

See the [Built-in Views][4] section of these guides to learn how to further
extend these views.

[4]: http://emberjs.com/guides/views/built-in-views

これらのビューをさらに拡張する方法について、このガイドの[Built-in Views][4]セクションを参照してください。

(The original document’s commit SHA1: 1aa41ae0559132149c58e5c515b603c08facd0a7)
