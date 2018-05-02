# 抽屜導航

```javascript
class MyHomeScreen extends React.Component {
  static navigationOptions = {
    drawerLabel: 'Home',
    drawerIcon: ({ tintColor }) => (
      <Image
        source={require('./chats-icon.png')}
        style={[styles.icon, {tintColor: tintColor}]}
      />
    ),
  };

  render() {
    return (
      <Button
        onPress={() => this.props.navigation.navigate('Notifications')}
        title="Go to notifications"
      />
    );
  }
}

class MyNotificationsScreen extends React.Component {
  static navigationOptions = {
    drawerLabel: 'Notifications',
    drawerIcon: ({ tintColor }) => (
      <Image
        source={require('./notif-icon.png')}
        style={[styles.icon, {tintColor: tintColor}]}
      />
    ),
  };

  render() {
    return (
      <Button
        onPress={() => this.props.navigation.goBack()}
        title="Go back home"
      />
    );
  }
}

const styles = StyleSheet.create({
  icon: {
    width: 24,
    height: 24,
  },
});

const MyApp = DrawerNavigator({
  Home: {
    screen: MyHomeScreen,
  },
  Notifications: {
    screen: MyNotificationsScreen,
  },
});
```

欲開啟或關閉抽屜，可以分別使用 `DrawerOpen` 與 `DrawerClose`。

```javascript
this.props.navigation.navigate('DrawerOpen'); // open drawer
this.props.navigation.navigate('DrawerClose'); // close drawer
```

如果你想要的是開合，可以使用 `DrawerToggle`，它會針對目前的抽屜狀態做出適當的行為。

```javascript
// fires 'DrawerOpen'/'DrawerClose' accordingly
this.props.navigation.navigate('DrawerToggle');
```

