---
title: Encriptação moderadora de conteúdo de dados em repouso
titleSuffix: Azure Cognitive Services
description: Encriptação moderadora de conteúdo de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372163"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Encriptação moderadora de conteúdo de dados em repouso

O Moderador de Conteúdo encripta automaticamente os seus dados quando é persistido na nuvem, ajudando a cumprir os seus objetivos de segurança organizacional e conformidade.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só estão disponíveis no nível de preços E0. Para solicitar a capacidade de utilizar as chaves geridas pelo cliente, preencha e envie o Formulário de [Pedido de Chave Gerido](https://aka.ms/cogsvc-cmk)pelo Cliente do Moderador de Conteúdo . Levará aproximadamente 3-5 dias úteis para voltar a ouvir o estado do seu pedido. Dependendo da procura, pode ser colocado numa fila e aprovado à medida que o espaço se torna disponível. Uma vez aprovado para utilizar a CMK com o serviço moderador de conteúdo, terá de criar um novo recurso moderador de conteúdo e selecionar E0 como Nível de Preços. Assim que for criado o seu recurso Moderador de Conteúdo com o nível de preços E0, pode utilizar o Cofre de Chave Azure para configurar a sua identidade gerida.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Ativar encriptação de dados para a sua Equipa moderadora de conteúdo

Para ativar a encriptação de dados para a sua Equipa de Revisão de Moderadorde conteúdo, consulte o [Quickstart: Experimente](quick-start.md#create-a-review-team)o Moderador de Conteúdo na web .  

> [!NOTE]
> Você precisará fornecer um ID de _recursos_ com o nível de preços Moderador de Conteúdo E0.


## <a name="next-steps"></a>Passos seguintes

* [Formulário de pedido de chave gerido pelo cliente por moderador de conteúdo](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
