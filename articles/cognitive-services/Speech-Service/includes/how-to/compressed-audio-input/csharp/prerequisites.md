---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81422193"
---
O manuseamento de áudio comprimido é implementado utilizando [o GStreamer](https://gstreamer.freedesktop.org). Por razões de licenciamento, os binários GStreamer não são compilados e ligados ao SDK do discurso. Os desenvolvedores precisam de instalar várias dependências e plugins, ver [instalar no Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Os binários Gstreamer precisam de estar no caminho do sistema, para que o SDK da fala possa carregar binários gstreamer durante o tempo de funcionamento. Se a SDK de fala for capaz de encontrar libgstreamer-1.0-0.dll durante o tempo de funcionamento, significa que os binários gstreamer estão no caminho do sistema.

