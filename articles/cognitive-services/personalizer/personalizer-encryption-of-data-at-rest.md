---
title: Encriptação de serviço personalizado de dados em repouso
titleSuffix: Azure Cognitive Services
description: Encriptação de serviço personalizado de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 63526454bb366b214c27bddce24ed9ebc09556b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071091"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Encriptação de serviço personalizado de dados em repouso

O serviço Personalizer encripta automaticamente os seus dados quando persistia na nuvem. A encriptação do serviço Personalizer protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só estão disponíveis no nível de preços E0. Para solicitar a capacidade de utilizar as chaves geridas pelo cliente, preencha e envie o Formulário de [Pedido de Chave Gerido](https://aka.ms/cogsvc-cmk)pelo Cliente gerido pelo cliente do Serviço Personalizado . Levará aproximadamente 3-5 dias úteis para voltar a ouvir o estado do seu pedido. Dependendo da procura, pode ser colocado numa fila e aprovado à medida que o espaço se torna disponível. Uma vez aprovado para utilizar a CMK com o serviço Personalizer, terá de criar um novo recurso Personalizer e selecionar e0 como Nível de Preços. Assim que for criado o seu recurso Personalizer com o nível de preços E0, pode utilizar o Cofre de Chave Azure para configurar a sua identidade gerida.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Passos seguintes

* [Formulário de pedido de chave gerido pelo cliente gerido pelo cliente](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
