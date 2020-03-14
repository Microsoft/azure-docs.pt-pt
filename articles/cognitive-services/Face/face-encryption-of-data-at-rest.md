---
title: Encriptação de dados do serviço de rosto em repouso
titleSuffix: Azure Cognitive Services
description: Encriptação de dados do serviço de rosto em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372218"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Encriptação de dados do serviço de rosto em repouso

O serviço Face encripta automaticamente os seus dados quando persistia na nuvem. A encriptação do serviço Face protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só estão disponíveis no nível de preços E0. Para solicitar a capacidade de utilizar as chaves geridas pelo cliente, preencha e envie o Formulário de [Pedido de Chave Gerido](https://aka.ms/cogsvc-cmk)pelo Cliente gerido pelo cliente do Serviço Face . Levará aproximadamente 3-5 dias úteis para voltar a ouvir o estado do seu pedido. Dependendo da procura, pode ser colocado numa fila e aprovado à medida que o espaço se torna disponível. Uma vez aprovado para utilizar a CMK com o serviço Face, terá de criar um novo recurso Face e selecionar e0 como Nível de Preços. Assim que for criado o seu recurso Face com o nível de preços E0, pode utilizar o Cofre chave Azure para configurar a sua identidade gerida.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Passos seguintes

* [Formulário de pedido de chave gerido pelo cliente do serviço de rosto](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


