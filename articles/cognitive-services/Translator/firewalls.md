---
title: Traduzir atrás de firewalls - Tradutor Text API
titleSuffix: Azure Cognitive Services
description: O Tradutor de Serviços Cognitivos Azure API pode traduzir-se atrás de firewalls utilizando o nome de domínio ou a filtragem IP.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cd7904fedd3ab3f64315cb6f98d99b8fd12254f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837391"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Como traduzir por trás de firewalls IP com a API de Texto tradutor

A API de texto de tradução pode traduzir-se atrás de firewalls utilizando o nome de domínio ou a filtragem IP. A filtragem do nome de domínio é o método preferido. Não **recomendamos** executar o Microsoft Tradutor por trás de uma firewall filtrada IP. É provável que a configuração se rompa no futuro sem aviso prévio.

## <a name="translator-ip-addresses"></a>Endereços IP tradutor
Os endereços IP para api.cognitive.microsofttranslator.com - Microsoft Tradutor Text API a partir de 21 de agosto de 2019:

* **Ásia-Pacífico:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **América do Norte:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Traduza por trás de firewalls IP na sua chamada TradutorA API](reference/v3-0-translate.md)
