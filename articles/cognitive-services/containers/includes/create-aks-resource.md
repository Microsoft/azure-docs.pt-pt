---
title: Criar um recurso de cluster azure Kubernetes Service
titleSuffix: Azure Cognitive Services
description: Aprenda a criar um recurso azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383449"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Criar um recurso de cluster azure Kubernetes Service

1. Vá ao [Serviço Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks)e selecione **Criar**.

1. No separador **Basics,** introduza as seguintes informações:

    |Definição|Valor|
    |--|--|
    |Subscrição|Selecione uma subscrição adequada.|
    |Grupo de recursos|Selecione um grupo de recursos disponíveis.|
    |Nome do cluster kubernetes|Introduza um nome (minúscula).|
    |Região|Selecione um local próximo.|
    |Versão Kubernetes|Qualquer valor marcado como **(padrão)**.|
    |Prefixo de nome DNS|Criado automaticamente, mas pode anular.|
    |Tamanho do nó|DS2 v2 padrão:<br>`2 vCPUs`, `7 GB`|
    |Contagem de nó|Deixe o deslizador pelo valor padrão.|

1. No separador **Escala,** deixe os nós virtuais e os **conjuntos** de **escala VM definidos** para os seus valores padrão.
1. No separador **Autenticação,** deixe o **diretor de serviço** e ative o **RBAC** definido nos seus valores predefinidos.
1. No separador **Networking,** introduza as seguintes seleções:

    |Definição|Valor|
    |--|--|
    |Encaminhamento de aplicações de HTTP|Não|
    |Configuração de rede|Básico|

1. No separador **Monitor,** certifique-se de que a **monitorização do recipiente ativação** está definida para **Sim**, e deixe o espaço de **trabalho do Log Analytics** como o valor predefinido.
1. No separador **Tags,** deixe os pares de nome/valor em branco por enquanto.
1. Selecione **Rever e Criar**.
1. Depois dos passes de validação, selecione **Criar**.

> [!NOTE]
> Se a validação falhar, pode ser devido a um erro de "diretor de serviço". Volte para o separador **Autenticação** e volte a **Rever + criar,** onde a validação deve ser executada e depois passar.
