# 客製化 Android 返回按鈕的行為

預設情況下，當使用者按下 Android 的硬體返回按鈕後，react-navigation 會回到上一個頁面或者當沒有上一頁時離開 app。這個做法很合理，然而在某些情境下或許你想要來些特別的處置。

如果你想要簡單易用的辦法，你或許可以參考由社群所開發的 [react-navigation-backhandler](https://github.com/vonovak/react-navigation-backhandler)。底下的文字段落便是告訴你套件背後實際運作的原理。

舉個例子好了，我們來考慮一個情境，一名使用者正在選擇清單裡的許多物品，該名使用者正處於「選擇模式」。當按下返回按鈕時，我們首先想解除「選擇模式」，然後要在使用者再次按下返回按鈕時，才真的返回上一頁。

底下的程式碼示範了這個狀況。

我們使用了 react-native 原生的 `BackHandler` ，[訂閱 navigation 生命週期的更新]()，以及添加我們客製化的 `hardwareBackPress` 監聽程序。

解釋底下的程式碼：將 `onBackButtonPressAndroid` 的回傳設為 `true` 時代表我們正在處理這個事件，react-navigation 的監聽程序就不會被呼叫，畫面便不會跳開。而當回傳 `false` 時會使讓事件傳遞，因此讓 react-navigation 的監聽程序跳開畫面。

```javascript
class ScreenWithCustomBackBehavior extends React.Component {
  componentDidMount() {
    BackHandler.addEventListener('hardwareBackPress', this.onBackButtonPressAndroid);
  }

  componentWillUnmount() {
    BackHandler.removeEventListener('hardwareBackPress', this.onBackButtonPressAndroid);
  }

  onBackButtonPressAndroid = () => {
    if (this.isSelectionModeEnabled()) {
      this.disableSelectionMode();
      return true;
    } else {
      return false;
    }
  };
}
```

上述的做法在 `StackNavigator` 的畫面中都會正常運作。然而其他的情況下，比如當 drawer 打開時按下返回按鈕時的客製化動作，現階段還無法做到 (歡迎你提交 PR！)。

## 為什麼不使用元件的生命週期方法？

首先，你可以能會想要使用 `componentDidMount` 訂閱返回按鈕的按壓事件，使用 `componentWillUnmount` 以取消訂閱。然而我們不使用它們的原因是，它們通常不會在進入或離開畫面時被呼叫。

更具體一點來說，考慮一個擁有畫面 `A` 與 `B` 的 `StackNavigator`。當移動到 `A` 後，它的 `componentDidMount` 呼叫了，而當移動到 `B` ，它的 `componentDidCount` 也被呼叫了，但是 `A` 仍然處於裝載中，所以它的 `componentWillUnmount` 不會被呼叫。

同樣的，當我們從 `B` 返回到 `A`，`B` 的 `componentWillUnmount` 被呼叫了，但是 `A` 的 `componentDidMount` 卻沒有，因為 `A` 仍然、一直處於裝載狀態。