---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 3b975d9f0953c874c627e30b382ad8c7fa7cfc09
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307833"
---
## <a name="about-cognitive-services-encryption"></a>Sobre a encriptação dos Serviços Cognitivos

Os dados são encriptados e desencriptados utilizando encriptação AES de [140-2](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) em conformidade com o [FIPS.](https://en.wikipedia.org/wiki/FIPS_140-2) A encriptação e a desencriptação são transparentes, o que significa que a encriptação e o acesso são geridos para si. Os seus dados são seguros por padrão e não precisa de modificar o seu código ou aplicações para tirar partido da encriptação.

## <a name="about-encryption-key-management"></a>Sobre a gestão de chaves de encriptação

Por predefinição, a sua subscrição utiliza chaves de encriptação geridas pela Microsoft. No entanto, também pode gerir a sua subscrição com as suas próprias chaves de encriptação. As teclas geridas pelo cliente (CMK), também conhecidas como trazer a sua própria chave (BYOK), oferecem uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os seus dados.