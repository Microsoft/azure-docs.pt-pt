---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 07405ad0da9c9ba280810402a638395a7feb8554
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637303"
---
O manuseamento do áudio comprimido é implementado utilizando [gStreamer](https://gstreamer.freedesktop.org). Por razões de licenciamento, os binários GStreamer não são compilados e ligados ao SDK da Fala. Os desenvolvedores precisam de instalar várias dependências e plugins, ver [Instalação no Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Os binários gstreamer precisam de estar no caminho do sistema, para que o sdk da fala possa carregar binários gstreamer durante o tempo de funcionamento. Se o discurso SDK for capaz de encontrar libgstreamer-1.0-0.dll durante o tempo de funcionamento, significa que os binários gstreamer estão no caminho do sistema.

