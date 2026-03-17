---
title: "A Guide to Interfaces in Programming for Beginners"
date: 2023-11-25T10:13:11+01:00
author: "Orim Dominic Adah"
description: "This aim of this article it to help beginners in programming to understand what an interface is using a USB port as an analogy"
draft: false
showToc: true
TocOpen: false
comments: false
hidemeta: false
disableShare: false
searchHidden: false
tags: ["interface", "programming"]
---

## What is an Interface?

An interface is a component that another component can interact with to perform one or more operations.

Most beginners in programming encounter the term _interface_ when they dive into object-oriented programming or clean code principles like SOLID. This article will help you understand what an interface is, irrespective of the programming language that you use. It will also cover the concept of _programming to an interface, not an implementation_.

## An Interface is like a USB Port

What are the similarities between an interface and a USB port? What can we learn about interfaces from a USB port?

{{< figure caption="A Phone's USB Port. Source - techpoint.africa" align=center src="../images/a-phone-usb-port.jpg" >}}

If you have a smart phone with a USB charging port, you will find that aside from using that port for charging your device, you can

1. plug in an OTG device into the same port and view the files in the OTG device's memory through your phone,

   {{< youtube id="YBOTY8pXSvk" autoplay="false" title="OTG device connected to a phone">}}

2. plug in an OTG bluetooth device connected to a wireless mouse and operate your phone with the mouse.

   {{< youtube id="HFPcdugqupw" autoplay="false" title="OTG bluetooth device connected to a phone" >}}

The USB port component of your phone serves as an interface through which other components like a charger and an OTG device can **interact** with your phone to perform one or more operations.

> An interface is a component that another component can interact with to perform one or more operations.

The manner in which the USB Port is shaped makes it a suitable point of interaction between your phone and other components. As a result, it demands that whatever is going to interact with the phone through the USB port should have the _shape_ that you see on the OTG device and the charger.

## The Behaviour of Interfaces

Did you notice that the USB port does not detemine the specific use case of the components that interact with it? It does not determine whether a component that interacts with it will
- share the contents of its memory with it as in an OTG memory device
- charge it as in the case of a charger
- control the screen as in the case of the OTG bluetooth device

These components have specific built-in behaviour and the USB port interface accommodates them all if they fit into the port. This is an important behaviour of interfaces (including programming interfaces) that should be kept in mind.

> Interfaces do not determine the behaviour of the components that connect with them. They only determine how components can interact through them.

## From Analogy to Code

Using a phone's USB port as an analogy for an interface, the `Phone` class in the code snippet below has an `acceptUSBDevice` method. When a component or device is connected to the port, `acceptUSBDevice` is executed.

The `acceptUSBDevice` accepts an argument called `component`. Any component that will be passed into the `acceptUSBDevice` method must fit the _shape_ of the `USBComponent` interface. In other words, the component must have the `performOperations` method.

```typescript
interface USBComponent {
  performOperations(): void;
}

class Phone {
  acceptUSBDevice(component: USBComponent) {
    component.performOperations();
  }
}

class USBCharger {
  performOperations() {
    console.log(
      "Transferring electric current from the socket to the phone's battery"
    );  }
}

class OTGBluetoothMouse {
  performOperations() {
    console.log("Currently pointing at position (X, Y) on the phone's screen");
  }
}

class OTGMemoryDrive {
  performOperations() {
    console.log("Currently displaying files on the device");
  }
}

const phone = new Phone();
const charger = new USBCharger();
const bluetoothMouse = new OTGBluetoothMouse();
const memoryDrive = new OTGMemoryDrive();

phone.acceptUSBDevice(charger); // 1
phone.acceptUSBDevice(bluetoothMouse); // 2
phone.acceptUSBDevice(memoryDrive); // 3
```

You can try this out in this [programming to an interface TypeScript example](https://www.typescriptlang.org/play/?#code/JYOwLgpgTgZghgYwgAgKoGUBCBhA9gWwAdcQJxkBvAKGVuUOhlynwHkGo4xgSBnACgCUALmQA3XMAAmAbioBfKlQQAbOL17IACgAsSKanWQISpBGABqwOBkxbmYfiaL7wo23helwgyjSN0zsTeYAB0HEws7NBcPCACgnJGiorKahpoWNg6cFAA5tB+RhHMbByxfEJFAbQm8bgqEKEquHn8AEQAKpzxMNBQoHnIEI3mAwjGAK5QUGRgyDBQBMhgOii8uAgA1hDzYLgra-R6pADkmgBGXJBQAJ7tif60KUqq6pqsnQDimCqTu7hcKsALK4Sa8AxPeiMUrRTjcSq+Qw1Yx8BpNFptdrYaazcAqW70STgQbILhE3jABEgZD8AAaABpkABNXwkQ4oQgnCDnZC8BCzMgPJJ0F5pd7IT5fYEQfDMW4AEQGYkhxRhUXK1IS1RqdQ2jWarQ6OJmcwJyCkwF4hDUt1JMGAjU07NWKCkEDEwCQwqhYr18y5+mQAF5kKQAO7ablCOT+4w5fKFUMRzI4BMFKAx5R8eYXP4AoE6UHglDJiCRqW-f77QvFiFZuP4WXypXAFUhsPlyXfGVyu6tlVZqiB0ihOpmSzWWz2KCOBDp6CJZAAemXyAAjMPuWPTBBzFYbFgZ4489XASCwRCl6vkAAmLf6HcgCcH6cOfhNvuK5UQa9rgDMQA)

Each component, the charger, the bluetooth mouse and the memory drive, implement the `performOperations` method in its own specific way. As long as the components fit the _shape_ of the `USBComponent` interface, by having a `performOperations` method, the interaction will work perfectly.

## Programming to an Interface

To program to an interface means to write code that is more concerned about the shape of the data that it handles than the operation that it executes. This type of code is flexible enough to perform multiple implementations.

To illustrate this with code, let us imagine that our USB port was programmed to an implementation and not an interface. If this was the case, the `acceptUSBDevice` method will not have its `component` parameter programmed to the `USBComponent` interface, but to a specific implementation such as `USBCharger`, `OTGBluetoothMouse` or `OTGMemoryDrive`. We will have have this in code instead

```typescript
class Phone {
  acceptUSBDevice(component: USBCharger) {
    component.transferCurrent();
  }
}
```

This works. The issue is that the `acceptUSBDevice` is tied to only being used with a `USBCharger`. No other component can be used with the USB port because when the `transferCurrent` method is executed by `acceptUSBDevice`, it will either not work or it will throw an error. Other components like the OTG memory drive do not have the `transferCurrent` method implementation.

In the case of the last snippet, `acceptUSBDevice` is programmed to an implementation - the `USBCharger` implementation, and that has made the code less flexible to accommodate other use cases of the `acceptUSBDevice` method. The code snippet in the[ programming to an interface TypeScript example](https://www.typescriptlang.org/play/?#code/JYOwLgpgTgZghgYwgAgKoGUBCBhA9gWwAdcQJxkBvAKGVuUOhlynwHkGo4xgSBnACgCUALmQA3XMAAmAbioBfKlQQAbOL17IACgAsSKanWQISpBGABqwOBkxbmYfiaL7wo23helwgyjSN0zsTeYAB0HEws7NBcPCACgnJGiorKahpoWNg6cFAA5tB+RhHMbByxfEJFAbQm8bgqEKEquHn8AEQAKpzxMNBQoHnIEI3mAwjGAK5QUGRgyDBQBMhgOii8uAgA1hDzYLgra-R6pADkmgBGXJBQAJ7tif60KUqq6pqsnQDimCqTu7hcKsALK4Sa8AxPeiMUrRTjcSq+Qw1Yx8BpNFptdrYaazcAqW70STgQbILhE3jABEgZD8AAaABpkABNXwkQ4oQgnCDnZC8BCzMgPJJ0F5pd7IT5fYEQfDMW4AEQGYkhxRhUXK1IS1RqdQ2jWarQ6OJmcwJyCkwF4hDUt1JMGAjU07NWKCkEDEwCQwqhYr18y5+mQAF5kKQAO7ablCOT+4w5fKFUMRzI4BMFKAx5R8eYXP4AoE6UHglDJiCRqW-f77QvFiFZuP4WXypXAFUhsPlyXfGVyu6tlVZqiB0ihOpmSzWWz2KCOBDp6CJZAAemXyAAjMPuWPTBBzFYbFgZ4489XASCwRCl6vkAAmLf6HcgCcH6cOfhNvuK5UQa9rgDMQA) shows how to program to an interface.

## Conclusion

You can find another code illustration for how to program to an interface in this [Salary Manager playground example](https://www.typescriptlang.org/play?#code/PTAEAUCUHkHFIIIFkkEkBytQBVqgeqBtgKKQBiCAwiQFAigC8zLrb77tAxgPYB2AZx4AbAKYA6LmICGAJwAUASloBLPgBdRsgGbSuo0AGVpwuQE8ScvltABvWqEegA5qPUB1aa4FKAXKD4AVwBbACMtByc9XkCNVAATUQ0VbRUtfwF1WTVnWgBfWnoAKiL8UClpAQFQdR5QYOk+LwMBE3NQAAdpM2Ck9Wqi4G5TKqM22TMkRubZO0jHLrNjUwn5VpWLK3SxjctZa1lFOacT8v5M0HX2xkvxzf2tcVcPZp9lU6deQREJYR5neQAA3A3RyoAAJLYrhM8jU6tCzKBRFtZgB3FTqAAWoBUiWSqRskIRewO4miPFi6gSfRSaVkeUB7ycBQKw0q1XIgWEwmwKl6hnU0m02mOTiavQyWRy81A5Mp1PxdMl2T4uRlX0yskCXFqCnFomVOQANLKuDE4nj1LTtpqckd7B9HFiVAJxPqmAFpL0ZSdna65RaaQTZjcA1TLdbZDLWSdnp5vEpRR8NT9xH8AQAiEFmMHaLnCAC0Vt6l0FwozTOT51T6fkGeeoFClRUXFuG1A2lkPGCoHi0kFTYEogrPs+1bEaf+dcS8W16hq0gAHqIBCPHWdvhPaxmZ3POkkBCp+GvHSmt1OM7I3IF9jVMQYrw01IlZCeq5vfheSt+f7+-7+31OK91BvPhQAARgABmg0AGEaWVZFCDFZHaAA3ExAlEaN8kKCpRiofgsj0dQBSFEUHSiM0KUDRUbSlVVChODUsjnHgFDDBUrWDQ1VXtUcnUxF0ySo+UI2DD0OLEulsJlONXkTCj3yEc9M2zMECI0FCdVLMjAKY8dP0zBtBxbNt2k7bte37aRB2HStTjPQy62A0C7wMEzWwRUB0SxCl50aRFdx1I9BAzUAAGpQAzAAdPg9LHD9J0zf8UtSop4scFzbygmC4LAuQkKIiZQHQ4RMOw1lmLMiYpiaVwQwCURUR2cxapmJRuHOedtBIssRRuaxms5bleX5Pq6wAKUaYcTUBHqAH1MjI+bISmLFxBQvh4m7JRxFqAUVQBRRxAEDphAxOtxArABtcCAF0GWUKquFI4UPUG0ANKInVXu0IEuEWvqVtsNbMQ2xptuCXb9voo6TrOi6MyuxRboexlCgRNr6vERZlnMeQet+5RMembHcbueQXr65RilKBxSnQHhNAEenQD-Vm8Zq0mbGiUQOn6WUwLUTQdD0AxOfuA4TQhxtRC4aRAiHUAeBFLF+2lvhESW4UTS+b75zYjt8xqPlRF1+Dwk6dlRHiVnakFnFBEFPh9GV1X7xarm6psPDqhlhsulxNyVFmBFxFZ1ARQlrGtAAcmqcn216Xz4lATFpFT8Ikk6LtnBQ4JelT+3ef5h2+TO0Reg0fsQpNARtWxSo3M+witN6sjldmLEsLZ0Bhp5U3fpNVEDFRClhFT-hhERS2bLEOFQEVgwMQXxZm68lXm-LsQq8FK1+DvfscX8s0+f6cRQGgG8c-+fPWfCXhemqNRtDERcVFCMQAG4N0PF8wTVvOEe9Ruhp2kKhFo3YDBM3vF3MwHQVxu2qoiZEDxZDPzAt3I2IErxwh4OHXurNsAe20DwLgiscTVHth0XO+cF5YOsDbaom8sHJ0xDweIJo+BM3oR7XENJ1CIhYXwvgHRAjqHDoMIAA) with notes attached to it.

Cheers!
