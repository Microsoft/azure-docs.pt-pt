---
title: Criar um recurso de cluster de serviço Azure Kubernetes
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso Azure Kubernetes Service (AKS).
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e7f5b6f3685a94b5497784360f8f12b22fb95012
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017889"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Criar um recurso de cluster de serviço Azure Kubernetes

1. Vá ao [Serviço Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks)e selecione **Criar**.

1. No separador **Informações Básicas**, introduza as seguintes informações:

    |Definição|Valor|
    |--|--|
    |Subscrição|Selecione uma subscrição adequada.|
    |Grupo de recursos|Selecione um grupo de recursos disponível.|
    |Nome do cluster Kubernetes|Introduza um nome (minúscula).|
    |Region|Selecione uma localização próxima.|
    |Versão Kubernetes|Qualquer valor é marcado como **(padrão)**.|
    |Prefixo do nome DNS|Criado automaticamente, mas pode ser sobrepõe-se.|
    |Tamanho do nó|DS2 v2 padrão:<br>`2 vCPUs`, `7 GB`|
    |Contagem de nós|Deixe o deslizador pelo valor predefinido.|

1. No separador **agrupamento de piscinas Nó,** deixe os nós virtuais e **os conjuntos de escala VM** **definidos** para os seus valores predefinidos.
1. No separador **Autenticação,** deixe **o principal de serviço** e ative o **RBAC** definido para os seus valores predefinidos.
1. No **separador Networking,** insira as seguintes seleções:

    |Definição|Valor|
    |--|--|
    |Encaminhamento de aplicações de HTTP|Não|
    |Configuração da rede|Básico|

1. No separador **Integrações,** certifique-se de que **a monitorização do contentor** está definida como **Ativada**, e deixe o **espaço de trabalho do Log Analytics** como o valor predefinido.
1. No **separador Tags,** deixe os pares de nome/valor em branco por enquanto.
1. Selecione **'Rever e criar' ( Revisão e Criação).**
1. Após os passes de validação, **selecione Criar**.

> [!NOTE]
> Se a validação falhar, pode ser devido a um erro de "Diretor de Serviço". Volte para o **separador Autenticação** e volte a **rever + criar,** onde a validação deve ser executada e depois passar.
