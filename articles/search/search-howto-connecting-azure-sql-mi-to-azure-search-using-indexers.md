---
title: Conexão do SQL Instância Gerenciada do Azure para indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite o ponto de extremidade público para permitir conexões com instâncias gerenciadas do SQL de um indexador no Azure Pesquisa Cognitiva.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 16daf4a79252134703715ccd88f0b10dda7f4fa6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792162"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Configurar uma conexão de um indexador Pesquisa Cognitiva do Azure para o SQL Instância Gerenciada

Conforme observado na [conexão do banco de dados SQL do Azure ao azure pesquisa cognitiva usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), a criação de indexadores em **instâncias gerenciadas do SQL** é suportada pelo Azure pesquisa cognitiva por meio do ponto de extremidade público.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Criar Instância Gerenciada do SQL do Azure com ponto de extremidade público
Crie um Instância Gerenciada SQL com a opção **habilitar ponto de extremidade público** selecionada.

   ![Habilitar ponto de extremidade público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Habilitar ponto de extremidade público")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Habilitar o ponto de extremidade público do Azure SQL Instância Gerenciada
Você também pode habilitar o ponto de extremidade público em um SQL Instância Gerenciada existente em **segurança** > **rede virtual** > **ponto de extremidade público** > **habilitar**.

   ![Habilitar ponto de extremidade público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Habilitar ponto de extremidade público")

## <a name="verify-nsg-rules"></a>Verificar regras do NSG
Verifique se o grupo de segurança de rede tem as **regras de segurança de entrada** corretas que permitem conexões de serviços do Azure.

   ![Regra de segurança de entrada do NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Regra de segurança de entrada do NSG")

## <a name="get-public-endpoint-connection-string"></a>Obter cadeia de conexão de ponto de extremidade público
Certifique-se de usar a cadeia de conexão para o **ponto de extremidade público** (porta 3342, não porta 1433).

   ![Cadeia de conexão de ponto de extremidade público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Cadeia de conexão de ponto de extremidade público")

## <a name="next-steps"></a>Passos seguintes
Com a configuração fora do caminho, agora você pode especificar um SQL Instância Gerenciada como a fonte de dados para um indexador de Pesquisa Cognitiva do Azure usando o portal ou a API REST. Consulte [conectando o banco de dados SQL do Azure ao azure pesquisa cognitiva usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.
