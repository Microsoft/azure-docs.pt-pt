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
ms.openlocfilehash: b660d3a0d49de80ed85cfbdcdf8e28b9828cbf26
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91544999"
---
- Apenas [o software e as teclas HSM RSA](../articles/key-vault/keys/about-keys.md) dos tamanhos de 2.048 bits, 3.072 bits e 4.096 bits são suportadas, sem outras chaves ou tamanhos.
    - As chaves [HSM](../articles/key-vault/keys/hsm-protected-keys.md) requerem o nível **premium** dos cofres da Chave Azure.
- Os discos criados a partir de imagens personalizadas que são encriptadas utilizando encriptação do lado do servidor e chaves geridas pelo cliente devem ser encriptados usando as mesmas chaves geridas pelo cliente e devem estar na mesma subscrição.
- As imagens criadas a partir de discos encriptados com encriptação do lado do servidor e chaves geridas pelo cliente devem ser encriptadas com as mesmas teclas geridas pelo cliente.
- Todos os recursos relacionados com as chaves geridas pelo cliente (Cofres chave Azure, conjuntos de encriptação de discos, VMs, discos e instantâneos) devem estar na mesma subscrição e região.
- Discos, instantâneos e imagens encriptadas com chaves geridas pelo cliente não podem mover-se para outro grupo de recursos e subscrição.
- Os discos geridos atualmente ou previamente encriptados utilizando a Encriptação do Disco Azure não podem ser encriptados utilizando chaves geridas pelo cliente.
- Só pode criar até 50 conjuntos de encriptação de disco por região por subscrição.
- Para obter informações sobre a utilização de chaves geridas pelo cliente com galerias de imagens partilhadas, consulte [Preview: Utilize chaves geridas pelo cliente para encriptar imagens](../articles/virtual-machines/image-version-encryption.md).
