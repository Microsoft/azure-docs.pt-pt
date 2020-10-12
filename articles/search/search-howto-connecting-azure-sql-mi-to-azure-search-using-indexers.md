---
title: Ligação de instância gerida Azure SQL para indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Ativar o ponto final público para permitir ligações a SQL Managed Instances a partir de um indexante na Pesquisa Cognitiva Azure.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9e8625724f67caac99ae799674f9db9399e11ad8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89294259"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Configure uma ligação de um indexante de pesquisa cognitiva Azure para SQL Gerenciada Instância

Como notado na [Connecting Azure SQL Database to Azure Cognitive Search usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), criar indexadores contra **SQL Managed Instances** é suportado pela Azure Cognitive Search através do ponto final público.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Criar exemplo gerido Azure SQL com ponto final público
Crie uma sql Managed Instance com a opção **de ponto final público Enable** selecionada.

   ![Permitir o ponto final público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Permitir o ponto final público")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Ativar o ponto final público da Instância Gerida Azure SQL
Também pode ativar o ponto final público numa ocorrência gerida pelo SQL existente na rede virtual **de segurança**  >  **Virtual network**  >  **Public endpoint**  >  **Ative**.

   ![Permitir o ponto final público utilizando o VNET de instância gerida](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Permitir o ponto final público")

## <a name="verify-nsg-rules"></a>Verificar as regras do NSG
Verifique se o Grupo de Segurança da Rede dispõe das **regras de segurança de entrada** corretas que permitem ligações a partir dos serviços Azure.

   ![Regra de segurança de entrada NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Regra de segurança de entrada NSG")

> [!NOTE]
> Os indexantes ainda exigem que a SQL Managed Instance seja configurada com um ponto final público para ler dados.
> No entanto, pode optar por restringir o acesso à entrada a esse ponto final público substituindo a regra atual ( `public_endpoint_inbound` ) com as seguintes 2 regras:
>
> * Permitindo o acesso à entrada a partir da etiqueta de `AzureCognitiveSearch` [serviço](../virtual-network/service-tags-overview.md#available-service-tags) ("SOURCE" = `AzureCognitiveSearch` , "NAME" = `cognitive_search_inbound` )
>
> * Permitindo o acesso à entrada a partir do endereço IP do serviço de pesquisa, que pode ser obtido através da sua denominação de domínio totalmente qualificada (por exemplo, `<your-search-service-name>.search.windows.net` ). ("FONTE" = `IP address` "NOME" = `search_service_inbound` )
>
> Para cada uma dessas 2 regras, definir "PORT" = `3342` , "PROTOCOLO" = `TCP` , "DESTINO" = `Any` " AÇÃO" = `Allow`

## <a name="get-public-endpoint-connection-string"></a>Obtenha a cadeia de ligação de ponto final público
Certifique-se de que utiliza a cadeia de ligação para o **ponto final público** (porta 3342, não para a porta 1433).

   ![Cadeia de ligação de ponto final público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Cadeia de ligação de ponto final público")

## <a name="next-steps"></a>Passos seguintes
Com a configuração fora do caminho, pode agora especificar uma SqL Managed Instance como a fonte de dados para um indexante de Pesquisa Cognitiva Azure usando o portal ou a API REST. Consulte [a Base de Dados Azure SQL de Ligação à Pesquisa Cognitiva Azure utilizando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.