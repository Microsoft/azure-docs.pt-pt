---
title: Alterações ao cluster de acesso de configuração - Azure HDInsight
description: Saiba mais sobre as alterações para aceder aos campos de configuração de cluster confidenciais.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610246"
---
# <a name="changes-to-cluster-configuration-access"></a>Alterações para o acesso de configuração de cluster

A versão mais recente do Azure HDInsight SDK traz algumas alterações importantes para dar suporte a mais acesso baseado em funções para obter informações confidenciais detalhado. Como parte de um destes for alterado, alguns **ação poderá ser necessária** se estiver a utilizar um dos métodos afetados.

## <a name="sdk-for-net-500"></a>SDK para .NET 5.0.0

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) serão **deixar de devolver os parâmetros com diferenciação** , como chaves de armazenamento (site principal) ou credenciais HTTP (gateway).
    - Para obter todas as configurações, incluindo os parâmetros com diferenciação, utilize `ConfigurationOperationsExtensions.List` daqui em diante.  Tenha em atenção que os utilizadores com a função de "Reader" não conseguir utilizar este método. Isso permite um controlo granular sobre o qual os utilizadores podem aceder a informações confidenciais relativas a um cluster. 
    - Para obter credenciais de gateway HTTP apenas, utilize `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) foi agora preterida e foi substituído por `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) e [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) foram preteridas. Está agora sempre ativado HTTP, para que esses métodos já não são necessários.