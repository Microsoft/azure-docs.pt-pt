---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 3d4c908e0caf1cf84159df49d98603fd13b75994
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821558"
---
O manuseamento de áudio comprimido é implementado utilizando [o GStreamer](https://gstreamer.freedesktop.org). Por razões de licenciamento, os binários GStreamer não são compilados e ligados ao SDK do discurso. Os desenvolvedores precisam de instalar várias dependências e plugins, ver [instalar no Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) ou instalar no [Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). Os binários GStreamer precisam de estar no caminho do sistema, para que o SDK do discurso possa carregar os binários durante o tempo de funcionamento. Por exemplo, no Windows, se o SDK de discurso for capaz de encontrar `libgstreamer-1.0-0.dll` durante o tempo de funcionamento, significa que os binários gstreamer estão no caminho do sistema.

