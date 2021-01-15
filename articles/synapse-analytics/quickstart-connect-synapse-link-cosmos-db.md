---
title: 'Quickstart: Ligue-se à Ligação Sinapse Azure para Azure Cosmos DB'
description: Como ligar um Azure Cosmos DB a um espaço de trabalho da Sinapse com a Synapse Link
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.subservice: synapse-link
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 7462c78ecad718c30980a98ff7659a2a4d2ea020
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222161"
---
# <a name="quickstart-connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Quickstart: Ligue-se à Ligação Sinapse Azure para Azure Cosmos DB

Este artigo descreve como aceder a uma base de dados DB Azure Cosmos do estúdio Azure Synapse Analytics com Synapse Link. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de ligar uma conta DB da Azure Cosmos ao seu espaço de trabalho, há algumas coisas que precisa.

* Conta DB Azure Cosmos existente ou criar uma nova conta após este [arranque rápido](../cosmos-db/how-to-manage-database-account.md)
* Espaço de trabalho synapse existente ou criar um novo espaço de trabalho após este [arranque rápido](./quickstart-create-workspace.md) 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Ativar a loja analítica Azure Cosmos DB

Para executar análises em larga escala em Azure Cosmos DB sem afetar o seu desempenho operacional, recomendamos permitir a Ligação Synapse para Azure Cosmos DB. Esta função traz a capacidade de HTAP a um contentor e suporte incorporado em Azure Synapse. Siga este quickstart para permitir a Ligação Synapse para recipientes Cosmos DB.

## <a name="navigate-to-synapse-studio"></a>Navegue para o Estúdio Synapse

A partir do seu espaço de trabalho Synapse, selecione **Launch Synapse Studio**. Na página inicial do Estúdio Synapse, selecione **Data, que o levará ao **Explorador de Objetos de Dados.**

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Ligue uma base de dados DB do Azure Cosmos a um espaço de trabalho da Synapse

A ligação de uma base de dados DB Azure Cosmos é feita como serviço ligado. Um serviço ligado ao Cosmos DB permite aos utilizadores navegar e explorar dados, ler e escrever a partir de Apache Spark para Azure Synapse Analytics ou SQL em Azure Cosmos DB.

A partir do Data Object Explorer, pode ligar diretamente uma base de dados DB Azure Cosmos, fazendo os seguintes passos:

1. Selecione * *_+_* _ ícone perto de Dados
2. Selecione _ *Conecte-se a dados externos**
3. Selecione a API a que pretende ligar: SQL ou MongoDB
4. Selecione **_Continue_* _
5. Diga o nome do serviço ligado. O nome será apresentado no Object Explorer e utilizado pela Synapse para ligar à base de dados e aos contentores. Recomendamos a utilização de um nome amigável.
6. Selecione o nome da *conta _ Cosmos DB** e **nome da base de dados**
7. (Opcional) Se nenhuma região for especificada, as operações de tempo de execução da Synapse serão encaminhadas para a região mais próxima, onde a loja analítica está ativada. No entanto, pode definir manualmente qual a região a que os seus utilizadores pretendem aceder à loja analítica Cosmos DB. Selecione **propriedades de ligação adicionais** e, em seguida, **Nova**. Em **Nome da Propriedade,** escreva **_As Regiões Preferenciais_ _ e *descreva o valor _*** para a região desejada (exemplo: WestUS2, não há espaço entre palavras e números)
8. Selecione **_Criar_* _

As bases de dados DB do Azure Cosmos são visíveis no separador _ *Ligado** na secção DB do Azure Cosmos. Pode diferenciar um recipiente DB Azure Cosmos ativado ativado por HTAP a partir de um recipiente OLTP apenas com os seguintes ícones:

**Recipiente de sinapse:**

![Recipiente HTAP](./media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

**Recipiente único OLTP:**

![Recipiente OLTP](./media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interaja rapidamente com ações geradas por código

Quando clicar direito num recipiente, terá uma lista de gestos que desencadearão um tempo de execução spark ou SQL. A escrita num contentor acontecerá através da Loja Transacional da Azure Cosmos DB e consumirá Unidades de Pedido.  

## <a name="next-steps"></a>Passos seguintes

* [Saiba o que é suportado entre a Synapse e a Azure Cosmos DB](./synapse-link/concept-synapse-link-cosmos-db-support.md)
* [Saiba como consultar uma loja analítica com a Apache Spark para a Azure Synapse Analytics](synapse-link/how-to-query-analytical-store-spark.md)