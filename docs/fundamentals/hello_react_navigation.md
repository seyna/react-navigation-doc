# 嗨，React Navigation

在網路瀏覽器裡，你可以用 `<a>` 標籤連結不同的頁面。當使用者點了連結， URL 就被存入瀏覽器的歷史紀錄裡。當使用者點擊了返回按鈕，瀏覽器取出歷史紀錄裡最新的項目，讓使用者回到先前拜訪的頁面。

React Native 沒有內建像瀏覽器這樣的全域歷史紀錄，而這也就是，React Navigation (以下簡稱 RN) 誕生的原因。

RN 的 `StackNavigator` 提供方法，讓你的應用程式可以在不同畫面間切換，並且管理導航的歷史。

如果你的應用程式只使用了一個 `StackNavigator`，那麼概念上來說它就會像是瀏覽器處理導航狀態一樣：當用戶互動時應用程式存入與取回導航紀錄裡的項目，並且使得用戶看見不同的畫面。

與瀏覽器不同的一個很大的差別在於，RN 的 `StackNavigator` 還提供了你在 Android 與 iOS 上會看到的手勢與動畫支援。

現在我們就開始用一個 `StackNavigator` 來學習 RN 吧。

## 建立一個 StackNavigator

`StackNavigation` 是一個會回傳 React compoment 的函式。它的參數包括一個 *route configuration object*，以及一個非必須的 *options object* (在此我們先暫時不提)。因為 `StackNavigator` 函式會回傳 React component，所以我們可以直接在 `App.js` 裡匯出它，成為 App 的根本元件。

```javascript
// In App.js in a new project

import React from 'react';
import { View, Text } from 'react-native';
import { StackNavigator } from 'react-navigation';

class HomeScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Home Screen</Text>
      </View>
    );
  }
}

export default StackNavigator({
  Home: {
    screen: HomeScreen,
  },
});
```

如果你執行以上的程式碼，你會看見畫面上僅有一個空白的導航列以及一個灰色的區域包含著 `HomeScreen` component。這些樣式都是 `StackNavigator` 的預設樣式，稍後我們會學習怎樣改變它們。

?> 路由名稱的大小寫並沒有關係，你可以使用小寫的 `home` 或大寫的 `Home`。一切取決於你。我們是比較喜歡使用大寫。

?> 路由設定裡唯一一個必填的參數是 `screen` component。你可以在 [StackNavigator reference](https://reactnavigation.org/docs/stack-navigator.html) 了解更多其他的選項。

在 React Native 裡，從 `App.js` 裡匯出的 component 是 app 的進入點 (或說是根本元件)：它是其它元件的上游。相較於僅是匯出 `StackNavigator`，對源頭的元件有更好的控制是比較實用的。所以，我們來匯出一個只是呈現 `StackNavigator` 的元件吧。

```javascript
const RootStack = StackNavigator({
  Home: {
    screen: HomeScreen,
  },
});

export default class App extends React.Component {
  render() {
    return <RootStack />;
  }
}
```



## 加入第二個路由

`<RootStack />` 元件並不接受任何 props：所有的設定參數皆是在 `options` 參數裡指明。我們剛剛讓 `options` 為空白，使用預設的設定值，現在我們加入第二個畫面到 `StackNavigator` 裡，以學習如何使用 `options`。

```javascript
// Other code for HomeScreen here...

class DetailsScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Details Screen</Text>
      </View>
    );
  }
}

const RootStack = StackNavigator(
  {
    Home: {
      screen: HomeScreen,
    },
    Details: {
      screen: DetailsScreen,
    },
  },
  {
    initialRouteName: 'Home',
  }
);

// Other code for App component here...
```

現在我們的 stack 有了兩個路由，`Home` 路由以及 `Details` 路由。`Home` 路由對應到 `HomeScreen` 元件，而 `Details` 路由對應到 `DetailsScreen` 元件。最初的路由會是 `Home` 路由。

到此你應該腦中會浮出一個問題，那就是：「那我要怎麼從 Home 移動到 Details 路由呢？」

這就是我們下一篇文章要提的事情。

## 總結

- React Native 並沒有提供像瀏覽器一樣的內建導航機制。而 React Navigation 提供了，並且還附上了 iOS 與 Android 系統裡的手勢與動畫效果。
- `StackNavigator` 是一個吃路由設定物件、選項物件，然後回傳 React component 的函式。
- 路由參數物件的 keys 是路由名稱， values 則是該路由對應的設定值，而設定值裡唯一必填的屬性是 `screen`。
- 若要指定最初的路由，在選項物件裡使用 `initialRouteName` 即可。