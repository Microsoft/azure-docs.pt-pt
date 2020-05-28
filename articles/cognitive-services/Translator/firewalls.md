---
title: Traduzir atrás de firewalls - Tradutor
titleSuffix: Azure Cognitive Services
description: O Tradutor de Serviços Cognitivos Azure pode traduzir-se atrás de firewalls utilizando o nome de domínio ou a filtragem IP.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 78a53c99f5f184c1b6b45d59d86c23efb898d7dc
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996963"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Como traduzir por trás de firewalls IP com Tradutor

O tradutor pode traduzir-se atrás de firewalls utilizando o nome de domínio ou a filtragem IP. A filtragem do nome de domínio é o método preferido. Não **recomendamos** executar o Microsoft Tradutor por trás de uma firewall filtrada IP. É provável que a configuração se rompa no futuro sem aviso prévio.

## <a name="translator-ip-addresses"></a>Endereços IP tradutor
Os endereços IP para api.cognitive.microsofttranslator.com - Tradutor a partir de 21 de agosto de 2019:

* **Ásia-Pacífico:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **América do Norte:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>Próximos passos
> [!div class="nextstepaction"]
> [Traduzir atrás de firewalls IP em Tradutor](reference/v3-0-translate.md)
