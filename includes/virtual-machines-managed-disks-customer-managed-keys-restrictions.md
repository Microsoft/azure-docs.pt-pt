---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 75acbb30c2bf811b7ae72d6939b9f164554fdd32
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98859535"
---
- Apenas [o software e as teclas HSM RSA](../articles/key-vault/keys/about-keys.md) dos tamanhos de 2.048 bits, 3.072 bits e 4.096 bits são suportadas, sem outras chaves ou tamanhos.
    - As chaves [HSM](../articles/key-vault/keys/hsm-protected-keys.md) requerem o nível **premium** dos cofres da Chave Azure.
- Os discos criados a partir de imagens personalizadas que são encriptadas utilizando encriptação do lado do servidor e chaves geridas pelo cliente devem ser encriptados usando as mesmas chaves geridas pelo cliente e devem estar na mesma subscrição.
- As imagens criadas a partir de discos encriptados com encriptação do lado do servidor e chaves geridas pelo cliente devem ser encriptadas com as mesmas teclas geridas pelo cliente.
- Todos os recursos relacionados com as chaves geridas pelo cliente (Cofres chave Azure, conjuntos de encriptação de discos, VMs, discos e instantâneos) devem estar na mesma subscrição e região.
- Discos, instantâneos e imagens encriptadas com chaves geridas pelo cliente não podem mover-se para outro grupo de recursos e subscrição.
- Os discos geridos atualmente ou previamente encriptados utilizando a Encriptação do Disco Azure não podem ser encriptados utilizando chaves geridas pelo cliente.
- Só pode criar até 1000 conjuntos de encriptação de disco por região por subscrição.
- Para obter informações sobre a utilização de chaves geridas pelo cliente com galerias de imagens partilhadas, consulte [Preview: Utilize chaves geridas pelo cliente para encriptar imagens](../articles/virtual-machines/image-version-encryption.md).
