---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 9971b16da42cdf1de0464857291c74a947535735
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804839"
---
Pode utilizar consultas no Data Explorer para obter e filtrar os seus dados.

1. Na parte superior a **documentos** separador no Data Explorer, reveja a consulta predefinida `SELECT * FROM c`. Esta consulta obtém e apresenta todos os documentos na coleção na ordem de ID. 
   
   ![A consulta predefinida no Data Explorer é "SELECT * FROM c"](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Para alterar a consulta, selecione **Editar filtro**, substitua a consulta predefinida com `ORDER BY c._ts DESC`e, em seguida, selecione **aplicar filtro**.
   
   ![Altere a consulta predefinida ao adicionar ORDER BY c._ts DESC e clique em Aplicar Filtro](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   O apresenta consulta modificado os documentos por ordem, com base no respetivo carimbo de hora, portanto, agora o segundo documento descendente é listada em primeiro lugar. 
   
   ![Alterar a consulta para ORDER BY c. TS DESC e clique em Aplicar filtro](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Se estiver familiarizado com a sintaxe SQL, pode introduzir qualquer suportado [consultas SQL](../articles/cosmos-db/sql-api-sql-query.md) na caixa de predicado de consulta. Também pode utilizar o Data Explorer para criar procedimentos armazenados, UDFs e acionadores para lógica de negócio do lado do servidor. 

O Data Explorer proporciona acesso fácil de portal do Azure para todos os recursos de acesso a dados programáticos incorporados disponíveis nas APIs. Também utilizar o portal para dimensionar o débito, obter chaves e cadeias de ligação e reveja as métricas e SLAs para a sua conta do Azure Cosmos DB. 

