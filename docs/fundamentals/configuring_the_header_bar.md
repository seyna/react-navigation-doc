# 設定標頭列

你可能已經厭倦了畫面上方空白、灰底的標頭列 — 是時候加些風味上去了，讓我們開始設定標頭列吧。

## 設定標題

一個畫面元件擁有一個稱為 `navigationOptions` 的靜態屬性，它可以是一個物件，或者是一個函式(回傳包含著許多設定值的物件)。如底下所示，我們使用 `title` 設定標頭列的標題。

```javascript
class HomeScreen extends React.Component {
  static navigationOptions = {
    title: 'Home',
  };

  /* render function, etc */
}

class DetailsScreen extends React.Component {
  static navigationOptions = {
    title: 'Details',
  };

  /* render function, etc */
}
```

?> `StackNavigator` 使用平台上的慣例，所以在預設的狀態下，在 iOS 裝置裡頭標題會置中，而在 Android 裝置裡標題會是靠左對齊。

## 在標題裡使用參數

為了能夠在標題裡使用參數，我們需要將 `navigationOptions` 變成一個會回傳設定值物件的函式。你可能會想要在 `navigationOptions` 裡使用 `this.props`，但是因為這是一個元件內的靜態屬性，所以 `this` 並沒有指向任何一個元件的實體，無 props 可用。

取而代之的是，如果我們將 `navigationOptions` 變成一個函式，React Navigation 能用 `{ navigation, navigationOptions, screenProps }` 呼叫它 — 這種情況下，我們在意的是 `navigation`，它會跟傳入畫面裡的 props `this.props.navigation`相同。

回想一下我們先前透過 `navigation.state.params` 從 `navigation` 裡取出參數，現在我們一樣這麼做，從參數裡取出標題。

```javascript
class DetailsScreen extends React.Component {
  static navigationOptions = ({ navigation }) => {
    const { params } = navigation.state;
    
    return {
      title: params ? params.otherParam : 'A Nested Details Screen',
    }
  };

  /* render function, etc */
}
```

傳入 `navigationOptions` 函式的引數包含底下幾個屬性：

- `navigation` — 畫面裡的 [navigation prop](https://reactnavigation.org/docs/navigation-prop.html)，路由在 `navigation.state` 裡。
- `screenProps` — 從導航元件傳來的 props
- `navigationOptions` — 如果沒有提供新的數值，則預設或先前設定的數值將被使用

在上面的例子中，我們只需要 `navigation` prop，但在一些情況下你可能會用到 `screenProps` 或 `navigationOptions`。



## 用 setParams 更新 navigationOptions

我們經常會需要修改 `navigationOptions` 以在多個加載的畫面中變化使用中的畫面。我們可以透過 `this.props.navigation.setParams` 來辦到。

```javascript
 /* Inside of render() */
  <Button
    title="Update the title"
    onPress={() => this.props.navigation.setParams({otherParam: 'Updated!'})}
  />
```



## 調整標頭風格

有三個可能你客製化標題風格的關鍵屬性，它們是：`headerStyle`、`headerTintColor`、以及 `headerTitleStyle`。

- `headerStyle`：一個能套用在 `View` 的風格物件，它包覆著整個標頭。如果你想改變標頭的底色，就在它上面設定 `backgroundColor`。
- `headerTintColor`：這個屬性決定了你的返回按鈕以及標題的顏色。在底下的例子，我們設定了白色的顏色(`#fff`)。
- `headerTitleStyle`：我們可以用它決定標題的 `fontFamily`、`fontWeight`、以及其他 `Text` 風格屬性。

```javascript
class HomeScreen extends React.Component {
  static navigationOptions = {
    title: 'Home',
    headerStyle: {
      backgroundColor: '#f4511e',
    },
    headerTintColor: '#fff',
    headerTitleStyle: {
      fontWeight: 'bold',
    },
  };

  /* render function, etc */
}
```

有些值得提醒的事情：

1. 在 iOS 上狀態列的預設文字與圖標為黑色，然而若你的畫面背景為暗色時，閱讀起來會很吃力。這不是我們想深入的主題，然而你應該留意[如何調整狀態列的設定值](https://reactnavigation.org/docs/status-bar.html)以搭配你的畫面顏色。
2. 我們剛剛設定的數值只會套用到 home 畫面上，當我們移動到 details 畫面時，又會變為預設的風格。我們接下來就要學習怎樣在畫面之間分享 `navigationOptions`。



## 在畫面之間分享通用的 navigationOptions

如果我們必須複製 `navigationOptions` 到 app 裡的每個不同的畫面，那真是悲慘極了。

不過幸好我們不必這麼做。我們只需要將設定值搬移到 `StackNavigator` 裡。

```javascript
class HomeScreen extends React.Component {
  static navigationOptions = {
    title: 'Home',
    /* 不需要再寫標頭設定了！ */
  };

  /* render function, etc */
}

/* other code... */

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
    /* 從 HomeScreen 來的標頭設定如今在這邊 */
    navigationOptions: {
      headerStyle: {
        backgroundColor: '#f4511e',
      },
      headerTintColor: '#fff',
      headerTitleStyle: {
        fontWeight: 'bold',
      },
    },
  }
);
```

現在，所有屬於 `RootStack` 的畫面都會擁有這個客製化後的風格。是的，你可能已經在腦海提問了，就是一旦需要時，我們是否也能覆蓋改寫這些設定值呢？



## 覆蓋共享的 navigationOptions

你在畫面元件中指示的 `navigationOptions` 會併入上游的 `StackNavigator`，以當下畫面元件裡的設定值為優先。所以我們知道了這點後，就可以來練習一下，試著將 details 頁的文字顏色和背景顏色對調

```javascript
class DetailsScreen extends React.Component {
  static navigationOptions = ({ navigation, navigationOptions }) => {
    const { params } = navigation.state;

    return {
      title: params ? params.otherParam : 'A Nested Details Screen',
      /* These values are used instead of the shared configuration! */
      headerStyle: {
        backgroundColor: navigationOptions.headerTintColor,
      },
      headerTintColor: navigationOptions.headerStyle.backgroundColor,
    };
  };

  /* render function, etc */
}
```



## 用客製化的元件取代標題

有時候你會需要更大幅度地換掉標題以及標題的樣式，比如說，你可能需要在標題的位置換上一張圖片，或者將標題改為一個按鈕。

在這些情況下，你可以如下徹底地改寫標題的元件。

```javascript
class LogoTitle extends React.Component {
  render() {
    return (
      <Image
        source={require('./spiro.png')}
        style={{ width: 30, height: 30 }}
      />
    );
  }
}

class HomeScreen extends React.Component {
  static navigationOptions = {
    // headerTitle instead of title
    headerTitle: <LogoTitle />,
  };

  /* render function, etc */
}
```



?> 你可能會納悶，為什麼是使用 `headerTitle` 而非 `title`。原因是 `headerTitle` 是 `StackNavigator` 特有的屬性，而 `headerTitle` 預設是一個 `Text` 元件，功能是顯示 `title`。



## 更多的設定

你可以在 [StackNavigator reference](https://reactnavigation.org/docs/stack-navigator.html#navigationoptions-used-by-stacknavigator) 閱讀到更多關於在 `StackNavigator` 下設定`navigationOptions` 的說明。



## 總結

- 你可以透過 `navigationOptions` 客製化畫面元件裡的標頭。可以在這份 [API 參照](https://reactnavigation.org/docs/stack-navigator.html#navigationoptions-used-by-stacknavigator)裡讀到完整的選項清單。
- `navigationOptions` 靜態屬性可以是一個物件或者函式。當它是函式時，它的引數可以有 `navigation` prop、`screenProps`、以及 `navigationOptions`。
- 你可以在初始設定時利用 `StackNavigator` 指定通用的 `navigationOptions`。