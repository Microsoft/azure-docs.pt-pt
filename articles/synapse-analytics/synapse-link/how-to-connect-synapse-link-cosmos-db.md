---
title: Ligar ao Azure Synapse Link para Cosmos DB
description: Saiba como ligar uma base de dados DB do Azure Cosmos a um espaço de trabalho Azure Synapse com a Azure Synapse Link.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: acomet
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 1c2dedfcbfd937215320c90d63c60d2e868939b7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868601"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Ligar ao Azure Synapse Link para Cosmos DB

Este artigo descreve como aceder a uma base de dados DB Azure Cosmos do Azure Synapse Analytics Studio com Azure Synapse Link.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ligar uma base de dados DB do Azure Cosmos ao seu espaço de trabalho, vai precisar de um:

* Base de dados DB Azure Cosmos existente, ou criar uma nova conta seguindo os passos em [Quickstart: Gerir uma conta DB Azure Cosmos](../../cosmos-db/how-to-manage-database-account.md).
* Espaço de trabalho existente em Azure Synapse, ou criar um novo espaço de trabalho seguindo os passos em [Quickstart: Criar um espaço de trabalho synapse](../quickstart-create-workspace.md).

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Ativar o Link Synapse numa conta de base de dados DB do Azure Cosmos

Para executar análises em larga escala em Azure Cosmos DB sem afetar o seu desempenho operacional, recomendamos permitir a Ligação Synapse para Azure Cosmos DB. A Synapse Link traz a capacidade HTAP a um contentor e suporte incorporado em Azure Synapse.

## <a name="go-to-synapse-studio"></a>Ir para o Estúdio Synapse

A partir do seu espaço de trabalho Azure Synapse, selecione **Launch Synapse Studio**. Na página inicial do Synapse Studio, selecione **Data**, que o leva ao Explorador de Objetos de Dados.

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Ligue uma base de dados DB do Azure Cosmos a um espaço de trabalho da Azure Synapse

A ligação de uma base de dados DB Azure Cosmos é feita como um serviço ligado. Com um serviço DB DB Azure Cosmos, você pode navegar e explorar dados, ler e escrever a partir de Apache Spark para Azure Synapse Analytics ou SQL em Azure Cosmos DB.

A partir do Data Object Explorer, pode ligar-se diretamente a uma base de dados DB Azure Cosmos seguindo estes passos:

1. Selecione o **+** ícone perto de **Data**.
1. Selecione **Ligar para dados externos.**
1. Selecione a API a que pretende ligar, por exemplo, **API SQL** ou **API para MongoDB**.
1. Selecione **Continuar**.
1. Use um nome amigável para nomear o serviço ligado. O nome aparecerá no Data Object Explorer e é utilizado pela Azure Synapse para ligar à base de dados e aos contentores.
1. Selecione o nome da **conta DB Azure Cosmos** e o nome da **base de dados**.
1. (Opcional) Se nenhuma região for especificada, as operações de execução do Azure Synapse serão encaminhadas para a região mais próxima, onde a loja analítica está ativada. Também pode definir manualmente a região que pretende que os seus utilizadores utilizem para aceder à loja analítica Azure Cosmos DB. Selecione **propriedades de ligação adicionais** e, em seguida, selecione **New**. Em **Nome da Propriedade,** insira **As Regiões Preferenciais.** Desacorda o **valor** da região que pretende, por exemplo, **WestUS2**. (Não há espaços entre as palavras e o número.)
1. Selecione **Criar**.

As bases de dados DB do Azure Cosmos aparecem no separador **Linked** na secção DB do **Azure Cosmos.** Com o Azure Cosmos DB, pode diferenciar um recipiente habilitado para HTAP a partir de um recipiente apenas OLTP através dos seguintes ícones:

**Recipiente apenas para OLTP:**

![Visualização que mostra o ícone do recipiente OLTP.](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Recipiente habilitado para HTAP:**

![Visualização que mostra o ícone do recipiente HTAP.](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interaja rapidamente com ações geradas por código

Ao clicar à direita num recipiente, tem uma lista de gestos que desencadearão um tempo de execução spark ou SQL. A escrita num contentor acontecerá através da Loja Transacional da Azure Cosmos DB e consumirá Unidades de Pedido.  

## <a name="next-steps"></a>Passos seguintes

* [Saiba o que é suportado entre Azure Synapse e Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Configurar pontos finais privados para a loja analítica Azure Cosmos DB](../../cosmos-db/analytical-store-private-endpoints.md)
* [Saiba como consultar a loja de analítica com a Spark](./how-to-query-analytical-store-spark.md)