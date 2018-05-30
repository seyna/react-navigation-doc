# 追蹤畫面

底下的範例將教你如何追蹤畫面並且將數據傳送到 Google Analytics。這個方法也可以套用到其他的分析 SDK。

當使用預設的導航容器時，我們可以使用 `onNavigationStateChange ` 追蹤畫面。

```javascript
import { GoogleAnalyticsTracker } from 'react-native-google-analytics-bridge';

const tracker = new GoogleAnalyticsTracker(GA_TRACKING_ID);

// gets the current screen from navigation state
function getCurrentRouteName(navigationState) {
  if (!navigationState) {
    return null;
  }
  const route = navigationState.routes[navigationState.index];
  // dive into nested navigators
  if (route.routes) {
    return getCurrentRouteName(route);
  }
  return route.routeName;
}

const AppNavigator = StackNavigator(AppRouteConfigs);

export default () => (
  <AppNavigator
    onNavigationStateChange={(prevState, currentState) => {
      const currentScreen = getCurrentRouteName(currentState);
      const prevScreen = getCurrentRouteName(prevState);

      if (prevScreen !== currentScreen) {
        // the line below uses the Google Analytics tracker
        // change the tracker here to use other Mobile analytics SDK.
        tracker.trackScreenView(currentScreen);
      }
    }}
  />
);
```

# 透過 Redux 追蹤畫面

當使用 Redux 時，我們可以寫一個 Redux middleware 以追蹤畫面。我們改寫上一個例子

```javascript
import { NavigationActions } from 'react-navigation';
import { GoogleAnalyticsTracker } from 'react-native-google-analytics-bridge';

const tracker = new GoogleAnalyticsTracker(GA_TRACKING_ID);

const screenTracking = ({ getState }) => next => (action) => {
  if (
    action.type !== NavigationActions.NAVIGATE
    && action.type !== NavigationActions.BACK
  ) {
    return next(action);
  }

  const currentScreen = getCurrentRouteName(getState().navigation);
  const result = next(action);
  const nextScreen = getCurrentRouteName(getState().navigation);
  if (nextScreen !== currentScreen) {
    // the line below uses the Google Analytics tracker
    // change the tracker here to use other Mobile analytics SDK.
    tracker.trackScreenView(nextScreen);
  }
  return result;
};

export default screenTracking;
```

# 建立 Redux store 並將之提供給上述的 middleware

在生成時，`screenTracking` moddleware 可以提供給 store。參考 [Redux Integration](https://v1.reactnavigation.org/docs/Redux-Integration.md) 可以知道更多詳情。

```javascript
const store = createStore(
  combineReducers({
    navigation: navigationReducer,
    ...
  }),
  applyMiddleware(
    screenTracking,
    ...
    ),
);
```

