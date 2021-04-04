---
title: Ligue o Qlik Sense ao Azure Cosmos DB e visualize os seus dados
description: Este artigo descreve os passos necessários para ligar a Azure Cosmos DB à Qlik Sense e visualizar os seus dados.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 2401786a82b6a3e5bf6c6a893a8e7cd3656f3402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996967"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Ligue o Qlik Sense ao Azure Cosmos DB e visualize os seus dados
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Qlik Sense é uma ferramenta de visualização de dados que combina dados de diferentes fontes numa única visão. O Qlik Sense indexa todas as relações possíveis nos seus dados para que possa obter informações imediatas sobre os dados. Pode visualizar os dados DB do Azure Cosmos utilizando o Qlik Sense. Este artigo descreve os passos necessários para ligar a Azure Cosmos DB à Qlik Sense e visualizar os seus dados. 

> [!NOTE]
> A ligação entre qlik Sense e Azure Cosmos DB é atualmente suportada para API SQL e API da Azure Cosmos DB apenas para contas MongoDB.

Pode ligar qlik Sense a Azure Cosmos DB com:

* Cosmos DB SQL API utilizando o conector ODBC.

* A API da Azure Cosmos DB para a MongoDB utilizando o conector Qlik Sense MongoDB (atualmente em pré-visualização).

* A API da Azure Cosmos DB para a MongoDB e a SQL API utilizando o conector REST API em Qlik Sense.

* Cosmos DB Mongo DB API utilizando o conector gRPC para Qlik Core.
Este artigo descreve os detalhes da ligação ao Cosmos DB SQL API utilizando o conector ODBC.

Este artigo descreve os detalhes da ligação ao Cosmos DB SQL API utilizando o conector ODBC.

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, certifique-se de que tem os seguintes recursos prontos:

* Descarregue o [Qlik Sense Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) ou instale o Qlik Sense in Azure [instalando o item de mercado Qlik Sense](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Descarregue os dados dos [videojogos,](https://www.kaggle.com/gregorut/videogamesales)este dado da amostra está no formato CSV. Você irá armazenar estes dados numa conta de Cosmos DB e visualizá-lo em Qlik Sense.

* Crie uma conta Azure Cosmos DB SQL API utilizando os passos descritos na [criação](create-sql-api-dotnet.md#create-account) de uma secção de conta do artigo quickstart.

* [Criar uma base de dados e uma coleção](create-sql-api-java.md#add-a-container) – Pode utilizar o valor de produção de recolha para 1000 RU/s. 

* Carregue os dados de vendas de jogos de vídeo na sua conta Cosmos DB. Pode importar os dados utilizando a ferramenta de migração de dados Azure Cosmos DB, pode fazer uma importação [sequencial](import-data.md#SQLSeqTarget) ou a [granel](import-data.md#SQLBulkTarget) de dados. Leva cerca de 3 a 5 minutos para os dados importarem para a conta cosmos DB.

* Descarregue, instale e configuure o controlador ODBC utilizando os passos na [ligação ao Cosmos DB com](odbc-driver.md) o artigo do controlador ODBC. Os dados dos videojogos são um simples conjunto de dados e não é preciso editar o esquema, basta usar o esquema de mapeamento de recolha padrão.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Ligue qlik Sense a Cosmos DB

1. Abra o Qlik Sense e selecione **Criar nova aplicação.** Forneça um nome para a sua aplicação e selecione **Criar.**

   :::image type="content" source="./media/visualize-qlik-sense/create-new-qlik-sense-app.png" alt-text="Criar uma nova aplicação Qlik Sense":::

2. Depois de a nova aplicação ser criada com sucesso, selecione **a aplicação Open** e escolha **adicionar dados de ficheiros e outras fontes.** 

3. A partir das fontes de dados, selecione **ODBC** para abrir a nova janela de configuração de ligação. 

4. Mude para **O Utilizador DSN** e escolha a ligação ODBC que criou anteriormente. Forneça um nome para a ligação e selecione **Criar**. 

   :::image type="content" source="./media/visualize-qlik-sense/create-new-connection.png" alt-text="Criar uma nova ligação":::

5. Depois de criar a ligação, pode escolher a base de dados, a recolha onde os dados dos videojogos estão localizados e, em seguida, pré-visualizar.

   :::image type="content" source="./media/visualize-qlik-sense/choose-database-and-collection.png" alt-text="Escolha a base de dados e a recolha"::: 

6. Em seguida, **selecione Adicionar dados** para carregar os dados para Qlik Sense. Depois de carregar dados para qlik Sense, pode gerar insights e realizar análises sobre os dados. Você pode usar as insights ou construir a sua própria app explorando as vendas de videojogos. A imagem a seguir mostra 

   :::image type="content" source="./media/visualize-qlik-sense/visualize-data.png" alt-text="Visualizar os dados":::

### <a name="limitations-when-connecting-with-odbc"></a>Limitações ao ligar-se ao ODBC 

Cosmos DB é uma base de dados distribuída sem esquemas com condutores modelados em torno das necessidades do desenvolvedor. O controlador ODBC requer uma base de dados com esquema para inferir colunas, seus tipos de dados e outras propriedades. A consulta regular do SQL ou a sintaxe DML com capacidade relacional não é aplicável à Cosmos DB SQL API porque a SQL API não é ANSI SQL. Devido a esta razão, as declarações SQL emitidas através do controlador ODBC são traduzidas para a sintaxe SQL específica da Cosmos DB que não tem equivalentes para todas as construções. Para evitar estas questões de tradução, deve aplicar um esquema ao configurar a ligação ODBC. A ligação com o artigo [do controlador ODBC](odbc-driver.md) dá-lhe sugestões e métodos para ajudá-lo a configurar o esquema. Certifique-se de criar este mapeamento para cada base de dados/coleção dentro da conta Cosmos DB.

## <a name="next-steps"></a>Passos Seguintes

Se estiver a utilizar uma ferramenta de visualização diferente, como o Power BI, pode ligar-se a ela utilizando as instruções do seguinte doc:

* [Visualizar os dados do Cosmos DB utilizando o conector Power BI](powerbi-visualize.md)
