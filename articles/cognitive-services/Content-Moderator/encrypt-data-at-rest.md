---
title: Encriptação do Moderador de Conteúdo dos dados em repouso
titleSuffix: Azure Cognitive Services
description: Encriptação do Moderador de Conteúdo dos dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 1401108a594e30790e842ec379724603f11d493f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100524551"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Encriptação do Moderador de Conteúdo dos dados em repouso

O Moderador de Conteúdo encripta automaticamente os seus dados quando estes são percamados na nuvem, ajudando a cumprir os seus objetivos de segurança organizacional e conformidade.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só estão disponíveis no nível de preços E0. Para solicitar a capacidade de utilizar chaves geridas pelo cliente, preencha e envie o [Formulário de Pedido de Pedido de Customer-Managed Chave do Moderador](https://aka.ms/cogsvc-cmk)de Conteúdo . Levará aproximadamente 3 a 5 dias úteis para ouvir de volta o estado do seu pedido. Dependendo da procura, você pode ser colocado em uma fila e aprovado à medida que o espaço fica disponível. Uma vez aprovado para a utilização da CMK com o serviço Desematado de Conteúdo, terá de criar um novo recurso de Moderador de Conteúdo e selecionar o E0 como O Nível de Preços. Uma vez criado o recurso do Seu Moderador de Conteúdo com o nível de preços E0, pode utilizar o Azure Key Vault para configurar a sua identidade gerida.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Ativar a encriptação de dados para a sua Equipa moderadora de conteúdo

Para ativar a encriptação de dados para a sua Equipa de Avaliação de Moderadores de Conteúdo, consulte o [Quickstart: Experimente o Moderador de Conteúdo na web](quick-start.md#create-a-review-team).  

> [!NOTE]
> Terá de fornecer um _ID de recursos_ com o nível de preços do Moderador de Conteúdo E0.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma lista completa de serviços que suportam a CMK, consulte [chaves geridas pelo cliente para serviços cognitivos](../encryption/cognitive-services-encryption-keys-portal.md)
* [O que é Azure Key Vault?](../../key-vault/general/overview.md)
* [Formulário de pedido chave de Customer-Managed serviços cognitivos](https://aka.ms/cogsvc-cmk)