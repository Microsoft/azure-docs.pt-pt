---
title: Relocalização grosseira em C++/WinRT
description: Explicação aprofundada de como criar e localizar âncoras utilizando uma relocalização grosseira em C++/WinRT.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7068ecb7fcfe888118396523e0e3d997e5a59e9a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005015"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-cwinrt"></a>Como criar e localizar âncoras utilizando uma relocalização grosseira em C++/WinRT

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

As Âncoras Espaciais Azure podem associar dados de sensores no dispositivo, posicionando os dados do sensor com as âncoras que cria. Estes dados também podem ser utilizados para determinar rapidamente se existem âncoras nas proximidades do seu dispositivo. Para mais informações, consulte [a relocalização de Coarse.](../concepts/coarse-reloc.md)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, certifique-se de que tem:

- Conhecimentos básicos sobre C++ e as <a href="/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt" target="_blank">APIs de execução</a>do Windows.
- Leia a visão geral das [âncoras espaciais de Azure](../overview.md).
- Completou um dos [Quickstarts de 5 minutos.](../index.yml)
- Leia através do [Create e localize as âncoras como fazer.](../create-locate-anchors-overview.md)

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Allow GPS
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);

// Allow WiFi scanning
sensors.WifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

// Allow the set of known BLE beacons
sensors.BluetoothEnabled(true);
sensors.KnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```cpp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();
nearDeviceCriteria.DistanceInMeters(5.0f);
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]