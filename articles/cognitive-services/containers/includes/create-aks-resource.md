---
title: Criar um recurso de cluster do serviço Kubernetes do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso do Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: ab7ce8b4a538e6a286a00285069a22878c5d88d9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877444"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Criar um recurso de cluster do serviço Kubernetes do Azure

1. Aceda a [do Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks) e selecione **criar**.

1. Sobre o **Noções básicas** separador, introduza as seguintes informações:

    |Definição|Valor|
    |--|--|
    |Subscription|Selecione uma subscrição adequada|
    |Resource group|Selecione um grupo de recursos disponíveis|
    |Nome do cluster de Kubernetes|Introduza um nome (em minúsculas)|
    |Região|Selecione uma localização próximas|
    |Versão do Kubernetes|1.12.8 (predefinição)|
    |Prefixo de nome DNS|Criado automaticamente, mas pode substituir|
    |Tamanho do nó|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Contagem de nós|Deixe o controlo de deslize com o valor predefinido|

1. Na **escala** separador, deixe **nós virtuais** e **conjuntos de dimensionamento de Máquina Virtual (pré-visualização)** definido para os valores predefinidos.
1. Na **autenticação** separador, deixe **principal de serviço** e **ativar RBAC** definido para os valores predefinidos.
1. Sobre o **redes** separador, introduza as seguintes seleções:

    |Definição|Value|
    |--|--|
    |Encaminhamento de aplicações de HTTP|Não|
    |Configuração de rede|Básica|

1. Na **monitorização** separador, certifique-se de que **ativar a monitorização do contentor** está definida como **Sim**e deixe **área de trabalho do Log Analytics** como o valor predefinido.
1. Sobre o **etiquetas** separador, deixe os pares nome/valor em branco por agora.
1. Selecione **reveja e criar**.
1. Depois de passar a validação, selecione **criar**.

> [!NOTE]
> Se a validação falhar, pode ser devido um erro de "Principal de serviço". Volte para o **autenticação** separador e, em seguida, voltar ao **revisão + criar**, onde validação deve executar e, em seguida, passar.
