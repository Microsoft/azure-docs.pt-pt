---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174982"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Implantar uma imagem com os termos do Marketplace

Algumas imagens de VM no Azure Marketplace têm licença adicional e termos de compra que você deve aceitar para poder implantá-las programaticamente.  

Para implantar uma VM de tal imagem, você precisará aceitar os termos da imagem e habilitar a implantação programática. Você só precisará fazer isso uma vez por assinatura. Posteriormente, sempre que você implantar uma VM programaticamente a partir da imagem, também precisará especificar os parâmetros do *plano de compra* .

As seções a seguir mostram como:

* Descubra se uma imagem do Marketplace tem termos de licença adicionais 
* Aceite os termos programaticamente
* Fornecer parâmetros de plano de compra ao implantar uma VM programaticamente

