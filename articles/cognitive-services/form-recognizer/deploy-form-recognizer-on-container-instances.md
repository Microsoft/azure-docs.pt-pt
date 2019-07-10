---
title: Executar as instâncias de contentor do Azure
titleSuffix: Azure Cognitive Services
description: Implementar o contentor do reconhecedor de formulário para uma instância de contentor do Azure e testá-la num navegador da web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c424465678a9989940d92910c5d288fa2fb1cab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711397"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Implementar o contentor do reconhecedor de formulário para o Azure Container Instances

Saiba como implementar os serviços cognitivos [formulário reconhecedor](form-recognizer-container-howto.md) contentor para o Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Este procedimento demonstra a criação de um recurso do reconhecedor de formulário do Azure. Em seguida, vamos abordar extrair a imagem de contentor associado. Por fim, podemos destacar a capacidade de exercer a orquestração dos dois num browser. Através de contentores podem deslocar a atenção dos desenvolvedores evitar o gerenciamento de infra-estrutura para em vez disso, com foco no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Pedir acesso ao registo de contentor

Primeiro tem de concluir e submeter o [formulário de pedido de acesso a contentores do reconhecedor de formulário de serviços cognitivos](https://aka.ms/FormRecognizerRequestAccess) para pedir acesso ao contentor. Se o fizer, também assina tendo em vista o de imagem digitalizada. Não precisa para se inscrever para o formulário de pedido de imagem digitalizada separadamente. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
