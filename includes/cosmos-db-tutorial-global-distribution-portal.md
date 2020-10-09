---
title: Distribuição global do Azure Cosmos DB
description: Saiba como replicar dados globalmente com o Azure Cosmos DB no Portal do Azure
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.custom: include file
ms.openlocfilehash: cb9899712fd023aaec41f4b6bb34357b60ecb106
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75752297"
---
## <a name="add-global-database-regions-using-the-azure-portal"></a><a id="addregion"></a>Adicionar regiões de base de dados globais com o Portal do Azure
A Azure Cosmos DB está disponível em todas as [regiões do Azure][azureregions] em todo o mundo. Depois de selecionar o nível de consistência predefinido para a sua conta de base de dados, pode associar uma ou mais regiões (dependendo da sua escolha do nível de consistência predefinido e das suas necessidades de distribuição global).

1. No [Portal do Azure](https://portal.azure.com/), na barra esquerda, clique em **Azure Cosmos DB**.
2. Na página **Azure Cosmos DB**, selecione a conta de base de dados a modificar.
3. Na página da conta, clique em **Replicar dados globalmente** no menu.
4. Na página **Replicar dados globalmente**, selecione as regiões a adicionar ou remover, clicando em regiões do mapa e, em seguida, clique em **Guardar**. Existe um custo para a adição de regiões, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) ou o artigo [Distribuir dados globalmente com o Azure Cosmos DB](../articles/cosmos-db/distribute-data-globally.md) para obter mais informações.
   
    ![Clicar em regiões do mapa para adicioná-las ou removê-las][1]
    
Depois de adicionar uma segunda região, a opção **Ativação Pós-falha Manual** fica ativada na página **Replicar dados globalmente** no portal. Pode utilizar esta opção para testar o processo de ativação pós-falha ou alterar a região de escrita principal. Depois de adicionar uma terceira região, a opção **Prioridades da Ativação Pós-falha** fica ativada na mesma página, para que possa alterar a ordem de ativação pós-falha para as leituras.  

### <a name="selecting-global-database-regions"></a>Selecionar regiões globais de bases de dados
Existem dois cenários comuns para configurar duas ou mais regiões:

1. Proporcionar acesso de latência baixa aos dados pelos utilizadores finais, independentemente da respetiva localização em todo o mundo
2. Adicionar resiliência regional para continuidade empresarial e recuperação após desastre (BCDR)

Para proporcionar latência baixa aos utilizadores finais, recomenda-se que implemente a aplicação e o Azure Cosmos DB nas regiões que correspondem àquelas onde os utilizadores da aplicação estão localizados.

Para BCDR, recomenda-se que adicione regiões com base nos pares de região descritos no artigo [Continuidade empresarial e recuperação após desastre (BCDR): Regiões Emparelhadas do Azure][bcdr].

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
