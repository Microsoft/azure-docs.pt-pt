---
title: Traduzir por trás de firewalls-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Traduza por trás de firewalls IP com o API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 3d5c775d24c89d126962b6c4bccb4d5a572801ac
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906771"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Como converter por trás de firewalls IP com o API de Tradução de Texto

API de Tradução de Texto pode traduzir por trás de firewalls usando o nome de domínio ou a filtragem de IP. A filtragem de nome de domínio é o método preferencial. **Não recomendamos** a execução do Microsoft Translator por trás de um firewall filtrado por IP. A configuração provavelmente será interrompida no futuro sem aviso prévio.

## <a name="translator-ip-addresses"></a>Endereços IP do Tradutor
Os endereços IP para api.cognitive.microsofttranslator.com-Microsoft API de Tradução de Texto a partir de 21 de agosto de 2019:

* **Pacífico Asiático:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Européia** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **América do Norte:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Converter por trás firewalls IP em sua chamada à API do Tradutor](reference/v3-0-translate.md)
