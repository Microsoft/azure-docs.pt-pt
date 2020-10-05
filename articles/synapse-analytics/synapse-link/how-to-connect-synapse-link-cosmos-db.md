---
title: Ligue-se ao Azure Synapse Link (pré-visualização) para Azure Cosmos DB
description: Como ligar um Azure Cosmos DB a um espaço de trabalho da Sinapse com a Azure Synapse Link
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f453c074126e448eb54d432532b9064b3eb3cb35
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91287897"
---
# <a name="connect-to-azure-synapse-link-preview-for-azure-cosmos-db"></a>Ligue-se ao Azure Synapse Link (pré-visualização) para Azure Cosmos DB

Este artigo descreve como aceder a uma base de dados DB Azure Cosmos do Azure Synapse Analytics Studio com Azure Synapse Link.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ligar uma base de dados DB do Azure Cosmos ao seu espaço de trabalho, vai precisar de um:

> [!IMPORTANT]
> A Azure Synapse Link for Azure Cosmos DB é atualmente suportado para espaços de trabalho que não têm gerido rede virtual ativada. 

* Base de dados DB Azure Cosmos existente ou criar uma nova conta após este [arranque rápido](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account)
* Espaço de trabalho synapse existente ou criar um novo espaço de trabalho após este [arranque rápido](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Ativar a loja analítica Azure Cosmos DB

Para executar análises em larga escala em Azure Cosmos DB sem afetar o seu desempenho operacional, recomendamos permitir a Ligação Synapse para Azure Cosmos DB. A Synapse Link traz a capacidade HTAP a um contentor e suporte incorporado em Azure Synapse.

## <a name="navigate-to-synapse-studio"></a>Navegue para o Estúdio Synapse

A partir do seu espaço de trabalho Synapse, selecione **Launch Synapse Studio**. Na página inicial do Estúdio Synapse, selecione **Data, que o levará ao **Explorador de Objetos de Dados.**

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Ligue uma base de dados DB do Azure Cosmos a um espaço de trabalho da Synapse

A ligação de uma base de dados DB Azure Cosmos é feita como um serviço ligado. Um serviço ligado a Azure Cosmos DB permite aos utilizadores navegar e explorar dados, ler e escrever a partir de Apache Spark para Azure Synapse Analytics ou SQL em Azure Cosmos DB.

A partir do Data Object Explorer, pode ligar-se diretamente a uma base de dados DB do Azure Cosmos, fazendo os seguintes passos:

1. Selecione ***+*** ícone perto de Dados
2. Selecione **Ligar aos dados externos**
3. Selecione a API a que pretende ligar: SQL API ou API para MongoDB
4. Selecione ***Continuar***
5. Diga o nome do serviço ligado. O nome será apresentado no Object Explorer e utilizado pela Synapse para ligar à base de dados e aos contentores. Recomendamos a utilização de um nome amigável.
6. Selecione o nome da **conta DB Azure Cosmos** e **o nome da base de dados**
7. (Opcional) Se nenhuma região for especificada, as operações de tempo de execução da Synapse serão encaminhadas para a região mais próxima, onde a loja analítica está ativada. No entanto, pode definir manualmente qual a região a que os seus utilizadores pretendem aceder à Loja Azure Cosmos DB Analytical. Selecione **propriedades de ligação adicionais** e, em seguida, **Nova**. Em **Nome da Propriedade,** escreva ***As Regiões Preferenciais*** e descreva o **Valor** para a região que deseja (exemplo: WestUS2, não há espaço entre palavras e número)
8. Selecione ***Criar***

As bases de dados DB do Azure Cosmos são visíveis no separador **Ligado** na secção DB do Azure Cosmos. Com o Azure Cosmos DB, pode diferenciar um recipiente habilitado para HTAP a partir de um recipiente OLTP apenas através dos seguintes ícones:

**Recipiente único OLTP:**

![Recipiente OLTP](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Recipiente habilitado para HTAP:**

![Recipiente HTAP](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interaja rapidamente com ações geradas por código

Ao clicar à direita num recipiente, tem uma lista de gestos que irão desencadear um tempo de execução spark ou SQL. A escrita num contentor acontecerá através da Loja Transacional da Azure Cosmos DB e consumirá Unidades de Pedido.  

## <a name="next-steps"></a>Passos seguintes

* [Saiba o que é suportado entre a Synapse e a Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Saiba como consultar a loja de analítica com a Spark](./how-to-query-analytical-store-spark.md)