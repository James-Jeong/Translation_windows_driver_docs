# choosing-a-driver-model

Created: Jul 31, 2019 9:36 AM
Last Edited Time: Jul 31, 2019 10:36 AM

# 드라이버 모델 선택하기

마이크로소프트는 당신이 드라이버들을 만들기 위해 사용할 수 있는 다양한 드라이버 모델을 제공합니다. 최적의 드라이버 모델을 선택하기 위한 방법은 당신이 만들려고 계획 중인 드라이버의 종류에 의존합니다. 아래 내용은 고려 사항입니다:

- 장치 함수 드라이버
- 장치 필터 드라이버
- 소프트웨어 드라이버
- 파일 시스템 필터 드라이버
- 파일 시스템 드라이버

다양한 종류의 드라이버들 사이의 차이점들에 대한 논의를 하는 동안, 무엇이 드라이버이고 장치 노드들과 장치 스택들인지 알아봐라. 다음 내용들은 드라이버의 각 종류마다 어떻게 모델을 선택하는지에 대해 설명해 줄 것이다. 

## 장치 함수 드라이버를 위한 드라이버 모델 선택하기

당신이 하드웨어 장치를 설계할 때, 첫 번째로 고려해야할 사항들 중 하나는 당신이 함수 드라이버 제작 필요 여부이다. 다음 질문들로 물어보자:

당신은 드라이버 제작을 전적으로 피할 수 있는가? 만약 함수 드라이버를 만들어야만 한다면, 사용하기에 최적의 드라이버 모델은 무엇인가? 이 질문들에 답하기 위해서 **장치와 드라이버 기술들**에 작성된 기술들의 목록들에 적합한 당신의 장치가 어디에 있는지 알아내라. 함수 드라이버 제작 필요 여부를 알아낼 수 있고 당신의 장치에 적합한 드라이버 모델들을 배울 수 있는 특정 기술에  대한 문서들을 찾아보자.

각 기술들의 일부는 소형 드라이버 모델들을 포함하고 있다. 소형 모델에서는, 장치 드라이버는 두 개의 부분으로 구성되어 있습니다: one that handles general tasks, and one that handles device-specific tasks. Typically, Microsoft writes the general portion and the device manufacturer writes the device-specific portion. The device specific portions have a variety of names, most of which share the prefix *mini*. Here are some of the names used in minidriver models:

- Display miniport driver
- Audio miniport driver
- Battery miniclass driver
- Bluetooth protocol driver
- HID minidriver
- WIA minidriver
- NDIS miniport driver
- Storage miniport driver
- Streaming minidriver

For an overview of minidriver models, see [Minidrivers and driver pairs](https://github.com/James-Jeong/windows-driver-docs/blob/staging/windows-driver-docs-pr/gettingstarted/minidrivers-and-driver-pairs.md).

Not every technology listed in [Device and driver technologies](https://docs.microsoft.com/windows-hardware/drivers/device-and-driver-technologies) has a dedicated minidriver model. The documentation for a particular technology might advise you to use the [Kernel-Mode Driver Framework (KMDF)](https://docs.microsoft.com/windows-hardware/drivers/wdf/); the documentation for another technology might advise you to use the [User-Mode Driver Framework (UMDF)](https://docs.microsoft.com/windows-hardware/drivers/wdf/). The key point is that you should start by studying the documentation for your specific device technology. If your device technology has a minidriver model, you must use the minidriver model. Otherwise follow the advice in the your technology-specific documentation about whether to use the UMDF, KMDF, or the Windows Driver Model (WDM).

## **Choosing a driver model for a device filter driver**

Frequently several drivers participate in a single I/O request (like reading data from a device). The drivers are layered in a stack, and the conventional way to visualize the stack is with the first driver at the top and the last driver at the bottom. The stack has one function driver and can also have filter drivers. For a discussion about function drivers and filter drivers, see [What is a driver?](https://github.com/James-Jeong/windows-driver-docs/blob/staging/windows-driver-docs-pr/gettingstarted/what-is-a-driver-.md) and [Device nodes and device stacks](https://github.com/James-Jeong/windows-driver-docs/blob/staging/windows-driver-docs-pr/gettingstarted/device-nodes-and-device-stacks.md).

If you are preparing to write a filter driver for a device, determine where your device fits in the list of technologies described in [Device and driver technologies](https://docs.microsoft.com/windows-hardware/drivers/device-and-driver-technologies). Check to see whether the documentation for your particular device technology has any guidance on choosing a filter driver model. If the documentation for your device technology does not offer this guidance, then first consider using UMDF as your driver model. If your filter driver needs access to data structures that are not available through UMDF, consider using KMDF as your driver model. In the extremely rare case that your driver needs access to data structures not available through KMDF, use WDM as your driver model.

## **Choosing a driver model for a software driver**

A driver that is not associated with a device is called a *software driver*. For a discussion about software drivers, see the [What is a driver?](https://github.com/James-Jeong/windows-driver-docs/blob/staging/windows-driver-docs-pr/gettingstarted/what-is-a-driver-.md) topic. Software drivers are useful because they can run in kernel mode, which gives them access to protected operating system data. For information about processor modes, see [User mode and kernel mode](https://github.com/James-Jeong/windows-driver-docs/blob/staging/windows-driver-docs-pr/gettingstarted/user-mode-and-kernel-mode.md).

For a software driver, your two options are KMDF and the legacy Windows NT driver model. With both KMDF and the legacy Windows NT model, you can write your driver without being concerned about Plug and Play (PnP) and power management. You can concentrate instead on your driver's primary tasks. With KMDF, you do not have to be concerned with PnP and power because the framework handles PnP and power for you. With the legacy Windows NT model, you do not have to be concerned about PnP and power because kernel-mode services operate in an environment that is completely independent from PnP and power management.

Our recommendation is that you use KMDF, especially if you are already familiar with it. If you want your driver to be completely independent from PnP and power management, use the legacy Windows NT model. If you need to write a software driver that is aware of power transitions or PnP events, you cannot use the legacy Windows NT model; you must use KMDF.

**Note** In the very rare case that you need to write a software driver that is aware of PnP or power events, and your driver needs access to data that is not available through KMDF, you must use WDM.

## **Choosing a driver model for a file system driver**

For help with choosing a model for a file system filter driver, see [File system driver samples](https://docs.microsoft.com/windows-hardware/drivers/samples/file-system-driver-samples). Note that file system drivers can be quite complex and may require knowledge of advanced concepts for driver development.

## **Choosing a driver model for a file system filter driver**

For help with choosing a model for a file system filter driver, see File system minifilter drivers and [File system filter drivers](https://docs.microsoft.com/windows-hardware/drivers/ifs/file-system-filter-drivers).

## **Choosing a driver model for a file system minifilter driver**

For help choosing a model for a file system minifilter driver, see [File System Minifilter Drivers](https://docs.microsoft.com/windows-hardware/drivers/ifs/file-system-minifilter-drivers).

## **Related topics**

[Kernel-Mode Driver Framework](https://docs.microsoft.com/windows-hardware/drivers/wdf/)

[User-Mode Driver Framework](https://docs.microsoft.com/windows-hardware/drivers/wdf/)
