---
title: Traduzir atrás de firewalls - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Traduza atrás de firewalls IP com a API de texto do Translator.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: 053bc5964644b4183572de35372e2580a80f19ae
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729989"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Como converter atrás de firewalls IP com a API de texto do tradutor

API de texto do Translator pode traduzir atrás de firewalls usando o nome de domínio ou a filtragem de IP. A filtragem de nome de domínio é o método preferencial. Estamos **não é recomendável** a executar o Microsoft Translator atrás de um IP filtrado firewall. A configuração está corrompida no futuro, sem aviso prévio.

## <a name="translator-ip-addresses"></a>Endereços IP do Microsoft Translator
Os endereços IP para api.cognitive.microsofttranslator.com - API de texto do Microsoft Translator a partir de 20 de Novembro de 2018:

* **Ásia-Pacífico:** 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99
* **América do Norte:** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Traduzir atrás de firewalls IP em sua chamada de API do Translator](reference/v3-0-translate.md)
