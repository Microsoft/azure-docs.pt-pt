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
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372174"
---
## <a name="about-cognitive-services-encryption"></a>Sobre a encriptação dos Serviços Cognitivos

Os dados são encriptados e desencriptados utilizando encriptação [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) compatível com [140-2](https://en.wikipedia.org/wiki/FIPS_140-2) bits. A encriptação e a desencriptação são transparentes, o que significa que a encriptação e o acesso são geridos para si. Os seus dados são seguros por padrão e não precisa de modificar o seu código ou aplicações para tirar partido da encriptação.

## <a name="about-encryption-key-management"></a>Sobre a gestão da chave de encriptação

Por padrão, a sua subscrição utiliza chaves de encriptação geridas pela Microsoft. Se estiver a utilizar um nível de preços que suporta as chaves geridas pelo Cliente, pode ver as definições de encriptação do seu recurso na secção de **encriptação** do [portal Azure,](https://portal.azure.com)como mostra a imagem seguinte.

![Ver definições de encriptação](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Para subscrições que suportam apenas chaves de encriptação geridas pela Microsoft, não terá uma secção de **Encriptação.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves geridas pelo cliente com o Azure Key Vault

Existe também uma opção para gerir a sua subscrição com as suas próprias chaves. As chaves geridas pelo cliente (CMK), também conhecidacomo Bring your own key (BYOK), oferecem maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os seus dados.
