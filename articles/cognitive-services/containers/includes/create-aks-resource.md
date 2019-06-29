---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como criar um kubernetes do azure resource (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1d68c08f6dfca74c38973af1686d614f3f10cc28
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455121"
---
## <a name="create-an-azure-kubernetes-service-aks-cluster-resource"></a>Criar um recurso de cluster do Azure Kubernetes Service (AKS)

1. Vá para o [criar](https://ms.portal.azure.com/#create/microsoft.aks) para serviços do Kubernetes.

1. Sobre o **Noções básicas** separador, introduza os seguintes detalhes:

    |Definição|Valor|
    |--|--|
    |Subscrição|Selecione a subscrição adequada|
    |Grupo de recursos|Selecione um grupo de recursos disponíveis|
    |Nome do cluster de Kubernetes|Nome desejado (minúscula)|
    |Região|Selecione uma localização próximas|
    |Versão do Kubernetes|1.12.8 (predefinição)|
    |Prefixo de nome DNS|Criado automaticamente, mas, opcionalmente, pode substituir|
    |Tamanho do nó|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Contagem de nós|Deixe o controlo de deslize com o valor predefinido|

1. Na **escala** separador, deixe a *nós virtuais* e *conjuntos de dimensionamento de máquinas virtuais* valores predefinidos.
1. Sobre o **autenticação** separador, deixe *principal de serviço* e *ativar RBAC* valores predefinidos.
1. Sobre o **redes** separador, introduza as seguintes seleções:

    |Definição|Value|
    |--|--|
    |Encaminhamento de aplicações de HTTP|Não|
    |Configuração de rede|Básica|

1. Na **monitorização** separador, certifique-se de que *ativar a monitorização do contentor* está definida como **Sim** e deixe o *área de trabalho do Log Analytics* como seu valor predefinido
1. Sobre o **etiquetas** separador, deixe os pares nome/valor em branco por agora.
1. Clique em **reveja e criar**.
1. Depois de passar a validação, clique em **criar**.

> [!NOTE]
> Se a validação falhar, podem ser devido uma *principal de serviço* erro. Navegue de volta para o *autenticação* separador e, novamente para o *rever + criar* onde validação deve executar e, em seguida, passar.
