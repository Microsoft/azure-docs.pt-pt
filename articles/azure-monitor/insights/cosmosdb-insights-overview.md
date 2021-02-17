---
title: Monitor Azure Cosmos DB com monitor Azure para Cosmos DB| Microsoft Docs
description: Este artigo descreve o recurso Azure Monitor for Cosmos DB que proporciona aos proprietários da Cosmos DB uma rápida compreensão dos problemas de desempenho e utilização com as suas contas CosmosDB.
author: lgayhardt
ms.author: lagayhar
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: fdf482f5afc444aff77c2ab528a4e333a0282c3d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582357"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db"></a>Explore o Monitor Azure para Azure Cosmos DB

O Azure Monitor for Azure Cosmos DB proporciona uma visão do desempenho global, falhas, capacidade e saúde operacional de todos os seus recursos DB Azure Cosmos numa experiência interativa unificada. Este artigo irá ajudá-lo a entender os benefícios desta nova experiência de monitorização, e como pode modificar e adaptar a experiência para se adequar às necessidades únicas da sua organização.   

## <a name="introduction"></a>Introdução

Antes de mergulhar na experiência, deve entender como apresenta e visualiza a informação. 

Entrega:

* **Na perspetiva** de escala dos seus recursos DB Azure Cosmos em todas as suas subscrições num único local, com a capacidade de estender seletivamente apenas essas subscrições e recursos que está interessado em avaliar.

* **Aprofundar a análise** de um recurso Azure CosmosDB para ajudar a diagnosticar problemas ou realizar análises detalhadas por categoria - utilização, falhas, capacidade e operações. A seleção de qualquer uma dessas opções proporciona uma visão aprofundada das métricas relevantes do Azure Cosmos DB.  

* **Personalizável** - Esta experiência é construída em cima dos modelos de livro do Azure Monitor, permitindo-lhe alterar as métricas que são apresentadas, modificar ou definir limiares que se alinham com os seus limites e, em seguida, guardar num livro personalizado. Os gráficos nos livros podem então ser fixados aos painéis Azure.  

Esta funcionalidade não requer que você ative ou configure nada, estas métricas Azure Cosmos DB são recolhidas por padrão.

>[!NOTE]
>Não há qualquer custo para aceder a esta funcionalidade e só será cobrado para as funcionalidades essenciais do Azure Monitor que configura ou ativa, conforme descrito na página de detalhes de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Ver métricas de utilização e desempenho para Azure Cosmos DB

Para visualizar a utilização e desempenho das suas contas de armazenamento em todas as suas subscrições, execute os seguintes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **por Monitor** e selecione **Monitor.**

    ![Caixa de pesquisa com a palavra "Monitor" e um dropdown que diz Serviços "Monitor" com uma imagem de estilo de velocímetro](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selecione **Cosmos DB**.

    ![Screenshot do livro de visão geral da Cosmos DB](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Descrição Geral

Em **visão geral,** a tabela apresenta métricas interativas de Azure Cosmos DB. Pode filtrar os resultados com base nas opções selecionadas nas seguintes listas de entrega:

* **Subscrições** - apenas subscrições que tenham um recurso DB Azure Cosmos estão listadas.  

* **Cosmos DB** - Pode selecionar todos, um subconjunto ou um único recurso DB Azure Cosmos.

* **Intervalo de tempo** - por padrão, apresenta as últimas 4 horas de informação com base nas respetivas seleções feitas.

O contador de azulejos sob as listas de drop-down rolls-up o número total de recursos DB Azure Cosmos estão nas subscrições selecionadas. Existe codificação de cores condicional ou massas de calor para colunas no livro que reportam métricas de transação. A cor mais profunda tem o valor mais elevado e uma cor mais clara baseia-se nos valores mais baixos. 

A seleção de uma seta de queda ao lado de um dos recursos DB do Azure Cosmos revelará uma desagregação das métricas de desempenho ao nível do contentor de base de dados individual:

![Queda expandida revelando recipientes de base de dados individuais e desagregação de desempenho associada](./media/cosmosdb-insights-overview/container-view.png)

A seleção do nome de recurso DB Azure Cosmos realçado em azul irá levá-lo à **visão geral** padrão para a conta DB Azure Cosmos associada. 

### <a name="failures"></a>Falhas

Selecione **Falhas** no topo da página e a parte de **Falhas** do modelo de livro abre. Mostra-lhe todos os pedidos com a distribuição de respostas que compõem esses pedidos:

![Screenshot de falhas com avaria por tipo de pedido HTTP](./media/cosmosdb-insights-overview/failures.png)

| Código |  Description       | 
|-----------|:--------------------|
| `200 OK`  | Uma das seguintes operações REST foi bem sucedida: </br>- Obter um recurso. </br> - Coloque um recurso. </br> - Post num recurso. </br> - POST num recurso de procedimento armazenado para executar o procedimento armazenado.|
| `201 Created` | Uma operação POST para criar um recurso é bem sucedida. |
| `404 Not Found` | A operação está a tentar agir com base num recurso que já não existe. Por exemplo, o recurso pode já ter sido eliminado. |

Para obter uma lista completa de códigos de estado, consulte o [artigo de código de estado Azure Cosmos DB HTTP](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Capacidade

Selecione **a capacidade** no topo da página e a parte **de capacidade** do modelo de livro abre. Mostra quantos documentos tem, o seu crescimento de documentos ao longo do tempo, o uso de dados e a quantidade total de armazenamento disponível que deixou.  Isto pode ser usado para ajudar a identificar potenciais problemas de armazenamento e utilização de dados.

![Livro de capacidade](./media/cosmosdb-insights-overview/capacity.png) 

Tal como acontece com o livro de visão geral, a seleção do drop-down ao lado de um recurso DB Azure Cosmos na coluna **subscrição** revelará uma avaria por cada recipiente que compõem a base de dados.

### <a name="operations"></a>Operações 

Selecione **Operações** no topo da página e a parte de **Operações** do modelo de livro abre. Dá-lhe a capacidade de ver os seus pedidos discriminados pelo tipo de pedidos feitos. 

Assim, no exemplo abaixo vê-se que `eastus-billingint` está predominantemente a receber pedidos de leitura, mas com um pequeno número de upsert e criar pedidos. Considerando `westeurope-billingint` que a leitura é apenas de uma perspetiva de pedido, pelo menos nas últimas quatro horas que o livro é atualmente traçado através do seu parâmetro de intervalo de tempo.

![Livro de operações](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Pin, exportação e expansão

Pode fixar qualquer uma das secções métricas a um [Painel de Instrumentos Azure](../../azure-portal/azure-portal-dashboards.md) selecionando o ícone de pino de pressão no canto superior direito da secção.

![Pino de secção métrica para o exemplo do painel de instrumentos](./media/cosmosdb-insights-overview/pin.png)

Para exportar os seus dados para o formato Excel, selecione o ícone de seta para baixo à esquerda do ícone de pino de pressão.

![Ícone de livro de exportação](./media/cosmosdb-insights-overview/export.png)

Para expandir ou colapsar todas as vistas drop-down no livro, selecione o ícone de expansão à esquerda do ícone de exportação:

![Expandir ícone de livro](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db"></a>Personalize o Monitor Azure para Azure Cosmos DB

Uma vez que esta experiência é construída em cima dos modelos de livros do Azure Monitor, você tem a capacidade de **personalizar**  >  **editar** e **guardar** uma cópia da sua versão modificada em um livro personalizado. 

![Barra personalizada](./media/cosmosdb-insights-overview/customize.png)

Os livros de trabalho são guardados dentro de um grupo de recursos, quer na secção **My Reports,** que é privada para si, quer na secção **Relatórios Partilhados** que é acessível a todos os que tenham acesso ao grupo de recursos. Depois de guardar o livro personalizado, tem de ir à galeria do livro para o lançar.

![Lançar galeria de livros da barra de comando](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Resolução de problemas

Para obter orientações sobre resolução de problemas, consulte o artigo dedicado baseado em insights baseados em [livros](troubleshoot-workbooks.md)de trabalho .

## <a name="next-steps"></a>Passos seguintes

* Configure [alertas métricos](../alerts/alerts-metric.md) e [notificações de saúde](../../service-health/alerts-activity-log-service-notifications-portal.md) de serviço para configurar alertas automatizados para ajudar na deteção de problemas.

* Saiba os cenários que os livros de trabalho são projetados para apoiar, como autorizar novos e personalizar relatórios existentes, e mais através da revisão [criar relatórios interativos com os livros de trabalho do Azure Monitor.](../visualize/workbooks-overview.md)
