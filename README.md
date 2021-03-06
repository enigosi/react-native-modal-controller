## React Native Modal Controller

Modals can be fiddly in React Native. Lets say for example you have a mostly-fullscreen modal with a form in it and you want to show an error modal after an incorrect submission - you'd have to close the form modal and once `onDismiss` is called open the error modal.

React Native Modal Controller aims to solve this by providing a control component that manages your one or many modals. It does this by opening a single React Native Modal with a single backdrop.

For example, you might want to have a modal and a tray with a picker in it:

<img src="https://i.imgur.com/6JhOGID.gif" width="200" />

The code for the above looks like:

```js
import React from 'react';
import { StyleSheet, Text, View, Button } from 'react-native';
import showModal, { PRIORITIES, ModalController } from 'react-native-modal-controller';
import AlertModal from './components/alert';
import TrayModal from './components/tray';

export default class App extends React.Component {
  handleShowtray = () => {
    showModal({
      name: 'TRAY',
      priority: PRIORITIES.OVERRIDE,
      modalProps: {
        message: 'Hey tray.',
        onOpenAlert: () => this.handleOpen(PRIORITIES.STANDARD),
      },
    });
  };
  handleOpen = (priority) => {
    showModal({
      name: 'ALERT',
      priority: priority || PRIORITIES.OVERRIDE,
      modalProps: {
        message: 'Hello, modal.',
        onOpenAnother: this.handleOpen,
        onOpenTray: this.handleShowtray
      },
    });
  };

  render() {
    return (
      <View style={styles.container}>
        <Button title="Open Modal" onPress={this.handleOpen} />
        <ModalController
          customAnimations={{
            slideTrayUp: {
              from: { bottom: -300 },
              to: { bottom: 0 }
            },
            slideTrayDown: {
              from: { bottom: 0 },
              to: { bottom: -300 }
            }
          }}
          modals={{
            ALERT: { 
              animationIn: 'zoomInDown',
              animationOut: 'lightSpeedOut',
              Component: AlertModal 
            },
            TRAY: {
              animationIn: 'slideTrayUp',
              animationOut: 'slideTrayDown',
              Component: TrayModal,
              absolutePositioning: {
                bottom: 0,
                left: 0,
                right: 0,
              },
            },
          }}
        />
      </View>
    );
  }
}

```

Modal Control is implemented in a very similar way to [react-native-modal](https://github.com/react-native-community/react-native-modal), by using [react-native-animatable](https://github.com/oblador/react-native-animatable). So you'll notice in the above example that you can use any of the animatable animations - or implement your own.

### Priorities

**`PRIORITIES.STANDARD`** - Open the modal alongside (on-top-of) the existing modals - if any.

**`PRIORITIES.OVERRIDE`** - Open the modal and close all existing modals.

### `ModalController`

`ModalController` should be mounted at the route of your app and only mounted once, similar to how you'd set up routing.

#### Props

##### `modals` - required
---

```js
type ModalsPropType = {
	[name: string]: {
		Component: React.Component, // required
		animationIn?: string,
		animationOut?: string,
		animationInDuration?: number,
		animationOutDuration?: number,
		absolutePositioning?: Object, // Position the modal absolutely with given styles
	}
};
```

##### `customAnimations` - optional
---

```js
type CustomAnimationsProp = ?{
	[name: string]: {
		from: Object,
		to: Object,
	}
};
```

##### `activeBackdropOpacity` - optional
---

```js
type ActiveBackdropOpacityProp = ?number;
```


##### `backdropTransitionInTiming` - optional
---

```js
type BackdropTransitionInTimingProp = ?number;
```

##### `backdropTransitionOutTiming` - optional
---

```js
type BackdropTransitionOutTimingProp = ?number;
```

### `showModal`

`showModal` is the default export and can be used to show one of your modals based on the config you pass it.

#### Args

##### `name` - required
---

```js
type name = string; // The key used in your modals prop of ModalController
```

##### `modalProps` - optional
---

```js
type modalProps = Object; // Props passed to your Component
```

##### `priority` - optional
---

```js
type priority = $Keys<typeof PRIORITIES>;
```

#### Overrides

You can also pass in any of the `ModalsPropType`s apart from Component to override your defaults.




