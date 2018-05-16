# 在任意元件裡讀取 navigation prop

?> 此文章的內容非常實用，如果你的畫面會切割成父子關係的元件，模組化的封裝的話，那麼建議多留意這裡提到的技巧！

`withNavigation` 能夠將 `navigation` prop 傳送至封裝好的元件裡。如果你無法直接將 `navigation` prop 送到元件裡，或者為了避免 nested child 你不想要這麼做的時候，它會很好用。

```javascript
import React from 'react';
import { Button } from 'react-native';
import { withNavigation } from 'react-navigation';

export default class MyBackButton extends React.Component {
  render() {
    // This will throw an 'undefined is not a function' exception because the navigation
    // prop is undefined.
    return <Button title="Back" onPress={() => { this.props.navigation.goBack() }} />;
  }
}
```

為了解決上面看到的錯誤，你可以在畫面生成時將 `navigation` prop 傳至 `MyBackButton`，像這樣：`<MyBackButton navigation={this.props.navigation} />`

又或者你也可以這樣做，透過 `withNavigation` 自動提供 `navigation` prop (如果你好奇的話，這是因為 React 的關係)。這個函式的行為就像是 Redux 的 `connect` 函式，除了它不是提供 `dispatch` prop，它提供的是 `navigation` prop。

```javascript
import React from 'react';
import { Button } from 'react-native';
import { withNavigation } from 'react-navigation';

class MyBackButton extends React.Component {
  render() {
    return <Button title="Back" onPress={() => { this.props.navigation.goBack() }} />;
  }
}

// withNavigation returns a component that wraps MyBackButton and passes in the
// navigation prop
export default withNavigation(MyBackButton);
```

透過這個辦法，你無須再特別寫明傳送 `navigation` prop，即可在任何地方正常地顯示 `MyBackButton`。