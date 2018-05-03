# 驗證流程

大部分的 app 都需要讓使用者能夠驗證登入，以存取與自己相關以及隱私的資料。一般的流程是這樣：

- 使用者開啟 app
- app 從裝置的儲存空間中讀出驗證的狀態 (例如，`AsyncStorage`)
- 當進入流程時，取決於讀取出的驗證狀態是有效或無效，使用者要不就是看到驗證畫面，要不就是主畫面。
- 當使用者登出後，會將驗證狀態清空，並且重新返回驗證畫面。



## 設定我們的 navigators

```javascript
import { StackNavigator, SwitchNavigator } from 'react-navigation';

// Implementation of HomeScreen, OtherScreen, SignInScreen, AuthLoadingScreen
// goes here.

const AppStack = StackNavigator({ Home: HomeScreen, Other: OtherScreen });
const AuthStack = StackNavigator({ SignIn: SignInScreen });

export default SwitchNavigator(
  {
    AuthLoading: AuthLoadingScreen,
    App: AppStack,
    Auth: AuthStack,
  },
  {
    initialRouteName: 'AuthLoading',
  }
);
```



你可能不認識 `SwitchNavigator`。`SwitchNavigator` 的目的僅是只顯示這個畫面一次。預設狀態下，它不會處置返回按鈕的動作，並且會將路由重設回預設的狀態。

而這就是我們驗證流程想要的行為。當使用者登入後，我們想要丟掉卸下所有驗證流程裡的畫面，而因為已經登入成功了，所以當按下返回按鈕後，我們不希望再次回到驗證畫面。

我們可以用 `navigate` 在 `SwitchNavigator` 裡切換路由。你可以在 [API 參照文件](https://reactnavigation.org/docs/switch-navigator)裡讀到更多關於 `SwitchNavigator` 的事情。

我們將 `initialRouteName` 設為 `AuthLoading` 是因為我們將要在那裏從裝置的儲存空間裡取出驗證狀態。

## 實作驗證讀取中畫面

```javascript
import React from 'react';
import {
  ActivityIndicator,
  AsyncStorage,
  StatusBar,
  StyleSheet,
  View,
} from 'react-native';

class AuthLoadingScreen extends React.Component {
  constructor(props) {
    super(props);
    this._bootstrapAsync();
  }

  // 從儲存空間裡取出 token，然後導航到適當的地方
  _bootstrapAsync = async () => {
    const userToken = await AsyncStorage.getItem('userToken');
    
    // 將會切換到 App 或 Auth 畫面，並且這個畫面將會被卸下丟掉
    this.props.navigation.navigate(userToken ? 'App' : 'Auth');
  };


  // 顯示任何你喜歡的讀取中畫面
  render() {
    return (
      <View style={styles.container}>
        <ActivityIndicator />
        <StatusBar barStyle="default" />
      </View>
    );
  }
}
```



## 實作其他元件

你可能會想要在驗證流程裡放上許多功能，比如重設密碼、註冊等等。同樣的你的 app 也會有許多其他的畫面。我們不會提到如何實作驗證畫面裡的文字輸入、按鈕等等東西，這不在我們討論的範圍。於此我們僅填進一些簡單的內容。

```javascript
class SignInScreen extends React.Component {
  static navigationOptions = {
    title: 'Please sign in',
  };

  render() {
    return (
      <View style={styles.container}>
        <Button title="Sign in!" onPress={this._signInAsync} />
      </View>
    );
  }

  _signInAsync = async () => {
    await AsyncStorage.setItem('userToken', 'abc');
    this.props.navigation.navigate('App');
  };
}

class HomeScreen extends React.Component {
  static navigationOptions = {
    title: 'Welcome to the app!',
  };

  render() {
    return (
      <View style={styles.container}>
        <Button title="Show me more of the app" onPress={this._showMoreApp} />
        <Button title="Actually, sign me out :)" onPress={this._signOutAsync} />
      </View>
    );
  }

  _showMoreApp = () => {
    this.props.navigation.navigate('Other');
  };

  _signOutAsync = async () => {
    await AsyncStorage.clear();
    this.props.navigation.navigate('Auth');
  };
}

// More code like OtherScreen omitted for brevity
```

這樣我們就做好了。現在 `SwitchNavigator` 還不支援畫面間的動畫效果。如果你覺得這個很重要，請到 [Canny 上](https://react-navigation.canny.io/feature-requests)告訴我們囉。