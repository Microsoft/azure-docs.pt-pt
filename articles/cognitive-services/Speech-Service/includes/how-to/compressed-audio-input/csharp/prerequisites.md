---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422193"
---
O manuseamento do áudio comprimido é implementado utilizando [gStreamer](https://gstreamer.freedesktop.org). Por razões de licenciamento, os binários GStreamer não são compilados e ligados ao SDK da Fala. Os desenvolvedores precisam de instalar várias dependências e plugins, ver [Instalação no Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Os binários gstreamer precisam de estar no caminho do sistema, para que o sdk da fala possa carregar binários gstreamer durante o tempo de funcionamento. Se o discurso SDK for capaz de encontrar libgstreamer-1.0-0.dll durante o tempo de funcionamento, significa que os binários gstreamer estão no caminho do sistema.

