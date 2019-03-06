---
title: Monitorizar as métricas de desempenho e armazenamento no Azure Cosmos DB
description: Saiba como monitorizar a sua conta do Azure Cosmos DB para métricas de desempenho, tais como pedidos e erros de servidor e a métrica de utilização, como o consumo de armazenamento.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: a12e0f567747b67c67f7bc825df1079d852e2f16
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448243"
---
# <a name="monitor-performance-and-storage-metrics-in-azure-cosmos-db"></a>Monitorizar as métricas de desempenho e armazenamento no Azure Cosmos DB

Pode monitorizar as suas contas do Azure Cosmos DB no [portal do Azure](https://portal.azure.com/). Para cada conta do Azure Cosmos DB, um conjunto completo de métricas está disponível para monitorizar o débito, armazenamento, disponibilidade, latência e consistência.

As métricas podem ser examinadas na página de conta, a nova página de métricas, ou no Azure Monitor.

## <a name="view-performance-metrics-on-the-metrics-page"></a>Ver métricas de desempenho na página de métricas
1. Na [portal do Azure](https://portal.azure.com/), clique em **todos os serviços**, desloque-se para **bases de dados**, clique em **Azure Cosmos DB**e, em seguida, clique no nome do Azure Conta do cosmos DB para o qual gostaria de ver métricas de desempenho.
2. Quando a nova página for carregada, no menu de recursos, em **monitorização**, clique em **métricas**.
3. Quando é aberta a página as métricas, selecione a coleção para rever a partir da **coleções** pendente.

   O portal do Azure apresenta o conjunto de métricas de coleção disponíveis. Tenha em atenção que as métricas de débito, armazenamento, disponibilidade, latência e consistência são fornecidas nos separadores separados. Para obter detalhes adicionais sobre as métricas fornecidas, clique na seta dupla no canto superior direito de cada painel de métricas.

   ![Captura de ecrã da lente de monitorização que mostra o conjunto de métricas](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Ver métricas de desempenho ao utilizar a monitorização do Azure
1. Na [portal do Azure](https://portal.azure.com/), clique em **Monitor** na barra esquerda.
2. No menu de recursos, clique em **métricas**.
3. Na **monitorizar - métricas** janela, na **grupo de recursos** menu pendente, selecione o grupo de recursos associado à conta do Azure Cosmos DB que deseja monitorar. 
4. Na **recursos** menu pendente, selecione a base de dados de contas para monitorizar.
5. Na lista de **métricas disponíveis**, selecione as métricas para apresentar. Utilize o botão CTRL para selecionar. 

## <a name="view-performance-metrics-on-the-account-page"></a>Ver métricas de desempenho na página da conta
1. Na [portal do Azure](https://portal.azure.com/), clique em **todos os serviços**, desloque-se para **bases de dados**, clique em **Azure Cosmos DB**e, em seguida, clique no nome do Azure Conta do cosmos DB para o qual gostaria de ver métricas de desempenho.
2. O **monitorização** lente mostra os seguintes mosaicos por predefinição:
   
   * Total de pedidos para o dia atual.
   * Armazenamento utilizado.
   
   ![Captura de ecrã da lente de monitorização que mostra as solicitações e a utilização do armazenamento](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. Clicar na seta dupla no canto superior direito do **pedidos** mosaico abre-se um detalhadas **métrica** página.
4. O **métrica** página mostra detalhes sobre o total de pedidos. 

## <a name="set-up-alerts-in-the-portal"></a>Configurar alertas no portal
1. Na [portal do Azure](https://portal.azure.com/), clique em **todos os serviços**, clique em **do Azure Cosmos DB**e, em seguida, clique no nome da conta do Azure Cosmos DB para o qual pretende configurar desempenho alertas de métricas.
2. No menu de recursos, clique em **regras de alerta** para abrir a página de regras de alerta.  
   ![Captura de ecrã a parte de regras de alerta selecionado](./media/monitor-accounts/madocdb10.5.png)
3. Na **regras de alerta** página, clique em **Adicionar alerta**.  
   ![Captura de ecrã da página de regras de alerta, com o botão Adicionar alerta realçado](./media/monitor-accounts/madocdb11.png)
4. Na **adicionar uma regra de alerta** , especifique:
   
   * O nome da regra de alerta que estiver a configurar.
   * Uma descrição da nova regra de alerta.
   * A métrica para a regra de alerta.
   * A condição de limiar e período que determinam quando o alerta é ativado. Por exemplo, um erro de servidor contagem maior do que 5 durante os últimos 15 minutos.
   * Se o administrador de serviços e coadministradores são enviadas por e-mail quando o alerta é acionado.
   * Endereços de e-mail adicionais para notificações de alerta.  
     ![Captura de ecrã do adicionar uma página de regra de alerta](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Monitorizar o Azure Cosmos DB através de programação
As métricas ao nível de conta disponíveis no portal, como a conta a utilização e total de pedidos de armazenamento, não estão disponíveis por meio das APIs de SQL. No entanto, pode recuperar dados de utilização ao nível da coleção com as APIs de SQL. Para obter dados de nível de coleção, faça o seguinte:

* Para utilizar a API de REST [obtém na coleção](https://msdn.microsoft.com/library/mt489073.aspx). As informações de quota e utilização para a coleção são devolvidas nos cabeçalhos x-ms-resource-quota e x-ms--a utilização de recursos na resposta.
* Para utilizar o SDK de .NET, utilize o [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) método, que retorna um [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) que contém um número de propriedades de utilização como  **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**e muito mais.

Para aceder às métricas adicionais, utilize o [SDK do Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Definições de métricas disponíveis podem ser obtidas chamando:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Consultas para obter métricas individuais, utilize o seguinte formato:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Para obter mais informações, consulte [obter métricas de recurso através da API de REST do Azure Monitor](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Tenha em atenção que "Do Azure Insights" nome foi mudado "Do Azure Monitor".  Esta entrada de blogue refere-se para o nome antigo.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o planejamento de capacidade do Azure Cosmos DB, veja a [Calculadora de Planeador de capacidade do Azure Cosmos DB](https://www.documentdb.com/capacityplanner).

