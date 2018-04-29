# 分頁導航

分頁導航或許是手機裡最常見的導航方式了。常見的分頁是在畫面的上方、下方，或者標頭的下面(或是根本取代了標頭)。

## 極簡版的分頁導航範例

```javascript
import React from 'react';
import { Text, View } from 'react-native';
import { TabNavigator } from 'react-navigation';

class HomeScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <Text>Home!</Text>
      </View>
    );
  }
}

class SettingsScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <Text>Settings!</Text>
      </View>
    );
  }
}

export default TabNavigator({
  Home: { screen: HomeScreen },
  Settings: { screen: SettingsScreen },
});
```

## 客製化模樣

方法跟客製化 `StackNavigator` 的方法很接近  — 有一些屬性可以在初始設定時便在 `TabNavigator` 裡指明，而其它一些則是可以在每個畫面的 `NavigationOptions` 裡再指定。

````javascript
// You can import Ionicons from @expo/vector-icons if you use Expo or
// react-native-vector-icons/Ionicons otherwise.
import Ionicons from 'react-native-vector-icons/Ionicons';
import { TabNavigator, TabBarBottom } from 'react-navigation';

export default TabNavigator(
  {
    Home: { screen: HomeScreen },
    Settings: { screen: SettingsScreen },
  },
  {
    navigationOptions: ({ navigation }) => ({
      tabBarIcon: ({ focused, tintColor }) => {
        const { routeName } = navigation.state;
        let iconName;
        if (routeName === 'Home') {
          iconName = `ios-information-circle${focused ? '' : '-outline'}`;
        } else if (routeName === 'Settings') {
          iconName = `ios-options${focused ? '' : '-outline'}`;
        }

        // You can return any component that you like here! We usually use an
        // icon component from react-native-vector-icons
        return <Ionicons name={iconName} size={25} color={tintColor} />;
      },
    }),
    tabBarOptions: {
      activeTintColor: 'tomato',
      inactiveTintColor: 'gray',
    },
    tabBarComponent: TabBarBottom,
    tabBarPosition: 'bottom',
    animationEnabled: false,
    swipeEnabled: false,
  }
);
````

讓我們來解構一下上述的程式碼：

- `tabBarIcon` 是 `navigationOptions` 的屬性，所以我們可以在畫面元件裡使用它，但在這個例子我們為了集中圖標的管理方便，所以將它放在 `TabNavigator` 裡。
- `tabBarIcon` 是一個接收 `focused` 狀態與 `tintColor` 的函式。如果你細究它的話會發現 `tabBarOptions`、`activeTintColor` 和 `inactiveTintColor`。這些數值預設會和 iOS 的預設值一樣，但你可以在這裡改變它們。
- 為了讓 iOS 與 Android 上的行為一致，我們特別提供了 `tabBarComponent`、`tabBarPosition`、`animationEnabled`和`swipeEnabled`。關於 `TabNavigator` 的預設行為，Android 是在是在畫面的上方顯示分頁條，iOS 則是在畫面底部，在此我們強迫雙方平台都將分頁條顯示在底部。
- 若欲瞭解更多 `TabNavigator` 的設定選項，請參考[完整的 API 文件](https://reactnavigation.org/docs/tab-navigator.html)。



## 在分頁中切換

使用的是同一個 API —  `this.props.navigation.navigate`

```javascript
import { Button, Text, View } from 'react-native';

class HomeScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <Text>Home!</Text>
        <Button
          title="Go to Settings"
          onPress={() => this.props.navigation.navigate('Settings')}
        />
      </View>
    );
  }
}

class SettingsScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <Text>Settings!</Text>
        <Button
          title="Go to Home"
          onPress={() => this.props.navigation.navigate('Home')}
        />
      </View>
    );
  }
}
```

## 每個分頁各自的 `StackNavigator`

通常分頁不會只顯示一個畫面，你可以想成每個分頁裡都有各自的 navigation stacks，而這正是我們要做的。

```javascript
import { TabNavigator, StackNavigator } from 'react-navigation';

class DetailsScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <Text>Details!</Text>
      </View>
    );
  }
}

class HomeScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        { /* other code from before here */ }
        <Button
          title="Go to Details"
          onPress={() => this.props.navigation.navigate('Details')}
        />
      </View>
    );
  }
}

class SettingsScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        { /* other code from before here */ }
        <Button
          title="Go to Details"
          onPress={() => this.props.navigation.navigate('Details')}
        />
      </View>
    );
  }
}

const HomeStack = StackNavigator({
  Home: { screen: HomeScreen },
  Details: { screen: DetailsScreen },
});

const SettingsStack = StackNavigator({
  Settings: { screen: SettingsScreen },
  Details: { screen: DetailsScreen },
});

export default TabNavigator(
  {
    Home: { screen: HomeStack },
    Settings: { screen: SettingsStack },
  },
  {
    /* Other configuration remains unchanged */
  }
);
```

## 為什麼我們要用 TabNavigator，而不是用 TabBarIOS 或其他元件？

你可能會很想使用獨立，沒有與 react navigation 整合的的分頁元件。在一些情況下，這是很 ok 的。然而我們需要提醒你，這麼做有可能在未來發生一些未預期的挫折與麻煩的。

舉例來說，React Navigation 的 `TabNavigator` 會處理 Android 的返回按鈕，然而一般獨立的元件並不會。此外，如果你需要執行像是"跳到這個分頁然後移動到這個畫面"的任務，呼叫兩個不同的 API 會變成很困難。

最後，手機上的使用者介面經常有些設計細節需要部份元件能夠察覺其他零件的存在與否 — 比如，如果你有一個半透明的分頁條，內容應該能在它下面捲動，而且畫面應該在底部保留空間以能完整看見分頁條的內容。雙擊分頁條應該讓使用中的 navigation stack 跳至頂部，而再做一次的話會移動到畫面最上方。

這些都不是一般獨立元件能夠做到的事，而 React Navigation 內建就為你準備好了。