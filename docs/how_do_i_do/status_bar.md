# 依據路由設定不同的狀態條

如果你沒有導航標頭，或者你的導航標頭會因為路由不同而改變，那麼這篇文章就是為你而寫的。

## stack 以及 drawer 導航

底下是一個使用 stack 或 drawer 的簡單示範。你只需要顯示 `StatusBar` 元件，設定相關的數值。

```javascript
class Screen1 extends React.Component {
  render() {
    return (
      <SafeAreaView style={[styles.container, { backgroundColor: '#6a51ae' }]}>
        <StatusBar
          barStyle="light-content"
          backgroundColor="#6a51ae"
        />
        <Text style={[styles.paragraph, { color: '#fff' }]}>
          Light Screen
        </Text>
        <Button
          title="Next screen"
          onPress={() => this.props.navigation.navigate('Screen2')}
          color={isAndroid ? "blue" : "#fff"}
        />
      </SafeAreaView>
    );
  }
}

class Screen2 extends React.Component {
  render() {
    return (
      <SafeAreaView style={[styles.container, { backgroundColor: '#ecf0f1' }]}>
        <StatusBar
          barStyle="dark-content"
          backgroundColor="#ecf0f1"
        />
        <Text style={styles.paragraph}>
          Dark Screen
        </Text>
        <Button
          title="Next screen"
          onPress={() => this.props.navigation.navigate('Screen1')}
        />
      </SafeAreaView>
    );
  }
}
```

```javascript
export default createStackNavigator({
  Screen1: {
    screen: Screen1,
  },
  Screen2: {
    screen: Screen2,
  },
}, {
  headerMode: 'none',
});
```

![](https://reactnavigation.org/docs/assets/statusbar/statusbar-stack-demo.gif)

```javascript
export default createDrawerNavigator({
  Screen1: {
    screen: Screen1,
  },
  Screen2: {
    screen: Screen2,
  },
});
```

![](https://reactnavigation.org/docs/assets/statusbar/statusbar-drawer-demo.gif)

## TabNavigator

如果你使用的是 TabNavigator，那麼情況會複雜一些，因為分頁裡的畫面都是一次生好的。

為了解決這個問題我們需要做兩件事：

1. 只在初始的畫面裡使用 `StatusBar`。這可以確保 `StatusBar` 的設定值不會因為重複設定而錯誤。
2. 在分頁使用中時，運用 React Navigation 裡的事件系統以及 `StatusBar` 的 API 以改變 `StatusBar` 的設定值。

首先，新的 `Screen2.js` 將不會再使用 `StatusBar` 元件。

```javascript
class Screen2 extends React.Component {
  render() {
    return (
      <SafeAreaView style={[styles.container, { backgroundColor: '#ecf0f1' }]}>
        <Text style={styles.paragraph}>
          Dark Screen
        </Text>
        <Button
          title="Next screen"
          onPress={() => this.props.navigation.navigate('Screen1')}
        />
        {/* <Button
          title="Toggle Drawer"
          onPress={() => this.props.navigation.navigate('DrawerToggle')}
        /> */}
      </SafeAreaView>
    );
  }
}
```

接著，在 `Screen1.js` 與 `Screen2.js` 裡我們都會設定監聽程序，好當分頁 `didFocus` 時改變 `StatusBar` 的設定值。此外，我們也會在 `TabNavigator` 被卸除時移除監聽程序。

```javascript
class Screen1 extends React.Component {
  componentDidMount() {
    this._navListener = this.props.navigation.addListener('didFocus', () => {
      StatusBar.setBarStyle('light-content');
      isAndroid && StatusBar.setBackgroundColor('#6a51ae');
    });
  }

  componentWillUnmount() {
    this._navListener.remove();
  }

  ...
}

class Screen2 extends React.Component {
  componentDidMount() {
    this._navListener = this.props.navigation.addListener('didFocus', () => {
      StatusBar.setBarStyle('dark-content');
      isAndroid && StatusBar.setBackgroundColor('#ecf0f1');
    });
  }

  componentWillUnmount() {
    this._navListener.remove();
  }

  ...
}
```

![](https://reactnavigation.org/docs/assets/statusbar/statusbar-tab-demo.gif)