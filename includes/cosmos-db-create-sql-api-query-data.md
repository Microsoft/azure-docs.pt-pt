---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 609b62312329b3a8f9f16d15458a0a47f5eb377f
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85115217"
---
Pode utilizar consultas no Data Explorer para recuperar e filtrar os seus dados.

1. No topo do **separador Itens** no Data Explorer, reveja a consulta por defeito `SELECT * FROM c` . Esta consulta recupera e exibe todos os documentos do recipiente encomendado pela ID. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="Consulta padrão no Data Explorer é SELECT * FROM c":::
   
1. Para alterar a consulta, **selecione Editar Filtro,** substitua a consulta predefinida com `ORDER BY c._ts DESC` , e, em seguida, **selecione 'Aplicar Filtro'**.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="Altere a consulta predefinida ao adicionar ORDER BY c._ts DESC e clique em Aplicar Filtro":::

   A consulta modificada exibe os documentos em ordem descendente com base no seu carimbo de tempo, pelo que agora o seu segundo documento está listado primeiro. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="Alteração da consulta para ORDER BY c._ts DESC e clicar em Aplicar Filtro":::

Se estiver familiarizado com a sintaxe SQL, pode introduzir quaisquer [consultas SQL](../articles/cosmos-db/sql-api-sql-query.md) suportadas na caixa predicado de consulta. Também pode utilizar o Data Explorer para criar procedimentos armazenados, UDFs e gatilhos para lógica de negócio do lado do servidor. 

O Data Explorer fornece um fácil acesso ao portal Azure a todas as funcionalidades de acesso a dados programáticos incorporadas disponíveis nas APIs. Também usa o portal para escalar a produção, obter chaves e cordas de conexão, e rever métricas e SLAs para a sua conta Azure Cosmos DB. 

