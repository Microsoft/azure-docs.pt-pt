---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "71174982"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Implementar uma imagem com termos de Marketplace

Algumas imagens VM no Azure Marketplace têm licença e termos de compra adicionais que deve aceitar antes de poder implantá-las programáticamente.  

Para implementar um VM a partir de tal imagem, terá de aceitar os termos da imagem e ativar a implementação programática. Só terá de o fazer uma vez por subscrição. Depois, cada vez que implementar um VM programáticamente a partir da imagem, também terá de especificar os parâmetros do *plano de compra.*

As seguintes secções mostram como:

* Descubra se uma imagem do Marketplace tem termos de licença adicionais 
* Aceite os termos programáticamente
* Forneça parâmetros de plano de compra quando implementar um VM programáticamente

