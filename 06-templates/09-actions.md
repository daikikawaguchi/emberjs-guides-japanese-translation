# Action
# アクション

## The `{{action}}` Helper
## `{{action}}`ヘルパー

Your app will often need a way to let users interact with controls that
change application state. For example, imagine that you have a template
that shows a blog post, and supports expanding the post with additional
information.

あなたのアプリケーションは、アプリケーションの状態を変えるコントロールをユーザーが利用できるようにする方法を、しばしば必要とします。例えば、ブログの投稿を表示し、さらに追加の情報を表示するように投稿を展開することができるテンプレートをあなたが持っていると想像してください。

You can use the `{{action}}` helper to make an HTML element clickable.
When a user clicks the element, the named event will be sent to your
application.

HTML要素をクリック可能にするために`{{action}}`ヘルパーを使うことができます。ユーザーが要素をクリックしたとき、指定されたイベントがあなたのアプリケーションに送られます。

```handlebars
<!-- post.handlebars -->

<div class='intro'>
  {{intro}}
</div>

{{#if isExpanded}}
  <div class='body'>{{body}}</div>
  <button {{action 'contract'}}>Contract</button>
{{else}}
  <button {{action 'expand'}}>Show More...</button>
{{/if}}
```

```js
App.PostController = Ember.ObjectController.extend({
  // initial value
  isExpanded: false,

  actions: {
    expand: function() {
      this.set('isExpanded', true);
    },

    contract: function() {
      this.set('isExpanded', false);
    }
  }
});
```

Note that actions may be attached to any element of the DOM, but not all
respond to the `click` event. For example, if an action is attached to an `a`
link without an `href` attribute, or to a `div`, some browsers won't execute
the associated function. If it's really needed to define actions over such
elements, a CSS workaround exists to make them clickable, `cursor: pointer`.
For example:

ActionはどんなDOMの要素にもアタッチされる可能性がありますが、すべてが`click`イベントに反応するわけではありません。例えば、もし`href`属性を持たない`a`リンク、または`div`にActionがアタッチされていた場合、いくつかのブラウザーは関連する関数を実行しないかもしれません。もしそのような要素に対してActionを定義する必要が本当にあるなら、`cursor: pointer`という、これらをクリック可能にするためのCSSによる回避策があります。
例えば：

```css
[data-ember-action] {
  cursor: pointer;
}
```

### Action Bubbling
### アクションの伝播

By default, the `{{action}}` helper triggers a method on the template's
controller, as illustrated above.

標準では、上述したように、`{{action}}`ヘルパーはテンプレートのControllerのメソッドを呼び出します。

If the controller does not implement a method with the same name as the
action in its actions object, the action will be sent to the router, where
the currently active leaf route will be given a chance to handle the action.

もしControllerが、そのアクションオブジェクトのアクションと同じ名前のメソッドを実装していなければ、アクションはRouterに送られます。そこで、現在のアクティブな末端のRouteはそのアクションを扱う機会を与えられます。

Routes and controllers that handle actions **must place action handlers
inside an `actions` hash**. Even if a route has a method with the same name
as the actions, it will not be triggered unless it is inside an `actions` hash.
In the case of a controller, while there is deprecated support for triggering
a method directly on the controller, it is strongly recommended that you
put your action handling methods inside an `actions` hash for forward
compatibility.

アクションを扱うRouteとControllerは、`actions` **ハッシュの中にアクションハンドラーを設置する必要があります** 。たとえアクションと同じ名前のメソッドをRouteが持っていたとしても、それが`actions`ハッシュの中になければ、それは呼び出されません。
Controllerの場合、Controller上に直にあるメソッドを呼び出すことが非推奨ながらサポートされていますが、前方互換性のため、アクションハンドリングメソッドを`actions`ハッシュの中に配置することを強く勧めます。

```js
App.PostRoute = Ember.Route.extend({
  actions: {
    expand: function() {
      this.controller.set('isExpanded', true);
    },

    contract: function() {
      this.controller.set('isExpanded', false);
    }
  }
});
```

As you can see in this example, the action handlers are called such
that when executed, `this` is the route, not the `actions` hash.

この例をみて分かるとおり、アクションハンドラーがコールされた時、`this`は`actions`ハッシュでなく、Routeを指しています。

To continue bubbling the action, you must return true from the handler:

アクションの伝播を続けるために、ハンドラーからtrueを返す必要があります。

```js
App.PostRoute = Ember.Route.extend({
  actions: {
    expand: function() {
      this.controller.set('isExpanded', true);
    },

    contract: function() {
      // ...
      if (actionShouldAlsoBeTriggeredOnParentRoute) {
        return true;
      }
    }
  }
});
```

If neither the template's controller nor the currently active route
implements a handler, the action will continue to bubble to any parent
routes. Ultimately, if an `ApplicationRoute` is defined, it will have an
opportunity to handle the action.

テンプレートのControllerと現在のアクティブなRouteのどちらもハンドラーを実装していない場合、アクションは親のRouteに伝搬し続けます。最終的に、もし`ApplicationRoute`が定義されていれば、`ApplicationRoute`がそのアクションを扱います。

When an action is triggered, but no matching action handler is
implemented on the controller, the current route, or any of the
current route's ancestors, an error will be thrown.

アクションが呼び出されたとき、Controller、現在のRoute、または現在のRouteのどの祖先も対応するアクションハンドラーを実装していないのであれば、エラーが投げられます。

![Action Bubbling](http://emberjs.com/images/template-guide/action-bubbling.png)

This allows you to create a button that has different behavior based on
where you are in the application. For example, you might want to have a
button in a sidebar that does one thing if you are somewhere inside of
the `/posts` route, and another thing if you are inside of the `/about`
route.

このことは、あなたがアプリケーションのどこにいるかに基づいて、異なる振る舞いを持つボタンを作れることを意味します。例えばあなたは、サイドバーに、`/posts`ルートの中のどこかに居た場合にあることをして、`/about`ルートの中に居るときに他のことをするボタンを作ることができるのです。

### Action Parameters
### アクションパラメーター

You can optionally pass arguments to the action handler. Any values
passed to the `{{action}}` helper after the action name will be passed to
the handler as arguments.

アクションハンドラーにはオプションの引数を渡すことができます。 アクション名の後ろで、`{{action}}`ヘルパーに渡された値は、ハンドラーの引数に渡されます。

For example, if the `post` argument was passed:

たとえば、もし`post`引数が渡されたら、

```handlebars
<p><button {{action "select" post}}>✓</button> {{post.title}}</p>
```

The controller's `select` action handler would be called with a single argument
containing the post model:

Controllerの`select`アクションハンドラーはpost Modelを含んだ一つの引数をともなって呼び出されます。

```js
App.PostController = Ember.ObjectController.extend({
  actions: {
    select: function(post) {
      console.log(post.get('title'));
    }
  }
});
```

### Specifying the Type of Event
### イベントタイプの指定

By default, the `{{action}}` helper listens for click events and triggers
the action when the user clicks on the element.

標準では、`{{action}}`ヘルパーはクリックイベントを監視し、ユーザーが要素をクリックしたときにアクションを呼び出します。

You can specify an alternative event by using the `on` option.

`on`オプションを使うことで、他のイベントを指定することができます。

```handlebars
<p>
  <button {{action "select" post on="mouseUp"}}>✓</button>
  {{post.title}}
</p>
```

You should use the normalized event names [listed in the View guide][1].
In general, two-word event names (like `keypress`) become `keyPress`.

[1]: http://emberjs.com/guides/understanding-ember/the-view-layer/#toc_adding-new-events

あなたは[View guide][1]にリストされている正規化されたイベント名を使うべきです。一般的には、`keypress`のような２単語のイベント名は`keyPress`になります。
（訳者補足：View guideの表の左側が、Webブラウザーがサポートしている、JavaScriptで使われるイベント名。右側がEmber.jsのonオプションで指定する際に使うイベント名。）

### Specifying Whitelisted Modifier Keys
### ホワイトリスト式の修飾キーの指定

By default the `{{action}}` helper will ignore click events with
pressed modifier keys. You can supply an `allowedKeys` option
to specify which keys should not be ignored.

標準では、`{{action}}`ヘルパーは修飾キーを押した状態でのクリックイベントは無視します。あなたは、どのキーを無視しないようにするかを指定するために、`allowedKeys`オプションを使うことができます。

```handlebars
<script type="text/x-handlebars" data-template-name='a-template'>
  <div {{action 'anActionName' allowedKeys="alt"}}>
    click me
  </div>
</script>
```

This way the `{{action}}` will fire when clicking with the alt key
pressed down.

この方法の`{{action}}`は、Altキーが押された状態でクリックが行われたときに起動します。

### Stopping Event Propagation
### イベントの伝播の停止

By default, the `{{action}}` helper allows events it handles to bubble
up to parent DOM nodes. If you want to stop propagation, you can disable
propagation to the parent node.

標準では、`{{action}}`ヘルパーは、イベントが親のDOMノードに伝播していくのを許容します。もし伝播を止めたいなら、親ノードへの伝播を無効にすることができます。

For example, if you have a **✗** button inside of a link, you will want
to ensure that if the user clicks on the **✗**, that the link is not
clicked.

たとえば、リンクの中に **✗** ボタンがあったとして、ユーザーが **✗** をクリックしても、リンクはクリックされないことを確実にしたいとします。

```handlebars
{{#link-to 'post'}}
  Post
  <button {{action 'close' bubbles=false}}>✗</button>
{{/link-to}}
```

Without `bubbles=false`, if the user clicked on the button, Ember.js
will trigger the action, and then the browser will propagate the click
to the link.

`bubbles=false`がない場合、ユーザーがボタンをクリックすると、Ember.jsはアクションを呼び出し、そしてそのときブラウザーがクリックイベントをリンクに伝播させます。

With `bubbles=false`, Ember.js will stop the browser from propagating
the event.

`bubbles=false`があると、Ember.jsはブラウザーにイベントを伝播させることを止めさせます。

### Specifying a Target
### ターゲットを特定する

By default, the `{{action}}` helper will send the action to the view's
target, which is generally the view's controller. (Note: in the case of
an Ember.Component, the default target is the component itself.)

標準では、`{{action}}`ヘルパーはViewのターゲット（一般にはViewのController）にアクションを送ります。
（注意：Ember.Componentの場合は、デフォルトターゲットはComponent自身です。）

You can specify an alternative target by using the `target` option. This
is most commonly used to send actions to a view instead of a controller.

`target`オプションを使うことで、代わりのターゲットを指定することができます。これは通常、Controllerの代わりにViewにアクションを送るときによく使われます。

```handlebars
<p>
  <button {{action "select" post target=view}}>✓</button>
  {{post.title}}
</p>
```

You would handle this in an `actions` hash on your view.

あなたは送られたアクションを、ビューの`actions`ハッシュの中で扱うことになります。

```javascript
App.PostsIndexView = Ember.View.extend({
  actions: {
    select: function(post) {
      // do your business.
    }
  }
});
```

Note that actions sent to views in this way do not bubble up the 
currently rendered view hierarchy. If you want to handle the action in
a parent view, use the following:

この方法でViewに送られたActionは現在レンダリングされたView階層には伝播しません。もし親のViewでActionを扱いたい場合は、次のように使ってください。

```handlebars
<p>
  <button {{action "select" post target=view.parentView}}>✓</button>
  {{post.title}}
</p>
```

(The original document’s commit SHA1: c99213b2b80466c169d00f24d045da996b8ee19d)