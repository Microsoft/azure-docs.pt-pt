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
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: dce41879f77d0bed44daa89c1dabbcc3f92e145e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592530"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Como traduzir por trás de firewalls IP com Tradutor

O tradutor pode traduzir-se atrás de firewalls utilizando o nome de domínio ou a filtragem IP. A filtragem do nome de domínio é o método preferido. Não **recomendamos** executar o Microsoft Tradutor por trás de uma firewall filtrada IP. É provável que a configuração se rompa no futuro sem aviso prévio.

## <a name="translator-ip-addresses"></a>Endereços IP tradutor
Os endereços IP para api.cognitive.microsofttranslator.com - Tradutor a partir de 21 de agosto de 2019:

* **Ásia-Pacífico:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **América do Norte:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Traduzir atrás de firewalls IP em Tradutor](reference/v3-0-translate.md)
