---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282750"
---
O manuseamento de áudio comprimido é implementado utilizando [`GStreamer`](https://gstreamer.freedesktop.org) . Por razões de `GStreamer` licenciamento, os binários não são compilados e ligados ao SDK do Discurso. Os desenvolvedores precisam de instalar várias dependências e plugins, ver [instalar no Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). `GStreamer` binários precisam estar no caminho do sistema, para que o SDK de fala possa carregar os binários durante o tempo de funcionamento. Se o SDK de discurso for capaz de encontrar `libgstreamer-1.0-0.dll` durante o tempo de funcionamento, significa que os binários estão no caminho do sistema.

