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
ms.openlocfilehash: 90d5aae559a317ed509d04c8db3310ff8a5de026
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "89069920"
---
## <a name="about-cognitive-services-encryption"></a>Sobre a encriptação dos Serviços Cognitivos

Os dados são encriptados e desencriptados utilizando encriptação AES de [140-2](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) em conformidade com o [FIPS.](https://en.wikipedia.org/wiki/FIPS_140-2) A encriptação e a desencriptação são transparentes, o que significa que a encriptação e o acesso são geridos para si. Os dados estão protegidos por predefinição e não precisa de modificar o código ou as aplicações para tirar partido da encriptação.

## <a name="about-encryption-key-management"></a>Sobre a gestão de chaves de encriptação

Por predefinição, a subscrição utiliza chaves de encriptação geridas pela Microsoft. Existe também a opção de gerir a sua subscrição com as suas próprias chaves chamadas chaves geridas pelo cliente (CMK). A CMK oferece uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os dados. Se a CMK estiver configurada para a sua subscrição, é fornecida uma dupla encriptação, que oferece uma segunda camada de proteção, permitindo ao mesmo tempo controlar a chave de encriptação através do seu Cofre de Chave Azure.