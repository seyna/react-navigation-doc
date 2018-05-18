# 在不使用 navigation prop 下導航

用 `navigation` prop 裡的 `navigate` 或 `popToTop` 函式不是唯一一種導航方式，你還可以在最高階層的 navigator 裡指派導航的行為。

有些情況下這種方法會很管用，比如在某些你無法取得 `navigation` prop 的地方，或者你就是不想使用 `navigation` prop。

你可以運用 `ref` 取得 navigator，並且將之傳遞到我們待會會用到的 `NavigationService`。請記住這只會在最高階層 (root) 的 navigator 產生作用。

```javascript
// App.js

import NavigationService from './NavigationService';

const TopLevelNavigator = StackNavigator({ /* ... */ })

class App extends React.Component {
  // ...

  render(): {
    return (
      <TopLevelNavigator
        ref={navigatorRef => {
          NavigationService.setTopLevelNavigator(navigatorRef);
        }}
      />
    );
  }
}
```

接著，我們將 `NavigationService` 定義為擁有使用自行定義的導航行為的模組。

```javascript
// NavigationService.js

import { NavigationActions } from 'react-navigation';

let _navigator;

function setTopLevelNavigator(navigatorRef) {
  _navigator = navigatorRef;
}

function navigate(routeName, params) {
  _navigator.dispatch(
    NavigationActions.navigate({
      type: NavigationActions.NAVIGATE,
      routeName,
      params,
    })
  );
}

// add other navigation functions that you need and export them

export default {
  navigate,
  setTopLevelNavigator,
};
```

然後，你就可以在任何 javascript 模組裡，匯入這個 `NavigationService` 並且呼叫它匯出的函式。

```javascript
// 在任何 js 模組
import NavigationService from 'path-to-NavigationService.js';

// ...

NavigationService.navigate('ChatScreen', { userName: 'Lucy' });
```

在 `NavigationService` 裡，你可以創造你自己的導航行為，輕易地重複使用它們。