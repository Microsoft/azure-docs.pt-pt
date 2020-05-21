---
title: Ligue-se ao Azure Synapse Link (pré-visualização) para Azure Cosmos DB
description: Como ligar um Azure Cosmos DB a um espaço de trabalho sinapse com ligação synapse Azure
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 6bdc2eca3bdf02814ee851ff266e04bb57f7978c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658823"
---
# <a name="connect-to-azure-synapse-link-preview-for-azure-cosmos-db"></a>Ligue-se ao Azure Synapse Link (pré-visualização) para Azure Cosmos DB

Este artigo descreve como aceder a uma base de dados Azure Cosmos DB do Azure Synapse Analytics Studio com Azure Synapse Link.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ligar uma base de dados Azure Cosmos DB ao seu espaço de trabalho, necessitará do seguinte:

* Base de dados Azure Cosmos DB existente ou criar uma nova conta após este [arranque rápido](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account)
* Espaço de trabalho existente em Synapse ou criar um novo espaço de trabalho após este [arranque rápido](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Ativar a loja analítica Azure Cosmos DB

Para fazer análises em larga escala no Azure Cosmos DB sem afetar o seu desempenho operacional, recomendamos que o Synapse Link para Azure Cosmos DB. Synapse Link traz a capacidade hTAP para um recipiente e suporte incorporado em Azure Synapse.

## <a name="navigate-to-synapse-studio"></a>Navegue para o Estúdio Synapse

A partir do seu espaço de trabalho Synapse, selecione **Launch Synapse Studio**. Na página inicial do Estúdio Synapse, selecione **Data, que o levará ao Explorador de **Objetos de Dados**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Ligue uma base de dados Azure Cosmos DB a um espaço de trabalho sinapse

A ligação de uma base de dados Azure Cosmos DB é feita como um serviço ligado. Um serviço ligado ao Azure Cosmos DB permite que os utilizadores naveguem e explorem dados, leiam e escrevam a partir de Apache Spark para Azure Synapse Analytics ou SQL para o Azure Cosmos DB.

A partir do Data Object Explorer, pode ligar-se diretamente a uma base de dados Azure Cosmos DB fazendo os seguintes passos:

1. Selecione ícone perto de ***+*** Dados
2. Selecione **Ligar a dados externos**
3. Selecione a API a que pretende ligar: SQL API ou API para MongoDB
4. Selecione ***Continuar***
5. Diga o nome do serviço ligado. O nome será apresentado no Object Explorer e utilizado pela Synapse tempos de execução para ligar à base de dados e aos contentores. Recomendamos o uso de um nome amigável.
6. Selecione o nome da **conta Azure Cosmos DB** e nome da base de **dados**
7. (Opcional) Se nenhuma região for especificada, as operações de tempo de execução da Synapse serão direcionadas para a região mais próxima onde a loja analítica está ativada. No entanto, pode definir manualmente qual a região que pretende que os seus utilizadores acedam à Loja Analítica Azure Cosmos DB. Selecione **propriedades de ligação adicionais** e, em seguida, **Nova**. Em **nome de propriedade,** escreva ***PreferredRegions*** e defino o **Valor** para a região que deseja (exemplo: WestUS2, não há espaço entre palavras e número)
8. Selecione ***Criar***

As bases de dados Azure Cosmos DB são visíveis sob o separador **Linked** na secção Azure Cosmos DB. Com o Azure Cosmos DB, pode diferenciar um recipiente htap habilitado de um recipiente OLTP apenas através dos seguintes ícones:

**Único recipiente OLTP:**

![Recipiente OLTP](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Recipiente ativado por HTAP:**

![Recipiente HTAP](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interaja rapidamente com ações geradas por códigos

Ao clicar à direita num recipiente, tem uma lista de gestos que irão desencadear um tempo de execução de Spark ou SQL. A escrita num contentor acontecerá através da Loja Transacional de Azure Cosmos DB e consumirá Unidades de Pedido.  

## <a name="next-steps"></a>Próximos passos

* [Saiba o que é apoiado entre Synapse e Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Saiba como consultar a loja analítica com a Spark](./how-to-query-analytical-store-spark.md)