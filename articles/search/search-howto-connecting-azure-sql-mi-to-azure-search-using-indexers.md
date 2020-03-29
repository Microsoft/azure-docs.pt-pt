---
title: Ligação Azure SQL Managed Instance para indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Ativar o ponto final público para permitir ligações a Instâncias Geridas SQL a partir de um indexador em Pesquisa Cognitiva Azure.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964894"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Configure uma ligação de um indexador de pesquisa cognitiva Azure à Instância Gerida SQL

Como notado na [Connecting Azure SQL Database to Azure Cognitive Search utilizando indexers,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)a criação de indexantes contra **instâncias geridas sQL** é suportada pela Azure Cognitive Search através do ponto final público.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Criar a Instância Gerida Azure SQL com ponto final público
Crie uma Instância Gerida SQL com a opção **Enable public point** selecionada.

   ![Ativar o ponto final do público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Ativar o ponto final do público")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Enable Azure SQL Managed Instance ponto final público
Também pode ativar um ponto final público numa instância gerida sQL existente no âmbito da**rede** > de **segurança** > Virtual**Public point** > **Enable**.

   ![Ativar o ponto final do público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Ativar o ponto final do público")

## <a name="verify-nsg-rules"></a>Verificar as regras do NSG
Verifique se o Grupo de Segurança da Rede tem as regras de segurança de **entrada** corretas que permitem ligações dos serviços Azure.

   ![Regra de segurança de entrada nsg](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Regra de segurança de entrada nsg")

> [!NOTE]
> Os indexadores ainda exigem que a Instância Gerida sQL seja configurada com um ponto final público para ler dados.
> No entanto, pode optar por restringir o acesso de entrada a`public_endpoint_inbound`esse ponto final público substituindo a regra atual () pelas seguintes 2 regras:
>
> * Permitir o acesso à `AzureCognitiveSearch` entrada a partir `AzureCognitiveSearch`da etiqueta de `cognitive_search_inbound` [serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) ("SOURCE" = , "NAME" = )
>
> * Permitindo o acesso à entrada a partir do endereço IP do serviço de pesquisa, que `<your-search-service-name>.search.windows.net`pode ser obtido através da sua denominação de domínio totalmente qualificada (por exemplo, ). ("SOURCE" `IP address`= , "NAME" = `search_service_inbound`)
>
> Para cada uma dessas 2 regras, deset "PORT" = `3342`, "PROTOCOL" = `TCP`, "DESTINATION" = `Any`, "ACTION" =`Allow`

## <a name="get-public-endpoint-connection-string"></a>Obtenha cadeia de conexão de ponto final público
Certifique-se de que utiliza a corda de ligação para o **ponto final público** (porta 3342, não porta 1433).

   ![Cadeia de ligação de ponto final público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Cadeia de ligação de ponto final público")

## <a name="next-steps"></a>Passos seguintes
Com a configuração fora do caminho, pode agora especificar um Caso Gerido SQL como a fonte de dados de um indexador de pesquisa cognitiva Azure usando o portal ou a API REST. Consulte [a Connecting Azure SQL Database à Azure Cognitive Search utilizando os indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.
