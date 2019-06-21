---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184251"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Implementar uma imagem com os termos do Marketplace

Algumas imagens VM no Azure Marketplace têm adicionais de licença e que têm de aceitar antes de poder implementá-las por meio de programação de termos de compra.  

Para implementar uma VM a partir de uma imagem desse tipo, terá de aceitar os termos da imagem e ativar a implementação programática. Só precisará fazer isto vez por subscrição. Depois disso, sempre que implementar uma VM através de programação da imagem também terá de especificar *compra do plano* parâmetros.

As secções seguintes mostram como:

* Descubra se a uma imagem do Marketplace tem termos de licenciamento adicionais 
* Aceite os termos de forma programática
* Fornecer os parâmetros de plano de compra ao implementar uma VM através de programação

