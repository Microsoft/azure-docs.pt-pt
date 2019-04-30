---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows, virtual-machines-linux
author: rockboyfor
ms.service: multiple
ms.topic: include
origin.date: 10/09/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880562"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Implementar uma imagem com os termos do Marketplace

Algumas imagens VM no Azure Marketplace têm adicionais de licença e que têm de aceitar antes de poder implementá-las por meio de programação de termos de compra.  

Para implementar uma VM a partir de uma imagem desse tipo, terá de aceitar os termos da imagem e ativar a implementação programática. Só precisará fazer isto vez por subscrição. Depois disso, sempre que implementar uma VM através de programação da imagem também terá de especificar *compra do plano* parâmetros.

As secções seguintes mostram como:

* Descubra se a uma imagem do Marketplace tem termos de licenciamento adicionais 
* Aceite os termos de forma programática
* Fornecer os parâmetros de plano de compra ao implementar uma VM através de programação

<!-- Update_Description: update meta properties, wording update -->