---
title: Configurar regras e ações no Azure IoT Central | Microsoft Docs
description: This how-to article shows you, as a builder, how to configure telemetry-based rules and actions in your Azure IoT Central application.
author: vavilla
ms.author: vavilla
ms.date: 11/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 76a9d8302d53d50bc3c1e306e31fbb26ed230436
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484915"
---
# <a name="configure-rules-preview-features"></a>Configure rules (preview features)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Este artigo aplica-se a operadores, construtores e administradores.*

Rules in IoT Central serve as a customizable response tool that trigger on actively monitored events from connected devices. The following sections describe how rules are evaluated.

## <a name="select-target-devices"></a>Select target devices

Use the target devices section to select on what kind of devices this rule will be applied. Filters allow you to further refine what devices should be included. The filters use properties on the device template to filter down the set of devices. Filters themselves don't trigger an action. In the following screenshot, the devices that are being targeted are of device template type **Refrigerator**. The filter states that the rule should only include **Refrigerators** where the **Manufactured State** property equals **Washington**.

![Condições](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Use multiple conditions

Conditions are what rules trigger on. Currently, when you add multiple conditions to a rule, they're logically AND'd together. In other words, all conditions must be met for the rule to evaluate as true.  

In the following screenshot, the conditions check when the temperature is greater than 90 and the humidity is less than 10. When both of these statements are true, the rule evaluates to true and triggers an action.

![Condições](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>Use aggregate windowing

Rules evaluate aggregate time windows as tumbling windows. In the screenshot below, the time window is five minutes. Every five minutes, the rule evaluates on the last five minutes of data. The data is only evaluated once in the window to which it corresponds.

![Tumbling Windows](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Use rules with IoT Edge modules

A restriction applies to rules that are applied to IoT Edge modules. Rules on telemetry from different modules aren't evaluated as valid rules. Take the following as an example. The first condition of the rule is on a temperature telemetry from Module A. The second condition of the rule is on a humidity telemetry on Module B. Since the two conditions are from different modules, this is an invalid set of conditions. The rule isn't valid and will throw an error on trying to save the rule.

## <a name="next-steps"></a>Passos seguintes

Now that you've learned how to configure a rule in your Azure IoT Central application, you can:

> [!div class="nextstepaction"]
> [Analyze your data on the fly](howto-create-analytics.md)
