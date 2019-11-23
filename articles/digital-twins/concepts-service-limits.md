---
title: Public preview service limits - Azure Digital Twins | Microsoft Docs
description: Learn about public preview service, subscription, instance, and rate limits for Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: f54311af65d9678b2a51b23a38bab66111a818ca
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383074"
---
# <a name="public-preview-service-limits"></a>Limites do serviço de pré-visualização pública

During the public preview, Azure Digital Twins has the following temporary subscription, instance, and rate limits.

These constraints exist to help simplify learning about the new service and its many features.

> [!NOTE]
> These limits will be increased or removed by general availability (GA).

## <a name="per-subscription-limits"></a>Per-subscription limits

During the public preview, each Azure subscription can create or run only one Azure Digital Twins instance at a time.

> [!TIP]
> If you delete your instance, you can create a new one.

## <a name="per-instance-limits"></a>Per-instance limits

In turn, each Azure Digital Twins instance can have:

- Exactly one embedded **IoTHub** resource that's created automatically during service provisioning.
- Exactly One **EventHub** endpoint for the event type **DeviceMessage**.
- Up to three **EventHub**, **ServiceBus**, or **EventGrid** endpoints of the event type **SensorChange**, **SpaceChange**, **TopologyOperation**, or **UdfCustom**.

> [!NOTE]
> Some parameters that are usually defined in creating the above Azure IoT entities are not required during public preview.
> - Consult the [Swagger reference documentation](./how-to-use-swagger.md) for the most recent API specifications.

## <a name="azure-digital-twins-management-api-limits"></a>Azure Digital Twins Management API limits

The request rate limits for your Azure Digital Twins Management API are:

- 100 requests per second to the Azure Digital Twins Management API.
- Up to 1,000 objects returned by a single Azure Digital Twins Management API query.

> [!IMPORTANT]
> If you exceed the 1,000-object limit, you receive an error and must simplify your query.

## <a name="user-defined-functions-rate-limits"></a>User-defined functions rate limits

The following limits set the total number of all user-defined function calls made to your Azure Digital Twins instance:

- 400 client library calls per second
- 100 **SendNotification** calls per second

> [!NOTE]
> The following actions might cause additional rate limits to be applied temporarily:
> - Edits made to the topology object metadata
> - Updates made to the user-defined function definition
> - Devices that send telemetry for the first time

## <a name="device-telemetry-limits"></a>Device telemetry limits

The following limits cap the total number of all messages your devices can send to your Azure Digital Twins instance:

- 100 messages per second across all devices
-   25 messages per second per device

## <a name="next-steps"></a>Passos seguintes

- To try out an Azure Digital Twins sample, go to [Quickstart to find available rooms](./quickstart-view-occupancy-dotnet.md).
