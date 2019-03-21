---
title: Ligar o Qlik Sense ao Azure Cosmos DB e visualize os seus dados
description: Este artigo descreve os passos necessários para ligar o Azure Cosmos DB para Qlik Sense e visualize os seus dados.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 10/22/2018
ms.reviewer: sngun
ms.openlocfilehash: 4532962b6fd9f40fad625ab000116e5a617682e5
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258788"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Ligar o Qlik Sense ao Azure Cosmos DB e visualize os seus dados

Qlik Sense é uma ferramenta de visualização de dados que combina dados de diferentes origens numa única vista. Qlik Sense indexa todas as relações possíveis nos seus dados para que pode obter informações imediatas aos dados. É possível visualizar dados do Azure Cosmos DB com o Qlik Sense. Este artigo descreve os passos necessários para ligar o Azure Cosmos DB para Qlik Sense e visualize os seus dados. 

> [!NOTE]
> Qlik Sense a ligar ao Azure Cosmos DB é atualmente suportada para a API de API do SQL e do Azure Cosmos DB para apenas as contas do MongoDB.

Pode ligar o Qlik Sense ao Azure Cosmos DB com:

* Cosmos DB SQL API com o conector do ODBC.

* API do Azure Cosmos DB para MongoDB ao utilizar o conector de Qlik sentido MongoDB (atualmente em pré-visualização).

* API do Azure Cosmos DB para MongoDB e API de SQL ao utilizar o conector de REST API no Qlik Sense.

* API cosmos DB Mongo DB ao utilizar o conector de gRPC para Qlik Core.
Este artigo descreve os detalhes da ligação para a API de SQL do Cosmos DB ao utilizar o conector do ODBC.

Este artigo descreve os detalhes da ligação para a API de SQL do Cosmos DB ao utilizar o conector do ODBC.

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções neste artigo, certifique-se de que tem os seguintes recursos prontos:

* Transfira o [Qlik sentido Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) ou configure a Qlik Sense no Azure por [instalar o item do marketplace Qlik Sense](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Transfira o [dados de vídeo Games](https://www.kaggle.com/gregorut/videogamesales), estes dados de exemplo estão num formato CSV. Irá armazenar estes dados numa conta do Cosmos DB e visualizá-los de Qlik Sense.

* Criar uma conta do Azure Cosmos DB SQL API com os passos descritos em [criar uma conta](create-sql-api-dotnet.md#create-account) secção do artigo guia de introdução.

* [Criar uma base de dados e uma coleção](create-sql-api-dotnet.md#create-collection-database) – pode usar definir o valor de débito da coleção para 1000 RU/s. 

* Carregar os dados de vendas de jogo de vídeo de exemplo para a sua conta do Cosmos DB. Pode importar os dados utilizando a ferramenta de migração de dados do Azure Cosmos DB, que pode fazer uma [sequenciais](import-data.md#SQLSeqTarget) ou uma [em massa importação](import-data.md#SQLBulkTarget) de dados. Demora cerca de 3 a 5 minutos até que os dados a importar para a conta do Cosmos DB.

* Transferir, instalar e configurar o controlador ODBC, utilizando os passos a [ligar ao Cosmos DB com o controlador ODBC](odbc-driver.md) artigo. Os dados de jogo de vídeo são um conjunto de dados simples e não tem de editar o esquema, utilize apenas o esquema de mapeamento de coleção padrão.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Ligar o Qlik Sense ao Cosmos DB

1. Abra o Qlik Sense e selecione **criar nova aplicação**. Forneça um nome para a sua aplicação e selecione **criar**.

   ![Criar uma nova aplicação de Qlik Sense](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. Depois da nova aplicação é criada com êxito, selecione **abrir uma aplicação** e escolha **adicionar dados a partir de ficheiros e de outras origens**. 

3. A partir de origens de dados, selecione **ODBC** para abrir a janela de configuração de ligação nova. 

4. Mude para o **utilizador DSN** e escolha a ligação de ODBC que criou anteriormente. Forneça um nome para a ligação e selecione **criar**. 

   ![Criar uma nova ligação](./media/visualize-qlik-sense/create-new-connection.png)

5. Depois de criar a ligação, pode escolher a base de dados, coleção onde os dados de jogo de vídeo estão localizados e, em seguida, pré-visualização.

   ![Escolha a base de dados e coleção](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Em seguida selecione **adicionar dados** para carregar os dados para Qlik Sense. Depois de carregar dados para Qlik Sense, pode gerar informações e fazer análises em dados. Pode utilizar as informações ou criar sua própria aplicação explorando as vendas de jogos de vídeo. A imagem seguinte mostra 

   ![Visualizar os dados](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Limitações ao ligar-se com o ODBC 

O cosmos DB é uma base de dados distribuída de esquema com drivers modelado ao redor de necessidades de desenvolvedor. O controlador ODBC exige uma base de dados com o esquema, inferir colunas, seus tipos de dados e outras propriedades. A consulta SQL normal ou a sintaxe DML com capacidade relacional não é aplicável ao Cosmos DB SQL API porque a API de SQL não está ANSI SQL. Por este motivo, as instruções SQL efetuadas por meio do controlador ODBC são convertidas em sintaxe SQL específico do Cosmos DB, que não tenha equivalentes para todas as construções. Para evitar estes problemas de tradução, tem de aplicar um esquema durante a configuração da ligação de ODBC. O [ligar com o controlador ODBC](odbc-driver.md) artigo dá-lhe sugestões e métodos para ajudar a configurar o esquema. Certifique-se criar esse mapeamento para cada base de dados/coleta para a conta do Cosmos DB.

## <a name="next-steps"></a>Próximos Passos

Se estiver a utilizar uma ferramenta de visualização diferente, como o Power BI, pode ligar à mesma ao utilizar as instruções no documento seguinte:

* [Visualizar dados do Cosmos DB ao utilizar o conector do Power BI](powerbi-visualize.md)
