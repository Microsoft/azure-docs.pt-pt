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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71174982"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Implementar uma imagem com termos de Marketplace

Algumas imagens VM no Azure Marketplace têm licença e termos de compra adicionais que deve aceitar antes de as implementar programáticamente.  

Para implementar um VM a partir de tal imagem, terá de aceitar os termos da imagem e permitir a implementação programática. Só precisa de fazer isto uma vez por subscrição. Depois, cada vez que implementar um VM programáticamente a partir da imagem, também terá de especificar os parâmetros do plano de *compra.*

As seguintes secções mostram como:

* Descubra se uma imagem do Marketplace tem termos de licença adicionais 
* Aceite os termos programáticamente
* Forneça parâmetros do plano de compra quando implementar um VM programáticamente

