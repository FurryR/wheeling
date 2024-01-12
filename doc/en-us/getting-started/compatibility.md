# Ensuring compatibility with Gandi IDE


Gandi IDE and Turbowarp's extension format aren't that diffrent from each other;
So, a completely new tutorial for both is optional.

-----------------------------------

## Extension info

Gandi IDE supports the use of `Scratch.extensions.register()` but in order to load an extension in Gandi, it will need a few more information.

```js
// Extension's class...
getInfo() {
    return {
      id: 'extensionId',
      name: 'Extension Name',
      blocks: [/*...*/],
      //Everything here is as the name suggests
      info: {
        name: 'hcn.extensionName',
        description: 'Extension Description',
        extensionId: 'extensionID', /* The same id that you provided above */
        iconURL: cover, /* Yes, the name is misleading for some reason */
        insetIconURL: icon,
        featured: true,
        disabled: false,
        collaborator: 'name',
        collaboratorList: [
          {
            collaborator: 'name',
            collaboratorURL: 'url'
          },
          //The rest of the team...
        ]
      },
      l10n: {
        'zh-cn': {
          'hcn.extensionName': '扩展名',
          'hcn.description': '扩展说明',
        },
        en: {
          'hcn.extensionName': 'Extension Name',
          'hcn.description': 'Extension Description',
        },
      },
      /*-------------------------------------------------------*/
    };
}
//Rest of code...
```
Those infos will be used to show extension cards like these.

<details>
<summary>Gandi Extension cards</summary>

![Gandi extension card](assets/image.png)
</details>

---------------------------------------------------------------------

## Runtime

Turbowarp allows the use of `Scratch.vm.runtime` for extensions to access the runtime, while Gandi IDE requires you to use `this.runtime` via the class's constructor.

```js
constructor(runtime) {
    this.runtime = runtime
}
//you can now use `this.runtime` to access the runtime
```

To fix this, we can check if Scratch.vm.runtime is defined; if yes, we use it; if no, we will use runtime instead.

```js
constructor(runtime) {
    this.runtime = Scratch.vm && Scratch.vm.runtime ? Scratch.vm.runtime : runtime;
}
//You can now use `this.runtime` on both TW and Gandi
```

-----------------------------------------------------------

## Scratch.vm

Gandi IDE doesn't allow you to access its virtual machine sadly 😭😭😭.

-----------------------------------------------------------

## Scratch.Cast

In Gandi people commonly use

```js
import Cast from '../utils/cast.js';
```

to use the Scratch.Cast API. But Gandi IDE **does** support the use of
```js
Scratch.Cast
```

So you are recommened to do it this way to ensure compatibility.

However if you want to use other APIs like `Color` you will have to manually import it from `../utils/color.js`.

------------------------------------------------------------------

## Scratch.translate

You can use `Scratch.translate()` but it requires you to add this inside your extension's class

```js
formatMessage(id) {
    return Scratch.translate({
      id,
      default: id,
      description: id,
    })
  }
```

and you will need to use `this.formatMessage()` instead.

This is because `runtime.getFormatMessage()` is only available in Gandi IDE and doing `Scratch.translate()` directly is not supported by Gandi.

------------------------------------------------------------------

In the future, as Gandi IDE is updated, more and more features from Turbowarp will be added. For now some Turbowarp features are missing in Gandi and the other way around. You'll have to figure out another way to port it yourself.