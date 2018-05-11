# iPhone X 支援

預設情況下 React Navigation 會協助你的應用程式在 iPhoneX 裡正常地顯示。做法是透過 UI 元素裡的 `SafeAreaView`，它會跟「缺口」("the notch") 進行互動。

## 隱藏或客製化導航條或分頁條

![](https://reactnavigation.org/docs/assets/iphoneX/01-iphonex-default.png)

然而，如果你有覆寫預設的導航條，那就要很小心確保你的 UI 沒有跟當中的硬體元素相互干擾。

舉例來說，如果我在 `header` 或 `tabBarComponet` 裡沒有寫任何東西，顯示會是空白。

```javascript
const Tabs = createBottomTabNavigator({
  ...
}, {
  tabBarComponent: () => null,
});

export default createStackNavigator({
  ...
}, {
  headerMode: 'none',
});
```



![](https://reactnavigation.org/docs/assets/iphoneX/02-iphonex-content-hidden.png)

要解決這個問題，你可以先從 `react-navigation` 匯入 `SafeAreaView`，然後在 `SafeAreaView` 裡擺入內容，

```javascript
import { SafeAreaView } from 'react-navigation';

class App extends Component {
  render() {
    return (
      <SafeAreaView style={styles.container}>
        <Text style={styles.paragraph}>
          This is top text.
        </Text>
        <Text style={styles.paragraph}>
          This is bottom text.
        </Text>
      </SafeAreaView>
    );
  }
}
```

![](https://reactnavigation.org/docs/assets/iphoneX/03-iphonex-content-fixed.png)

它會自動偵測運行環境是否為 iPhoneX，如果是，即會確保內容不會被任何硬體的原素所遮蓋。

## 景觀模式

儘管你是使用預設的導航條和分頁條，而且運用程式也可以順利運行於景觀模式，但你仍需要處理內容被感應器叢遮蔽的問題。

![](https://reactnavigation.org/docs/assets/iphoneX/04-iphonex-landscape-hidden.png)

要解決這個問題，一樣我們需要用到 `SafeAreaView`

![](https://reactnavigation.org/docs/assets/iphoneX/05-iphonex-landscape-fixed.png)



## 運用 `forceInset` 以取得更多控制

在某些情況下你可能會想要有更多控制的能力，例如增減 padding。底下的例子中，我們透過傳送 `forceInset` prop 至 `SafeAreaView` 以移除了底部的 padding。

```javascript
<SafeAreaView style={styles.container} forceInset={{ bottom: 'never' }}>
  <Text style={styles.paragraph}>
    This is top text.
  </Text>
  <Text style={styles.paragraph}>
    This is bottom text.
  </Text>
</SafeAreaView>
```

`forceInset` 參數物件的 key 可以是 `top | bottom | left | right | vertical | horizontal`，而值可為 `'always' | 'never'`。或者你也可以透過傳遞數值覆寫 padding。