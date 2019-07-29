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
ms.openlocfilehash: 567e869ab9ccb2f29cd0e88ba2e44d7d1b4a296c
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595269"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Como converter por trás de firewalls IP com o API de Tradução de Texto

API de Tradução de Texto pode traduzir por trás de firewalls usando o nome de domínio ou a filtragem de IP. A filtragem de nome de domínio é o método preferencial. **Não recomendamos** a execução do Microsoft Translator por trás de um firewall filtrado por IP. A configuração provavelmente será interrompida no futuro sem aviso prévio.

## <a name="translator-ip-addresses"></a>Endereços IP do Tradutor
Os endereços IP para api.cognitive.microsofttranslator.com-Microsoft API de Tradução de Texto a partir de 20 de novembro de 2018:

* **Pacífico Asiático:** 40.90.139.163, 104.44.89.44
* **Européia** 40.90.138.4, 40.90.141.99
* **América do Norte:** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Converter por trás firewalls IP em sua chamada à API do Tradutor](reference/v3-0-translate.md)
