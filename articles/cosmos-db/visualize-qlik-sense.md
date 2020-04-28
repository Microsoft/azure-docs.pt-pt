---
title: Ligue qlik Sense ao Azure Cosmos DB e visualize os seus dados
description: Este artigo descreve os passos necessários para ligar o Azure Cosmos DB à Qlik Sense e visualizar os seus dados.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 3a955060eb5f19544860c1c97abe1577084bef24
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67985548"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Ligue qlik Sense ao Azure Cosmos DB e visualize os seus dados

Qlik Sense é uma ferramenta de visualização de dados que combina dados de diferentes fontes numa única vista. Qlik Sense indexa todas as relações possíveis nos seus dados para que possa obter informações imediatas sobre os dados. Pode visualizar os dados do Azure Cosmos DB utilizando o Qlik Sense. Este artigo descreve os passos necessários para ligar o Azure Cosmos DB à Qlik Sense e visualizar os seus dados. 

> [!NOTE]
> A ligação qlik Sense ao Azure Cosmos DB é atualmente suportada apenas para contas SQL API e Azure Cosmos DB para contas MongoDB.

Você pode ligar Qlik Sense a Azure Cosmos DB com:

* Cosmos DB SQL API utilizando o conector ODBC.

* A PiC de Azure Cosmos DB para MongoDB utilizando o conector Qlik Sense MongoDB (atualmente em pré-visualização).

* API da Azure Cosmos DB para MongoDB e SQL API utilizando o conector REST API em Qlik Sense.

* Cosmos DB Mongo DB API utilizando o conector gRPC para Qlik Core.
Este artigo descreve os detalhes da ligação à Cosmos DB SQL API utilizando o conector ODBC.

Este artigo descreve os detalhes da ligação à Cosmos DB SQL API utilizando o conector ODBC.

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, certifique-se de que tem os seguintes recursos prontos:

* Descarregue o [Qlik Sense Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) ou instale o Qlik Sense em Azure [instalando o item](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense)de marketplace Qlik Sense .

* Descarregue os dados dos [videojogos,](https://www.kaggle.com/gregorut/videogamesales)estes dados da amostra estão em formato CSV. Você irá armazenar estes dados numa conta Cosmos DB e visualizá-lo em Qlik Sense.

* Crie uma conta API Azure Cosmos DB SQL utilizando os passos descritos na [criação de uma](create-sql-api-dotnet.md#create-account) secção de conta do artigo quickstart.

* [Criar uma base de dados e uma coleção](create-sql-api-java.md#add-a-container) – Pode utilizar o valor de entrada da coleção para 1000 RU/s. 

* Carregue os dados de vendas de videojogos na sua conta Cosmos DB. Pode importar os dados utilizando a ferramenta de migração de dados Azure Cosmos DB, pode fazer uma importação [sequencial](import-data.md#SQLSeqTarget) ou a [granel](import-data.md#SQLBulkTarget) de dados. Leva cerca de 3-5 minutos para os dados importarem para a conta Cosmos DB.

* Descarregue, instale e configure o controlador ODBC utilizando os passos na ligação ao Cosmos DB com o artigo de motorista da [ODBC.](odbc-driver.md) Os dados dos videojogos são um simples conjunto de dados e não é preciso editar o esquema, basta utilizar o esquema de mapeamento de recolha padrão.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Ligue Qlik Sense ao Cosmos DB

1. Abra o Qlik Sense e selecione **Criar uma nova app.** Forneça um nome para a sua aplicação e selecione **Criar**.

   ![Crie uma nova app Qlik Sense](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. Depois de a nova aplicação ser criada com sucesso, selecione **a app Open** e escolha adicionar dados a partir de **ficheiros e outras fontes**. 

3. A partir das fontes de dados, selecione **ODBC** para abrir a nova janela de configuração de ligação. 

4. Mude para **User DSN** e escolha a ligação ODBC que criou anteriormente. Forneça um nome para a ligação e selecione **Criar**. 

   ![Criar uma nova ligação](./media/visualize-qlik-sense/create-new-connection.png)

5. Depois de criar a ligação, pode escolher a base de dados, coletá-la onde os dados dos videojogos edepois pré-visualizar.

   ![Escolha a base de dados e a recolha](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Em seguida, **selecione Adicionar dados** para carregar os dados para Qlik Sense. Depois de carregar dados para qlik Sense, pode gerar insights e realizar análises sobre os dados. Você pode usar os insights ou construir sua própria app explorando as vendas de videojogos. A imagem seguinte mostra 

   ![Visualizar os dados](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Limitações na ligação com a ODBC 

Cosmos DB é uma base de dados distribuída sem esquemas com condutores modelados em torno das necessidades do desenvolvedor. O controlador da ODBC requer uma base de dados com esquemas para inferir colunas, os seus tipos de dados e outras propriedades. A consulta SQL regular ou a sintaxe DML com capacidade relacional não é aplicável à Cosmos DB SQL API porque a SQL API não é ANSI SQL. Devido a esta razão, as declarações da SQL emitidas através do condutor da ODBC são traduzidas para a sintaxe SQL específica da Cosmos db que não tem equivalentes para todas as construções. Para evitar estes problemas de tradução, deve aplicar um esquema ao configurar a ligação ODBC. A ligação com o artigo do [condutor da ODBC](odbc-driver.md) dá-lhe sugestões e métodos para ajudá-lo a configurar o esquema. Certifique-se de criar este mapeamento para cada base de dados/recolha dentro da conta Cosmos DB.

## <a name="next-steps"></a>Passos Seguintes

Se estiver a utilizar uma ferramenta de visualização diferente, como o Power BI, pode ligar-se a ela utilizando as instruções no seguinte doc:

* [Visualize os dados DB do Cosmos utilizando o conector Power BI](powerbi-visualize.md)
