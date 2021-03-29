---
title: Monitorizar operações e atividades
titleSuffix: Azure Cognitive Search
description: Ativar o registo, obter métricas de atividade de consulta, utilização de recursos e outros dados do sistema a partir de um serviço de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: aa224a09317aafd49ae10c89ae0c50455ddd4602
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709927"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitorizar operações e atividade da Azure Cognitive Search

Este artigo é uma visão geral de conceitos e ferramentas de monitorização para a Pesquisa Cognitiva Azure. Para monitorização holística, pode utilizar uma combinação de funcionalidades incorporadas e serviços adicionais como o Azure Monitor.

No total, pode acompanhar o seguinte:

* Serviço: saúde/disponibilidade e alterações na configuração do serviço.
* Armazenamento: utilizado e disponível, com contagens para cada tipo de conteúdo relativo ao contingente permitido para o nível de serviço.
* Atividade de consulta: volume, latência, e consultas estranguladas ou abandonadas. Os pedidos de consulta registados requerem [o Azure Monitor](#add-azure-monitor).
* Atividade de indexação: requer registo de [diagnóstico](#add-azure-monitor) com monitor Azure.

Um serviço de pesquisa não suporta a autenticação por utilizador, pelo que não serão encontradas informações de identidade nos registos.

## <a name="built-in-monitoring"></a>Monitorização incorporada

A monitorização incorporada refere-se a atividades que são registadas por um serviço de pesquisa. Com exceção dos diagnósticos, não é necessária nenhuma configuração para este nível de monitorização.

A Azure Cognitive Search mantém dados internos sobre um calendário de 30 dias para reportar sobre métricas de saúde e consulta de serviço, que pode encontrar no portal ou através destas [APIs REST](#monitoring-apis).

A imagem que se segue ajuda a localizar informações de monitorização no portal. Os dados ficam disponíveis assim que começar a utilizar o serviço. As páginas do portal são atualizadas a cada poucos minutos.

* **O separador de monitorização,** na página geral principal, mostra volume de consulta, latência e se o serviço está sob pressão.
* **O registo de atividade**, no painel de navegação esquerdo, está ligado ao Gestor de Recursos Azure. O registo de atividades informa sobre as ações realizadas pelo Gestor de Recursos: disponibilidade e estado do serviço, alterações na capacidade (réplicas e divisórias) e atividades relacionadas com as chaves da API.
* As definições **de monitorização,** mais abaixo, fornecem alertas configuráveis, visualização de métricas e registos de diagnóstico. Crie isto quando precisar. Uma vez recolhidos e armazenados os dados, pode consultar ou visualizar as informações para obter informações.

  ![Integração do Azure Monitor num serviço de pesquisa](./media/search-monitor-usage/azure-monitor-search.png
 "Integração do Azure Monitor num serviço de pesquisa")

> [!NOTE]
> Como as páginas do portal são atualizadas a cada poucos minutos, os números relatados são aproximados, destinados a dar-lhe uma ideia geral de quão bem o seu sistema está a servir pedidos. As métricas reais, tais como consultas por segundo (QPS) podem ser superiores ou inferiores ao número mostrado na página. Se a precisão for um requisito, considere a utilização de APIs.

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>APIs úteis para monitorização

Pode utilizar as seguintes APIs para recuperar as mesmas informações encontradas nos separadores de Monitorização e Utilização no portal.

* [Estatísticas de Serviços GET](/rest/api/searchservice/get-service-statistics)
* [Obter Estatísticas de Índices](/rest/api/searchservice/get-index-statistics)
* [OBTER Contagem de Documentos](/rest/api/searchservice/count-documents)
* [Obtenha o estado do indexante](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>Registos de atividades e saúde do serviço

A página [**de registo de Atividades**](../azure-monitor/essentials/activity-log.md#view-the-activity-log) no portal recolhe informações do Azure Resource Manager e reporta sobre alterações na saúde do serviço. Pode monitorizar o registo de atividades para condições críticas, erros e de alerta relacionadas com a saúde do serviço.

As entradas comuns incluem referências a teclas API - notificações informativas genéricas como *Get Admin Key* e *Obter Teclas de consulta*. Estas atividades indicam pedidos que foram feitos usando a tecla de administração (criar ou eliminar objetos) ou chave de consulta, mas não mostram o próprio pedido. Para obter informações sobre este grão, deve configurar a exploração de diagnóstico.

Pode aceder ao registo de **Atividade** a partir do painel de navegação à esquerda, ou a partir de Notificações na barra de comando da janela superior, ou a partir da página **de Diagnóstico e resolver problemas.**

### <a name="monitor-storage-in-the-usage-tab"></a>Monitorar o armazenamento no separador Utilização

Para monitorização visual no portal, o separador **Utilização** mostra a disponibilidade de recursos relativamente aos [limites](search-limits-quotas-capacity.md) atuais impostos pelo nível de serviço. Se estiver a finalizar decisões sobre [qual o nível a utilizar para cargas de trabalho](search-sku-tier.md)de produção , ou se para ajustar o número de [réplicas e divisórias ativas,](search-capacity-planning.md)estas métricas podem ajudá-lo com essas decisões, mostrando-lhe a rapidez com que os recursos são consumidos e quão bem a configuração atual lida com a carga existente.

A seguinte ilustração é para o serviço gratuito, que é limitado a 3 objetos de cada tipo e 50 MB de armazenamento. Um serviço Básico ou Standard tem limites mais elevados, e se aumentar as contagens de partição, o armazenamento máximo sobe proporcionalmente.

![Estado de utilização relativo aos limites de nível](./media/search-monitor-usage/usage-tab.png
 "Estado de utilização relativo aos limites de nível")

> [!NOTE]
> Os alertas relativos ao armazenamento não estão atualmente disponíveis; O consumo de armazenamento não é agregado ou registado na tabela **AzureMetrics** no Azure Monitor. Para obter alertas de armazenamento, você precisaria [de construir uma solução personalizada](../azure-monitor/insights/solutions.md) que emite notificações relacionadas com recursos, onde o seu código verifica o tamanho do armazenamento e trata da resposta.

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Monitorização de complementos com o Azure Monitor

Muitos serviços, incluindo a Azure Cognitive Search, integram-se com [o Azure Monitor](../azure-monitor/index.yml) para alertas adicionais, métricas e dados de diagnóstico de registo. 

[Ative o registo de diagnóstico](search-monitor-logs.md) para um serviço de pesquisa se quiser controlar a recolha e armazenamento de dados. Os eventos registados capturados pelo Azure Monitor são armazenados na tabela **AzureDiagnostics** e consistem em dados operacionais relacionados com consultas e indexação.

O Azure Monitor fornece várias opções de armazenamento, e a sua escolha determina como pode consumir os dados:

* Escolha o armazenamento Azure Blob se quiser [visualizar os dados de registo](search-monitor-logs-powerbi.md) num relatório Power BI.
* Escolha o Log Analytics se quiser explorar dados através de consultas kusto.

O Azure Monitor tem a sua própria estrutura de faturação e os registos de diagnóstico referenciados nesta secção têm um custo associado. Para obter mais informações, consulte [a utilização e os custos estimados no Azure Monitor.](../azure-monitor//usage-estimated-costs.md)

## <a name="monitor-user-access"></a>Monitorizar o acesso do utilizador

Como os índices de pesquisa são um componente de uma aplicação maior do cliente, não existe uma metodologia incorporada para controlar ou monitorizar o acesso por utilizador a um índice. Presume-se que os pedidos provêm de um pedido de cliente que apresente um pedido de administração ou consulta. As operações de leitura-escrita do administrador incluem a criação, atualização, eliminação de objetos em todo o serviço. As operações só de leitura são consultas contra a recolha de documentos, a um único índice. 

Como tal, o que verá nos registos de atividade são referências a chamadas usando teclas de administração ou teclas de consulta. A chave apropriada está incluída em pedidos originários do código do cliente. O serviço não está equipado para manusear fichas de identidade ou personificação.

Quando existem requisitos de negócio para autorização por utilizador, a recomendação é a integração com o Azure Ative Directory. Pode utilizar $filter e identidades do utilizador para [aparar os resultados](search-security-trimming-for-azure-search-with-aad.md) de pesquisa de documentos que um utilizador não deve ver. 

Não há como registar esta informação separadamente da cadeia de consulta que inclui o parâmetro $filter. Consulte [as consultas do Monitor](search-monitor-queries.md) para obter mais detalhes sobre as cadeias de consulta de relatório.

## <a name="next-steps"></a>Passos seguintes

Fluency with Azure Monitor é essencial para a supervisão de qualquer serviço Azure, incluindo recursos como Azure Cognitive Search. Se não está familiarizado com o Azure Monitor, aproveite para rever artigos relacionados com recursos. Além dos tutoriais, o seguinte artigo é um bom lugar para começar.

> [!div class="nextstepaction"]
> [Monitorizar os recursos do Azure com o Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md)