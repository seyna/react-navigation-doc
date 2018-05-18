# Deep linking

在這個章節我們將處理外部連結。假設我們想要在 app 放上一個 `mychat://chat/Eric` 的連結，點擊之後將使用者帶到一個與 Eric 進行對話的聊天室。

## 設定

之前，我們定義 navigator 如下：

```javascript
const SimpleApp = StackNavigator({
  Home: { screen: HomeScreen },
  Chat: { screen: ChatScreen },
});
```

我們想要連結到 "Chat" 畫面，而 `user` 可以是一個參數，好隨時換成不同的聊天對象。所以我們重新設置如下：

```javascript
const SimpleApp = StackNavigator({
  Home: { screen: HomeScreen },
  Chat: {
    screen: ChatScreen,
    path: 'chat/:user',
  },
});
```

## URI Prefix

接著，我們設置導航容器以抽取前來的  URI 路徑

```javascript
const SimpleApp = StackNavigator({...});

// on Android, the URI prefix typically contains a host in addition to scheme
const prefix = Platform.OS == 'android' ? 'mychat://mychat/' : 'mychat://';

const MainApp = () => <SimpleApp uriPrefix={prefix} />;
```

## Expo projects 的設置方法

閱讀 [Expo linking guide](https://docs.expo.io/versions/latest/guides/linking.html) 以取得更多資訊。