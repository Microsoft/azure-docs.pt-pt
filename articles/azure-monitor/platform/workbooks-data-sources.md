---
title: Azure Monitor resulta em fontes de dados Docs da Microsoft
description: Simplificar relatórios complexos com livros de trabalho azure monitor pré-construídos e personalizados construídos a partir de múltiplas fontes de dados
services: azure-monitor
documentationcenter: ''
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: a0349a3fe21bdc0b73252bce1207c9f3b53dc15f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143726"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Fontes de dados de livros do Azure Monitor

Os livros são compatíveis com um grande número de fontes de dados. Este artigo irá acompanhá-lo através de fontes de dados que estão atualmente disponíveis para os livros do Azure Monitor.

## <a name="logs"></a>Registos

Os livros permitem consultar registos das seguintes fontes:

* Registos do Monitor Azure (Recursos de Insights de Aplicação e Espaços de Trabalho de Log Analytics.)
* Dados centrados em recursos (registos de atividade)

Os autores do livro podem usar consultas de KQL que transformam os dados de recursos subjacentes para selecionar um conjunto de resultados que pode visualizar como texto, gráficos ou grelhas.

![Screenshot de registos de livros de trabalho reportar interface](./media/workbooks-overview/logs.png)

Os autores de livros de trabalho podem facilmente consultar vários recursos criando uma experiência de reporte verdadeiramente unificada e rica.

## <a name="metrics"></a>Métricas

Os recursos azure emitem [métricas](data-platform-metrics.md) que podem ser acedidas através de livros de trabalho. As métricas podem ser acedidas em livros através de um controlo especializado que lhe permite especificar os recursos-alvo, as métricas desejadas e a sua agregação. Estes dados podem então ser traçados em gráficos ou grelhas.

![Screenshot de gráficos de métricas de livro de utilização do CPU](./media/workbooks-overview/metrics-graph.png)

![Screenshot da interface de métricas de livro](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

Os livros de trabalho suportam a consulta de recursos e seus metadados utilizando o Azure Resource Graph (ARG). Esta funcionalidade é usada principalmente para construir âmbitos de consulta personalizados para relatórios. O âmbito do recurso é expresso através de um subconjunto KQL que a ARG suporta – o que é muitas vezes suficiente para casos de uso comum.

Para fazer um controlo de consulta utilizar esta fonte de dados, utilize o drop-down do tipo de consulta para escolher o Gráfico de Recursos Azure e selecione as subscrições para o alvo. Utilize o controlo de consulta para adicionar o subconjunto ARG KQL que seleciona um subconjunto de recursos interessante.

![Screenshot da consulta KQL do Gráfico de Recurso Azure](./media/workbooks-overview/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

O livro suporta as operações do Azure Resource Manager REST. Isto permite a capacidade de consultar management.azure.com ponto final sem a necessidade de fornecer o seu próprio sinal de cabeçalho de autorização.

Para fazer um controlo de consulta utilizar esta fonte de dados, utilize a fonte de dados para escolher o Gestor de Recursos Azure. Forneça os parâmetros adequados tais como método http, caminho url, cabeçalhos, parâmetros url e/ou corpo.

> [!NOTE]
> Apenas `GET` `POST` , e as `HEAD` operações estão atualmente apoiadas.

## <a name="azure-data-explorer"></a>Azure Data Explorer

Os livros de trabalho têm agora suporte para consulta a partir de clusters [Azure Data Explorer](/azure/data-explorer/) com a poderosa linguagem de consulta [Kusto.](/azure/kusto/query/index)   

![Screenshot da janela de consulta de Kusto](./media/workbooks-overview/data-explorer.png)

## <a name="workload-health"></a>Saúde da carga de trabalho

O Azure Monitor tem funcionalidade que monitoriza proativamente a disponibilidade e desempenho dos sistemas operativos windows ou linux. Azure Monitor modela componentes-chave e suas relações, critérios para como medir a saúde desses componentes, e quais os componentes que o alertam quando é detetada uma condição pouco saudável. Os livros de trabalho permitem que os utilizadores utilizem esta informação para criar relatórios interativos ricos.

Para fazer um controlo de consulta utilizar esta fonte de dados, utilize o **tipo de consulta** para escolher a Saúde da Carga de Trabalho e selecione a subscrição, grupo de recursos ou recursos VM para direcionar. Utilize as gotas de filtro de saúde para selecionar um subconjunto interessante de incidentes de saúde para as suas necessidades analíticas.

![Screenshot da consulta de alertas](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Saúde dos recursos Azure

Os livros de trabalho apoiam a obtenção da saúde dos recursos Azure e combinam-na com outras fontes de dados para criar relatórios de saúde ricos e interativos

Para fazer um controlo de consulta utilizar esta fonte de dados, utilize o **tipo de consulta** para escolher a saúde Azure e selecione os recursos para o alvo. Utilize as gotas de queda do filtro de saúde para selecionar um subconjunto interessante de problemas de recursos para as suas necessidades analíticas.

![Screenshot da consulta de alertas](./media/workbooks-overview/resource-health.png)

## <a name="json"></a>JSON

O provedor JSON permite-lhe criar um resultado de consulta a partir do conteúdo estático do JSON. É mais comumente usado em Parâmetros para criar parâmetros de dropdown de valores estáticos. As matrizes ou objetos simples do JSON serão automaticamente convertidos em linhas e colunas de grelha.  Para comportamentos mais específicos, pode utilizar o separador Resultados e as definições de JSONPath para configurar colunas.

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

Para evitar fazer chamadas automaticamente para anfitriões não confiáveis quando utilizar modelos, o utilizador precisa de marcar os anfitriões usados como confiáveis. Isto pode ser feito clicando no botão _Add como_ trust, ou adicionando-o como um anfitrião de confiança nas definições do Livro de Trabalho. Estas configurações serão guardadas nos navegadores que suportam o IndexDb com os trabalhadores da web, mais informações [aqui.](https://caniuse.com/#feat=indexeddb)

> [!NOTE]
> Não escreva segredos em nenhum dos campos, `headers` `parameters` uma vez que `body` `url` serão visíveis para todos os utilizadores do Livro.

## <a name="next-steps"></a>Passos seguintes

* [Começar a](./workbooks-overview.md#visualizations) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.
* [Dicas de otimização de consultas log Analytics](../log-query/query-optimization.md)