# 在畫面間移動

在前一篇文章["嗨，React Navigation"](/fundamentals/hello_react_navigation)裡，我們定義了一個擁有兩個路由 (`Home`與`Details`) 的 `StackNavigator`，但是我們還沒有學到如何讓使用者能從 `Home` 移動到 `Details`。

如果是網路瀏覽器，我們可以這樣做：

```html
<a href="details.html">Go to Details</a>
```

也可以這樣寫：

```html
<a onClick={() => { document.location.href = "details.html"; }}>Go to Details</a>
```

我們將做跟後者類似的事情，但不是使用 `document` 全域變數，而是使用從畫面元件裡得到的`navigation` prop。



## 移動到新畫面

```javascript
import React from 'react';
import { Button, View, Text } from 'react-native';
import { StackNavigator } from 'react-navigation';

class HomeScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Home Screen</Text>
        <Button
          title="Go to Details"
          onPress={() => this.props.navigation.navigate('Details')}
        />
      </View>
    );
  }
}

// ... other code from the previous section
```

讓我們來拆解一下喔：

- `this.props.navigation`：`navigation` prop 會傳到每一個在 `StackNavigator` 裡的 **screen component** (定義)
- `navigate('Details')`：在 `navigation` prop 上，呼叫 `navigate` 函數(命名真難啊！)，並且帶入使用者將前往的路由名稱。

?> 如果我們用一個沒有在 `StackNavigator`定義的路由名稱呼叫 `this.props.navigation.navigate`，那麼什麼事也不會發生。換句話說，我們只能移動到有被 `StackNavigator` 定義的路由 ─ 我們不能隨意移動到任何元件。

好的，所以我們現在有了兩個路由：1) Home 路由，2) Details 路由。如果我們從Details 再次移動到 Details 畫面會怎樣呢？



## 重複移動到同一個路由

```javascript
class DetailsScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Details Screen</Text>
        <Button
          title="Go to Details... again"
          onPress={() => this.props.navigation.navigate('Details')}
        />
      </View>
    );
  }
}
```

如果你執行這一段程式碼，你會發現每當你按下 "Go to Details...again" 按鈕，就會多了一個新畫面。這跟最初我們類比的 `document.location.href` 就有很大差別了，原因是網路瀏覽器會將它們視為同一個路由，所以不會加到瀏覽紀錄裡；而`StackNavigator` 的 `navigate` 表現得更像是網路的 `window.history.pushState`：每呼叫一次 `navigate`，就會多一筆路由到導航紀錄裡。



## 返回

當使用者可以從現在的畫面回去前一頁時，`StackNavigator` 提供的 header 會自動顯示一個返回按鈕(但若導航紀錄裡只有一個畫面紀錄時，由於沒有前一頁，自然就不會有返回按鈕)。

有時候你會想要透過程式觸發返回的動作，那麼你可以使用 `this.props.navigation.goBack()`

```javascript
class DetailsScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Details Screen</Text>
        <Button
          title="Go to Details... again"
          onPress={() => this.props.navigation.navigate('Details')}
        />
        <Button
          title="Go back"
          onPress={() => this.props.navigation.goBack()}
        />
      </View>
    );
  }
}
```

?> 在 Android 裡，React Navigation 與硬體的返回按鈕互相綁定，所以當使用者按鈕實體返回鈕時就會觸發 `goBack()` 函式，正如你所預期的一樣。

另一個常見需求是想要返回好幾個畫面之前：舉例來說，如果你已經深入了好幾個畫面然後想要擺脫它們重新回到第一個畫面，我們會在 ["在流程裡建立記號"](../how_do_i_do/authentication_flows) 文章裡討論它。



## 總結

- `this.props.navigation.navigate('RouteName')` 將一個新的路由推進 `StackNavigator`。我們可以一直呼叫它，而它也會一直推入路由。
- header 會自動顯示返回按鈕，而你也可以用 `this.props.navigation.goBack()` 的程式碼觸發返回。在 Android 裡，結果就和實際按壓硬體的返回按鈕一樣。
- 每個畫面元件(那些有在路由設定值裡被定義的畫面元件)都能接收到 `navigation` prop 

