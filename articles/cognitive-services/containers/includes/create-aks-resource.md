---
title: Criar um recurso de cluster do serviço kubernetes do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de AKS (serviço de kubernetes do Azure).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383449"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Criar um recurso de cluster do serviço kubernetes do Azure

1. Vá para o [serviço kubernetes do Azure](https://ms.portal.azure.com/#create/microsoft.aks)e selecione **criar**.

1. Na guia **noções básicas** , insira as seguintes informações:

    |Definição|Valor|
    |--|--|
    |Subscrição|Selecione uma subscrição adequada.|
    |Grupo de recursos|Selecione um grupo de recursos disponível.|
    |Nome do cluster kubernetes|Insira um nome (em minúsculas).|
    |Região|Selecione um local próximo.|
    |Versão do kubernetes|Qualquer que seja o valor marcado como **(padrão)** .|
    |Prefixo do nome DNS|Criado automaticamente, mas você pode substituir.|
    |Tamanho do nó|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Contagem de nós|Deixe o controle deslizante no valor padrão.|

1. Na guia **escala** , deixe **nós virtuais** e **conjuntos de dimensionamento de VM** definidos com seus valores padrão.
1. Na guia **autenticação** , deixe a **entidade de serviço** e **habilite o RBAC** definido com seus valores padrão.
1. Na guia **rede** , insira as seguintes seleções:

    |Definição|Valor|
    |--|--|
    |Encaminhamento de aplicações de HTTP|Não|
    |Configuração de rede|Básica|

1. Na guia **monitoramento** , verifique se habilitar o **monitoramento de contêiner** está definido como **Sim**e deixe **log Analytics espaço de trabalho** como o valor padrão.
1. Na guia **marcas** , deixe os pares nome/valor em branco por enquanto.
1. Selecione **revisar e criar**.
1. Depois que a validação for aprovada, selecione **criar**.

> [!NOTE]
> Se a validação falhar, isso pode ser devido a um erro de "entidade de serviço". Volte para a guia **autenticação** e, em seguida, volte para **revisar + criar**, em que a validação deve ser executada e, em seguida, passada.
