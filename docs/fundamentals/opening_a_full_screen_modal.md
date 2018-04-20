# 開啟全螢幕的 modal

要找到令人滿意的 modal 定義並不容易，讓我們試試底下這個：

?> modal 會顯示內容以暫時阻擋使用者與主畫面間的互動

聽起來挺正確的。modal 就像是一個 popup — 它不在導航的主要過程中，它經常有不同的轉場，不同的散場，並且被設計來讓你留意在某些特別的內容上。

我們會在 React Navigation (以下簡稱 RN) 的基礎章節裡解釋這些，目的並不只是因為它很常見，還因為實作會需要 *nesting navigators* 的知識，而 nesting navigators 是 RN 的重要觀念。

## 建立一個 modal stack

```javascript
class HomeScreen extends React.Component {
  static navigationOptions = ({ navigation }) => {
    const params = navigation.state.params || {};

    return {
      headerLeft: (
        <Button
          onPress={() => navigation.navigate('MyModal')}
          title="Info"
          color="#fff"
        />
      ),
      /* the rest of this config is unchanged */
    };
  };

  /* render function, etc */
}

class ModalScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontSize: 30 }}>This is a modal!</Text>
        <Button
          onPress={() => this.props.navigation.goBack()}
          title="Dismiss"
        />
      </View>
    );
  }
}

const MainStack = StackNavigator(
  {
    Home: {
      screen: HomeScreen,
    },
    Details: {
      screen: DetailsScreen,
    },
  },
  {
    /* Same configuration as before */
  }
);

const RootStack = StackNavigator(
  {
    Main: {
      screen: MainStack,
    },
    MyModal: {
      screen: ModalScreen,
    },
  },
  {
    mode: 'modal',
    headerMode: 'none',
  }
);
```



有幾個重點值得注意：

- 如我們所知道的，`StackNavigator` 函式會回傳 React 元件 (記得我們在 `App` 元件裡生成 `RootStack /`)。而這個元件也可以被當作一個畫面元件！要能做到這件事，我們是將一個 `StackNavigator` 嵌套到另一個 `StackNavigator` 裡。這樣做對我們很有幫助，因為我們想要讓 modal 有不同的轉場效果，也希望在整個 stack 裡暫時讓標頭失效。這對於分頁導航也非常有用，舉例來說，每個分頁都將擁有它自己的 stack！直覺來說，這是你所期望的，當你從分頁 A 移動到分頁 B 時，你會希望分頁 A 維持它現在既有的狀態。底下這個[示意圖](https://reactnavigation.org/docs/assets/modal/tree.png)或許可以幫助你更了解這個導航範例的結構。
- `StackNavigator` 的 `mode` 設定值可以是 `card`(預設) 或 `modal`。在 iOS 裡，`modal` 的行為是從畫面下方滑入，並且在使用者從上往下滑時退去。 若為 Android 平台， `modal` 設定值則沒有特別的影響，因為全螢幕的 modal 在 Android 裡並沒有被設計具有特別的轉場效果。
- 當我們呼叫 `navigate` 時，除了指定欲前往的路由外，我們並不用多指定什麼。我們不需要費心找出路由屬於的 stack (無論是叫做 'root' 還是 'main' stack) — RN 會嘗試找到最近的路由並且執行動作。我們可以參考[這張圖片](https://reactnavigation.org/docs/assets/modal/tree.png)來了解 `navigate` 動作是怎麼讓人從 `HomeScreen` 上游到 `MainStack`，而因為 `MainStack` 無法處理 `Mymodal` 路由，所以又再上游到 `RootStack`，而在那裡找到了處理路由的方法。



## 總結

- 為了改變 `StackNavigator` 的轉場效果，你可以使用 `mode` 設定值。當設為 `modal` 時，相對於從右至左，所有的畫面都會改為從底部滑上來。這會套用至所有的 `StackNavigator`，所以若是有其它的畫面要保留從右至左滑入，那麼你要增加另一個擁有預設 `moda` 值的 navigation stack。
- `this.props.navigation.navigate` 會在導航樹持續往上游，直到找到可以處理 `navigate` 動作的合適 navigator。