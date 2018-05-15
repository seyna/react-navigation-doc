# 傳送參數至路由

既然我們知道了如何[在 StackNavigator 裡新建路由](/fundamentals/hello_react_navigation.md)，以及[在路由之間移動](/fundamentals/moving_between_screens.md)，現在就來看看我們如何在移動至路由的時候傳送資料過去。

有兩個部分：

1. 在 `navigation.navigate` 函式裡的第二個參數擺上參數的物件：`this.props.navigation.navigate('RouteName', { /* 參數放在這兒 */ })`
2. 在畫面元件裡讀取參數：`this.props.navigation.state.params`。如果你想要直接取出參數，比方說這樣 `this.props.itemId`，那麼你會需要使用社群所開發的 [react-navigation-props-mapper](https://github.com/vonovak/react-navigation-props-mapper) 套件。

```javascript
class HomeScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Home Screen</Text>
        <Button
          title="Go to Details"
          onPress={() => {
            /* 1. 帶著參數導航到 Details 路由 */
            this.props.navigation.navigate('Details', {
              itemId: 86,
              otherParam: 'anything you want here',
            });
          }}
        />
      </View>
    );
  }
}

class DetailsScreen extends React.Component {
  render() {
    /* 2. 在 navigation state 裡讀出參數*/
    const { params } = this.props.navigation.state.params;
    const itemId = params ? params.itemId : null;
    const otherParam = params ? params.otherParam : null;

    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Details Screen</Text>
        <Text>itemId: {JSON.stringify(itemId)}</Text>
        <Text>otherParam: {JSON.stringify(otherParam)}</Text>
        <Button
          title="Go to Details... again"
          onPress={() => this.props.navigation.navigate('Details')}
        />
        <Button
          title="Go back"
          onPress={() => this.props.navigation.goBack()}
        />
      </View>
    );
  }
}
```



## 總結

- `navigate` 有第二個引數(選填)供你傳遞參數至欲前往的路由。舉例來說，`this.props.navigation.navigate('RouteName', {paramName: 'value'})` 推入新的路由到 `StackNavigator` 裡，並且伴著 `{paramName: 'value'}` 參數。
- 你可以透過 `this.props.navigation.state.params` 讀取參數。當沒有指定參數時，會是 `null`