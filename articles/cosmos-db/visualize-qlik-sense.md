---
title: Conecte o Qlik Sense Sense ao Azure Cosmos DB e visualize seus dados
description: Este artigo descreve as etapas necessárias para conectar Azure Cosmos DB ao sensor Qlik Sense e visualizar seus dados.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 3a955060eb5f19544860c1c97abe1577084bef24
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985548"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Conecte o Qlik Sense Sense ao Azure Cosmos DB e visualize seus dados

O Qlik Sense Sense é uma ferramenta de visualização de dados que combina dados de diferentes fontes em uma única exibição. O Qlik Sense Sense indexa cada relação possível em seus dados para que você possa obter informações imediatas sobre os dados. Você pode visualizar dados de Azure Cosmos DB usando o sensor Qlik Sense. Este artigo descreve as etapas necessárias para conectar Azure Cosmos DB ao sensor Qlik Sense e visualizar seus dados. 

> [!NOTE]
> A conexão do Qlik Sense Sense ao Azure Cosmos DB atualmente tem suporte para a API do SQL e a API do Azure Cosmos DB para contas do MongoDB.

Você pode conectar o Qlik Sense Sense ao Azure Cosmos DB com:

* Cosmos DB a API do SQL usando o conector ODBC.

* API do Azure Cosmos DB para MongoDB usando o conector do MongoDB do Qlik Sense Sense (atualmente em visualização).

* API do Azure Cosmos DB para MongoDB e API do SQL usando o conector da API REST no Qlik Sense Sense.

* Cosmos DB a API do Mongo DB usando o conector do gRPC para o Qlik Sense Core.
Este artigo descreve os detalhes da conexão com o Cosmos DB a API do SQL usando o conector ODBC.

Este artigo descreve os detalhes da conexão com o Cosmos DB a API do SQL usando o conector ODBC.

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções neste artigo, verifique se você tem os seguintes recursos prontos:

* Baixe o [Qlik Sense Sense desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) ou configure o Qlik Sense Sense no Azure [instalando o item Qlik Sense Sense Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Baixar os [dados do jogo de vídeo](https://www.kaggle.com/gregorut/videogamesales), esses dados de exemplo estão no formato CSV. Você armazenará esses dados em uma conta de Cosmos DB e os visualizará no Qlik Sense Sense.

* Crie uma conta de API do SQL Azure Cosmos DB usando as etapas descritas na seção [criar uma conta](create-sql-api-dotnet.md#create-account) do artigo de início rápido.

* [Criar um banco de dados e uma coleção](create-sql-api-java.md#add-a-container) – você pode usar definir o valor de taxa de transferência da coleção para 1000 ru/s. 

* Carregue os dados de vendas de jogos de vídeo de exemplo em sua conta de Cosmos DB. Você pode importar os dados usando Azure Cosmos DB ferramenta de migração de dados, pode fazer uma importação [seqüencial](import-data.md#SQLSeqTarget) ou [em massa](import-data.md#SQLBulkTarget) de dados. Leva cerca de 3-5 minutos para que os dados sejam importados para a conta de Cosmos DB.

* Baixe, instale e configure o driver ODBC usando as etapas no artigo [conectar-se a Cosmos DB com o driver ODBC](odbc-driver.md) . Os dados do jogo de vídeo são um conjunto de dados simples e você não precisa editar o esquema, basta usar o esquema de mapeamento de coleção padrão.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Conectar o Qlik Sense Sense ao Cosmos DB

1. Abra o Qlik Sense Sense e selecione **criar novo aplicativo**. Forneça um nome para seu aplicativo e selecione **criar**.

   ![Criar um novo aplicativo Qlik Sense Sense](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. Depois que o novo aplicativo for criado com êxito, selecione **abrir aplicativo** e escolha **Adicionar dados de arquivos e outras fontes**. 

3. Nas fontes de dados, selecione **ODBC** para abrir a janela nova configuração de conexão. 

4. Alterne para o **DSN do usuário** e escolha a conexão ODBC que você criou anteriormente. Forneça um nome para a conexão e selecione **criar**. 

   ![Criar uma nova ligação](./media/visualize-qlik-sense/create-new-connection.png)

5. Depois de criar a conexão, você pode escolher o banco de dados, a coleção em que o vídeo do jogo está localizado e, em seguida, visualizá-lo.

   ![Escolher o banco de dados e a coleção](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Em seguida, selecione **Adicionar dados** para carregar os dados no Qlik Sense Sense. Depois de carregar dados para o Qlik Sense Sense, você pode gerar informações e executar a análise nos dados. Você pode usar as informações ou criar seu próprio aplicativo explorando as vendas dos jogos de vídeo. A imagem a seguir mostra 

   ![Visualizar os dados](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Limitações ao se conectar com o ODBC 

O Cosmos DB é um banco de dados distribuído sem esquema com drivers modelados de acordo com as necessidades do desenvolvedor. O driver ODBC requer um banco de dados com esquema para inferir colunas, seus tipos de dado e outras propriedades. A consulta SQL regular ou a sintaxe DML com capacidade relacional não é aplicável a Cosmos DB API do SQL porque a API do SQL não é ANSI SQL. Devido a esse motivo, as instruções SQL emitidas por meio do driver ODBC são convertidas em sintaxe SQL específica Cosmos DB que não tem equivalentes para todas as construções. Para evitar esses problemas de tradução, você deve aplicar um esquema ao configurar a conexão ODBC. O artigo [conectar com o driver ODBC](odbc-driver.md) fornece sugestões e métodos para ajudá-lo a configurar o esquema. Certifique-se de criar esse mapeamento para cada banco de dados/coleção dentro da conta de Cosmos DB.

## <a name="next-steps"></a>Próximos Passos

Se você estiver usando uma ferramenta de visualização diferente, como Power BI, poderá se conectar a ela usando as instruções no documento a seguir:

* [Visualizar dados de Cosmos DB usando o conector de Power BI](powerbi-visualize.md)
