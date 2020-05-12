---
title: Monitor Azure Cosmos DB com Monitor Azure para Cosmos DB Microsoft Docs
description: Este artigo descreve a funcionalidade Azure Monitor for Cosmos DB que proporciona aos proprietários da Cosmos DB uma compreensão rápida dos problemas de desempenho e utilização com as suas contas CosmosDB.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/11/2020
ms.openlocfilehash: 68b1b8c92b5119da400b0ada0fe792c855dd1f33
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195404"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db"></a>Explore o Monitor Azure para o Azure Cosmos DB

O Azure Monitor para o Azure Cosmos DB proporciona uma visão do desempenho global, falhas, capacidade e saúde operacional de todos os seus recursos Azure Cosmos DB numa experiência interativa unificada. Este artigo irá ajudá-lo a entender os benefícios desta nova experiência de monitorização, e como pode modificar e adaptar a experiência de acordo com as necessidades únicas da sua organização.   

## <a name="introduction"></a>Introdução

Antes de mergulhar na experiência, deve entender como apresenta e visualiza a informação. 

Entrega:

* **Na perspetiva** de escala dos seus recursos Azure Cosmos DB em todas as suas subscrições num único local, com a capacidade de se adequar seletivamente apenas a essas subscrições e recursos que está interessado em avaliar.

* **Faça análises** de um recurso azure cosmosDB específico para ajudar a diagnosticar problemas ou realizar análises detalhadas por categoria - utilização, falhas, capacidade e operações. A seleção de qualquer uma dessas opções proporciona uma visão aprofundada das métricas de DB do Azure Cosmos relevantes.  

* **Personalizável** - Esta experiência é construída em cima de modelos de livro do Azure Monitor, permitindo-lhe alterar quais as métricas apresentadas, modificar ou definir limiares que se alinham com os seus limites e, em seguida, economizar num livro personalizado. Os gráficos nos livros de trabalho podem então ser fixados nos tabliers Azure.  

Esta funcionalidade não requer que ative ou configure nada, estas métricas Azure Cosmos DB são recolhidas por padrão.

>[!NOTE]
>Não existe qualquer custo para aceder a esta funcionalidade e só será cobrado para as funcionalidades essenciais do Monitor Azure que configura ou ativa, conforme descrito na página de detalhes de preços do [Monitor Azure.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Ver métricas de utilização e desempenho para Azure Cosmos DB

Para ver a utilização e desempenho das suas contas de armazenamento em todas as suas subscrições, execute os seguintes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure monitor **e** selecione **Monitor**.

    ![Caixa de pesquisa com a palavra "Monitor" e uma queda que diz Serviços "Monitor" com uma imagem de estilo velocímetro](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selecione **Cosmos DB**.

    ![Screenshot do livro de visão geral cosmos DB](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Descrição geral

Em **visão geral,** a tabela apresenta métricas interativas do Azure Cosmos DB. Pode filtrar os resultados com base nas opções selecionadas a partir das seguintes listas de drop-down:

* **Subscrições** - apenas estão listadas subscrições que possuam um recurso Azure Cosmos DB.  

* **Cosmos DB** - Você pode selecionar tudo, um subconjunto, ou único recurso Azure Cosmos DB.

* **Intervalo** de tempo - por padrão, exibe as últimas 4 horas de informação com base nas seleções correspondentes feitas.

O telha de balcão sob as listas de drop-down rolls-up o número total de recursos DoB Da Azure Cosmos estão nas subscrições selecionadas. Há códigos de cores condicional ou mapas de calor para colunas no livro que reportam métricas de transação. A cor mais profunda tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos. 

A seleção de uma seta de queda ao lado de um dos recursos da Azure Cosmos DB revelará uma desagregação das métricas de desempenho ao nível individual do recipiente de base de dados:

![Queda expandida revelando recipientes individuais de base de dados e avaria de desempenho associada](./media/cosmosdb-insights-overview/container-view.png)

A seleção do nome de recurso Azure Cosmos DB realçado em azul irá levá-lo à **visão** geral padrão para a conta DB DD Do Azure Cosmos associada. 

### <a name="failures"></a>Falhas

Selecione **Falhas** no topo da página e abre a parte de **Falhas** do modelo do livro. Mostra-lhe totalmente pedidos com a distribuição de respostas que compõem esses pedidos:

![Screenshot de falhas com avaria por tipo de pedido HTTP](./media/cosmosdb-insights-overview/failures.png)

| Código      |  Descrição       | 
|-----------|:--------------------|
| `200 OK`  | Uma das seguintes operações de REST foi bem sucedida: </br>- Arranja um recurso. </br> - Coloque um recurso. </br> - POST sobre um recurso. </br> - POST Sobre um recurso de procedimento armazenado para executar o procedimento armazenado.|
| `201 Created` | Uma operação POST para criar um recurso é bem sucedida. |
| `404 Not Found` | A operação está a tentar agir com base num recurso que já não existe. Por exemplo, o recurso pode já ter sido apagado. |

Para obter uma lista completa de códigos de estado, consulte o artigo de código de [estado Do Azure Cosmos DB HTTP](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Capacidade

Selecione **Capacidade** no topo da página e abre a parte de **capacidade** do modelo do livro. Mostra quantos documentos tem, o seu documento de crescimento ao longo do tempo, o uso de dados e a quantidade total de armazenamento disponível que lhe resta.  Isto pode ser usado para ajudar a identificar potenciais problemas de armazenamento e utilização de dados.

![Livro de capacidade](./media/cosmosdb-insights-overview/capacity.png) 

Tal como acontece com o livro de visão geral, a seleção da queda ao lado de um recurso Azure Cosmos DB na coluna **de Subscrição** revelará uma avaria dos recipientes individuais que compõem a base de dados.

### <a name="operations"></a>Operações 

Selecione **Operações** no topo da página e abre a parte de **Operações** do modelo do livro. Dá-lhe a capacidade de ver os seus pedidos desfeitos pelo tipo de pedidos feitos. 

Assim, no exemplo abaixo vê-se que `eastus-billingint` está predominantemente a receber pedidos de leitura, mas com um pequeno número de upsert e criar pedidos. Enquanto que `westeurope-billingint` a leitura é apenas do ponto de vista do pedido, pelo menos nas últimas quatro horas o livro é atualmente alargado através do seu parâmetro de intervalo de tempo.

![Livro de operações](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Pin, exportar e expandir

Pode fixar qualquer uma das secções métricas num [Painel De Instrumentos Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) selecionando o ícone do pino na parte superior direita da secção.

![Pino de secção métrica para exemplo do painel de instrumentos](./media/cosmosdb-insights-overview/pin.png)

Para exportar os seus dados para o formato Excel, selecione o ícone de seta para baixo para a esquerda do ícone do pino.

![Ícone do livro de exportação](./media/cosmosdb-insights-overview/export.png)

Para expandir ou colapsar todas as vistas de drop-down no livro, selecione o ícone de expansão à esquerda do ícone de exportação:

![Expandir ícone de livro](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db"></a>Personalize o Monitor Azure para o Azure Cosmos DB

Uma vez que esta experiência é construída em cima dos modelos de livro do Azure Monitor, você tem a capacidade de **personalizar**  >  **editar** e **guardar** uma cópia da sua versão modificada em um livro personalizado. 

![Personalizar bar](./media/cosmosdb-insights-overview/customize.png)

Os livros de reprodução são guardados dentro de um grupo de recursos, quer na secção **My Reports** que seja privada para si ou na secção **Relatórios Partilhados** que esteja acessível a todos os que tenham acesso ao grupo de recursos. Depois de guardar o livro personalizado, precisa de ir à galeria do livro para o lançar.

![Lançar galeria de livro saindo do bar de comando](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Passos seguintes

* Configure [alertas métricos](../platform/alerts-metric.md) e notificações de [saúde](../../service-health/alerts-activity-log-service-notifications.md) de serviço para configurar alertaautomatizado para ajudar na deteção de problemas.

* Saiba os cenários que os livros de trabalho são projetados para apoiar, como autor de novos relatórios existentes e mais através da revisão de [relatórios interativos Create com livros de trabalho do Monitor Azure.](../app/usage-workbooks.md)
