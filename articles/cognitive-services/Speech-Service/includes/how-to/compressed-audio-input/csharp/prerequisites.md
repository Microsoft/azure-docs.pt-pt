---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 8c77efe9e3e301573b032d1dc1dd32bb4a5ab1a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417733"
---
O manuseamento de áudio comprimido é implementado utilizando [o GStreamer](https://gstreamer.freedesktop.org). Por razões de licenciamento, os binários GStreamer não são compilados e ligados ao SDK do discurso. Os desenvolvedores precisam de instalar várias dependências e plugins, ver [instalar no Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) ou instalar no [Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). Os binários GStreamer precisam de estar no caminho do sistema, para que o SDK do discurso possa carregar os binários durante o tempo de funcionamento. Por exemplo, no Windows, se o SDK de discurso for capaz de encontrar `libgstreamer-1.0-0.dll` ou `gstreamer-1.0-0.dll` (para o gstreamer mais recente) durante o tempo de funcionamento, significa que os binários gstreamer estão no caminho do sistema.

