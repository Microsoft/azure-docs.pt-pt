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
ms.openlocfilehash: 64c900e01496dad0d5f6a96d8d790ae0c2a8c95a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927406"
---
Pode utilizar consultas no Data Explorer para recuperar e filtrar os seus dados.

1. No topo do separador **Itens** no Data Explorer, `SELECT * FROM c`reveja a consulta padrão . Esta consulta recupera e exibe todos os documentos da recolha por ordem de identificação. 
   
   ![A consulta predefinida no Data Explorer é "SELECT * FROM c"](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Para alterar a consulta, selecione **Editar Filtro,** substitua a consulta predefinida com `ORDER BY c._ts DESC`, e, em seguida, selecione Apply **Filter**.
   
   ![Altere a consulta predefinida ao adicionar ORDER BY c._ts DESC e clique em Aplicar Filtro](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   A consulta modificada exibe os documentos em ordem descendente com base no seu carimbo de tempo, por isso agora o seu segundo documento está listado primeiro. 
   
   ![Alteração da consulta para ORDER BY c._ts DESC e clicar em Aplicar Filtro](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Se estiver familiarizado com a sintaxe SQL, pode introduzir quaisquer [consultas SQL](../articles/cosmos-db/sql-api-sql-query.md) suportadas na caixa predicada de consulta. Também pode usar o Data Explorer para criar procedimentos armazenados, UDFs e gatilhos para a lógica do negócio do lado do servidor. 

O Data Explorer fornece acesso fácil ao portal Azure a todas as funcionalidades de acesso programático de dados integrados disponíveis nas APIs. Também usa o portal para escalar a entrada, obter chaves e cordas de ligação, e rever métricas e SLAs para a sua conta Azure Cosmos DB. 

