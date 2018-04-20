# 標頭按鈕

我們知道了怎麼去改變標頭的模樣，現在就讓我們學習如何讓標頭以指定的方式回應觸碰吧。

## 在標頭加入按鈕

與標頭最常見的互動方式是按下往左或往右的按鈕，我們來在標頭的右側放上一個按鈕吧。

```javascript
class HomeScreen extends React.Component {
  static navigationOptions = {
    headerTitle: <LogoTitle />,
    headerRight: (
      <Button
        onPress={() => alert('This is a button!')}
        title="Info"
        color="#fff"
      />
    ),
  };
}
```

`navigationOptions` 裡綁定的 `this` 並**不是**  `HomeScreen` 實體，所以你不能對它呼叫 `setState`。這很關鍵，因為我們經常會需要標頭裡的按鈕能夠和畫面互動。接著讓我們來看看該如何做到。

對此 React native 社群裡有提供 [react-navigation-header-buttons](https://github.com/vonovak/react-navigation-header-buttons) 套件供使用。

## 標頭與畫面元件的互動

一個常見的模式是透過使用 `params` 以讓標頭的按鈕能夠取得元件實體裡的函式。我們來用底下這個經典的記數器為例：

```javascript
class HomeScreen extends React.Component {
  static navigationOptions = ({ navigation }) => {
    const params = navigation.state.params || {};

    return {
      headerTitle: <LogoTitle />,
      headerRight: (
        <Button onPress={params.increaseCount} title="+1" color="#fff" />
      ),
    };
  };

  componentWillMount() {
    this.props.navigation.setParams({ increaseCount: this._increaseCount });
  }

  state = {
    count: 0,
  };

  _increaseCount = () => {
    this.setState({ count: this.state.count + 1 });
  };

  /* later in the render function we display the count */
}
```

?> React Navigation 並不保證畫面元件會在標頭之前顯示生成，然後因為 `increaseCount` 參數是在 `componentWillMount` 階段載入，所以我們可能在 `navigationOptions` 取不到，這也是為什麼我們會用 `|| {}` 的方式來取得參數 (有可能什麼都沒有)。我們知道這是個很奇怪的 API，我們已經計畫要改善它了！

?> 一個替代 `setParams` 的解決方法是，使用狀態的管理函式庫，比如 Redux 或 MobX，來讓標頭與畫面之間能夠互相溝通。



## 客製化返回按鈕

`StackNavigator` 針對不同的作業系統提供了不同預設的返回按鈕。在 iOS 裡頭按鈕旁，若空間許可時會顯示前一個畫面的標題標籤，而若不足時就只顯示"返回"。

你可以透過 `headerBackTitle` 和 `headerTruncateBackTitle` 改變標籤的行為 ([更多](https://reactnavigation.org/docs/stack-navigator.html#headerbacktitle))。

若是要改變返回按鈕的圖片，你可以使用 [headerBackImage](https://reactnavigation.org/docs/stack-navigator.html#headerbackimage)。



## 改寫返回按鈕

當有前一個畫面時，`StackNavigator` 會自動呈現返回按鈕。

一般來說，這是我們預期的表現。然而有些時候你會想要擁有更多的控制權，調整這個行為，在這個情況下你可以指定 `headerLeft`，就像是我們先前指定 `headerRight` 一樣，徹底地改寫返回按鈕。



## 總結

- 你可以在 `navigationOptions` 裡透過 `headerLeft` 與 `headerRight` 屬性設定標頭裡的按鈕。
- 運用 `headerLeft` 你可以徹底改寫返回按鈕，但如果你想要改變的是標題文字或圖片，那麼你要運用的是 `navigationOptions` 裡的 `headerBackTitle`、`headerTruncateBackTitle`、以及 `headerBackImage`。