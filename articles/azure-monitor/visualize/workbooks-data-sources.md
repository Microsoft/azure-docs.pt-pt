---
title: Azure Monitor resulta em fontes de dados | Docs da Microsoft
description: Simplificar relatórios complexos com livros de trabalho azure monitor pré-construídos e personalizados construídos a partir de múltiplas fontes de dados
services: azure-monitor
documentationcenter: ''
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 862c6c5253c1bb4481476b67c7cfb203c2568e24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700580"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Fontes de dados de livros do Azure Monitor

Os livros são compatíveis com um grande número de fontes de dados. Este artigo irá acompanhá-lo através de fontes de dados que estão atualmente disponíveis para os livros do Azure Monitor.

## <a name="logs"></a>Registos

Os livros permitem consultar registos das seguintes fontes:

* Registos do Monitor Azure (Recursos de Insights de Aplicação e Espaços de Trabalho de Log Analytics.)
* Dados centrados em recursos (registos de atividade)

Os autores do livro podem usar consultas de KQL que transformam os dados de recursos subjacentes para selecionar um conjunto de resultados que pode visualizar como texto, gráficos ou grelhas.

![Screenshot de registos de livros de trabalho reportar interface](./media/workbooks-data-sources/logs.png)

Os autores de livros de trabalho podem facilmente consultar vários recursos criando uma experiência de reporte verdadeiramente unificada e rica.

## <a name="metrics"></a>Métricas

Os recursos azure emitem [métricas](../essentials/data-platform-metrics.md) que podem ser acedidas através de livros de trabalho. As métricas podem ser acedidas em livros através de um controlo especializado que lhe permite especificar os recursos-alvo, as métricas desejadas e a sua agregação. Estes dados podem então ser traçados em gráficos ou grelhas.

![Screenshot de gráficos de métricas de livro de utilização do CPU](./media/workbooks-data-sources/metrics-graph.png)

![Screenshot da interface de métricas de livro](./media/workbooks-data-sources/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

Os livros de trabalho suportam a consulta de recursos e seus metadados utilizando o Azure Resource Graph (ARG). Esta funcionalidade é usada principalmente para construir âmbitos de consulta personalizados para relatórios. O âmbito do recurso é expresso através de um subconjunto KQL que a ARG suporta – o que é muitas vezes suficiente para casos de uso comum.

Para fazer um controlo de consulta utilizar esta fonte de dados, utilize o drop-down do tipo de consulta para escolher o Gráfico de Recursos Azure e selecione as subscrições para o alvo. Utilize o controlo de consulta para adicionar o subconjunto ARG KQL que seleciona um subconjunto de recursos interessante.

![Screenshot da consulta KQL do Gráfico de Recurso Azure](./media/workbooks-data-sources/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

O livro suporta as operações do Azure Resource Manager REST. Isto permite a capacidade de consultar management.azure.com ponto final sem a necessidade de fornecer o seu próprio sinal de cabeçalho de autorização.

Para fazer um controlo de consulta utilizar esta fonte de dados, utilize a fonte de dados para escolher o Gestor de Recursos Azure. Forneça os parâmetros adequados tais como método http, caminho url, cabeçalhos, parâmetros url e/ou corpo.

> [!NOTE]
> Apenas `GET` `POST` , e as `HEAD` operações estão atualmente apoiadas.

## <a name="azure-data-explorer"></a>Azure Data Explorer

Os livros de trabalho têm agora suporte para consulta a partir de clusters [Azure Data Explorer](/azure/data-explorer/) com a poderosa linguagem de consulta [Kusto.](/azure/kusto/query/index)

![Screenshot da janela de consulta de Kusto](./media/workbooks-data-sources/data-explorer.png)

## <a name="workload-health"></a>Saúde da carga de trabalho

O Azure Monitor tem funcionalidade que monitoriza proativamente a disponibilidade e desempenho dos sistemas operativos windows ou linux. Azure Monitor modela componentes-chave e suas relações, critérios para como medir a saúde desses componentes, e quais os componentes que o alertam quando é detetada uma condição pouco saudável. Os livros de trabalho permitem que os utilizadores utilizem esta informação para criar relatórios interativos ricos.

Para fazer um controlo de consulta utilizar esta fonte de dados, utilize o **tipo de consulta** para escolher a Saúde da Carga de Trabalho e selecione a subscrição, grupo de recursos ou recursos VM para direcionar. Utilize as gotas de filtro de saúde para selecionar um subconjunto interessante de incidentes de saúde para as suas necessidades analíticas.

![Screenshot da consulta de alertas](./media/workbooks-data-sources/workload-health.png)

## <a name="azure-resource-health"></a>Saúde dos recursos Azure

Os livros de trabalho apoiam a obtenção da saúde dos recursos Azure e combinam-na com outras fontes de dados para criar relatórios de saúde ricos e interativos

Para fazer um controlo de consulta utilizar esta fonte de dados, utilize o **tipo de consulta** para escolher a saúde Azure e selecione os recursos para o alvo. Utilize as gotas de queda do filtro de saúde para selecionar um subconjunto interessante de problemas de recursos para as suas necessidades analíticas.

![Screenshot de consulta de alertas que mostra as listas de filtros de saúde.](./media/workbooks-data-sources/resource-health.png)

## <a name="change-analysis-preview"></a>Análise de Alterações (pré-visualização)

Para escoar um controlo de consulta utilizando a Análise de Alteração de [Aplicação](../app/change-analysis.md) como fonte de dados, utilize a *fonte de dados* para baixo e escolha *a Análise de Alteração (pré-visualização)* e selecione um único recurso. Podem ser apresentadas alterações até aos últimos 14 dias. A queda *de nível* pode ser usada para filtrar entre alterações "Importante", "Normal" e "Barulhenta", e esta queda suporta parâmetros de livro de [queda](workbooks-dropdowns.md).

> [!div class="mx-imgBorder"]
> ![Uma imagem de um livro com a Change Analysis](./media/workbooks-data-sources/change-analysis-data-source.png)

## <a name="merge-data-from-different-sources"></a>Fundir dados de diferentes fontes

Muitas vezes é necessário reunir dados de diferentes fontes que melhorem a experiência de insights. Um exemplo é aumentar a informação de alerta ativo com dados métricos relacionados. Isto permite que os utilizadores vejam não só o efeito (um alerta ativo), mas também potenciais causas (por exemplo, alta utilização do CPU). O domínio de monitorização tem numerosas fontes de dados correlacionadas que são muitas vezes críticas para a triagem e o fluxo de trabalho de diagnóstico.

Os livros de trabalho permitem não só a consulta de diferentes fontes de dados, mas também fornece controlos simples que permitem fundir ou juntar os dados para fornecer insights ricos. O `merge` controlo é a maneira de alcançá-lo.

O exemplo abaixo combina dados de alerta com dados de desempenho VM de análise de registo para obter uma grelha de insights rica.

> [!div class="mx-imgBorder"]
> ![Uma imagem de um livro com um controlo de fusão que combina dados de alerta e de analítica de registo](./media/workbooks-data-sources/merge-control.png)

Os livros de trabalho suportam uma variedade de fusões:

* Unidos únicos interiores
* União interior completa
* Associação externa completa
* Associação externa à esquerda
* Associação externa à direita
* Semi-aderir à esquerda
* Semi-aderir à direita
* Esquerda anti-aderir
* Direito anti-aderir
* União
* Tabela duplicada

## <a name="json"></a>JSON

O provedor JSON permite-lhe criar um resultado de consulta a partir do conteúdo estático do JSON. É mais comumente usado em Parâmetros para criar parâmetros de dropdown de valores estáticos. As matrizes ou objetos simples do JSON serão automaticamente convertidos em linhas e colunas de grelha.  Para comportamentos mais específicos, pode utilizar o separador Resultados e as definições de JSONPath para configurar colunas.

Este fornecedor suporta [o JSONPath.](workbooks-jsonpath.md)

## <a name="alerts-preview"></a>Alertas (pré-visualização)

> [!NOTE]
> A forma sugerida de consultar informações sobre o Alerta de Azure é utilizando a fonte de dados do [Azure Resource Graph,](#azure-resource-graph) consultando a `AlertsManagementResources` tabela.
>
> Consulte a referência da [tabela Azure Resource Graph,](../../governance/resource-graph/reference/supported-tables-resources.md)ou o [modelo Alertas,](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook) por exemplo.
>
> A fonte de dados alerts permanecerá disponível por um período de tempo enquanto os autores transitam para a utilização de ARG. A utilização desta fonte de dados em modelos é desencorajada. 

Os livros de trabalho permitem que os utilizadores visualizem os alertas ativos relacionados com os seus recursos. Limitações: a fonte de dados de alertas requer acesso lido à Subscrição para consultar recursos, e pode não apresentar novos tipos de alertas. 

Para fazer um controlo de consulta utilizar esta fonte de dados, utilize a fonte de _dados_ para escolher _Alertas (pré-visualização)_ e selecione as subscrições, grupos de recursos ou recursos para direcionar. Utilize as descidas do filtro de alerta para selecionar um subconjunto interessante de alertas para as suas necessidades analíticas.

## <a name="custom-endpoint"></a>Ponto final personalizado

Os livros de trabalho suportam obter dados de qualquer fonte externa. Se os seus dados viverem fora do Azure, pode levá-lo aos Livros de Trabalho utilizando este tipo de fonte de dados.

Para então um controlo de consulta utilizar esta fonte de dados, utilize a fonte de _dados_ para escolher o _ponto de finalização personalizado_. Fornecer os parâmetros adequados tais `Http method` `url` `headers` como, `url parameters` e/ou `body` . . Certifique-se de que a sua fonte de dados suporta [o CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) caso contrário, o pedido falhará.

Para evitar fazer chamadas automaticamente para anfitriões não confiáveis quando utilizar modelos, o utilizador precisa de marcar os anfitriões usados como confiáveis. Isto pode ser feito clicando no botão _Add como_ trust, ou adicionando-o como um anfitrião de confiança nas definições do Livro de Trabalho. Estas configurações serão guardadas em [navegadores que suportam IndexDb com trabalhadores da web.](https://caniuse.com/#feat=indexeddb)

> [!NOTE]
> Não escreva segredos em nenhum dos campos, `headers` `parameters` uma vez que `body` `url` serão visíveis para todos os utilizadores do Livro.

Este fornecedor suporta [o JSONPath.](workbooks-jsonpath.md)

## <a name="next-steps"></a>Passos seguintes

* [Começar a](./workbooks-overview.md#visualizations) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](./workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.
* [Dicas de otimização de consultas log Analytics](../logs/query-optimization.md)