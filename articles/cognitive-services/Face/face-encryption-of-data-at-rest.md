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
ms.openlocfilehash: 1e0275c91b2243132650be7af256071589091c4b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201934"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Encriptação de dados do serviço de rosto em repouso

O serviço Face encripta automaticamente os seus dados quando persistia na nuvem. A encriptação do serviço Face protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente só estão disponíveis no nível de preços E0. Para solicitar a capacidade de utilizar as chaves geridas pelo cliente, preencha e envie o Formulário de [Pedido de Chave Gerido](https://aka.ms/cogsvc-cmk)pelo Cliente gerido pelo cliente do Serviço Face . Levará aproximadamente 3-5 dias úteis para voltar a ouvir o estado do seu pedido. Dependendo da procura, pode ser colocado numa fila e aprovado à medida que o espaço se torna disponível. Uma vez aprovado para utilizar a CMK com o serviço Face, terá de criar um novo recurso Face e selecionar e0 como Nível de Preços. Assim que for criado o seu recurso Face com o nível de preços E0, pode utilizar o Cofre chave Azure para configurar a sua identidade gerida.

### <a name="regional-availability"></a>Disponibilidade regional

As chaves geridas pelo cliente estão atualmente disponíveis nestas regiões:

* E.U.A. Centro-Sul
* E.U.A.Oeste 2
* E.U.A. Leste
* US Gov - Virginia

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Próximos passos

* [Formulário de pedido de chave gerido pelo cliente do serviço de rosto](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


