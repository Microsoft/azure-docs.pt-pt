---
title: Azure Monitor resulta em fontes de dados Docs da Microsoft
description: Simplificar relatórios complexos com livros de trabalho azure monitor pré-construídos e personalizados construídos a partir de múltiplas fontes de dados
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84697022"
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

## <a name="alerts-preview"></a>Alertas (pré-visualização)

Os livros de trabalho permitem que os utilizadores visualizem os alertas ativos relacionados com os seus recursos. Esta funcionalidade permite a criação de relatórios que reúnam dados de notificação (alerta) e informações de diagnóstico (métricas, registos) num único relatório. Esta informação também pode ser unida para criar relatórios ricos que combinem insights através destas fontes de dados.

Para fazer um controlo de consulta utilizar esta fonte de dados, utilize o tipo de consulta para escolher Alertas e selecione as subscrições, grupos de recursos ou recursos para direcionar. Utilize as descidas do filtro de alerta para selecionar um subconjunto interessante de alertas para as suas necessidades analíticas.

![Screenshot da consulta de alertas](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Saúde da carga de trabalho (pré-visualização)

O Azure Monitor tem funcionalidade que monitoriza proativamente a disponibilidade e desempenho dos sistemas operativos windows ou linux. Azure Monitor modela componentes-chave e suas relações, critérios para como medir a saúde desses componentes, e quais os componentes que o alertam quando é detetada uma condição pouco saudável. Os livros de trabalho permitem que os utilizadores utilizem esta informação para criar relatórios interativos ricos.

Para fazer um controlo de consulta utilizar esta fonte de dados, utilize o **tipo de consulta** para escolher a Saúde da Carga de Trabalho e selecione a subscrição, grupo de recursos ou recursos VM para direcionar. Utilize as gotas de filtro de saúde para selecionar um subconjunto interessante de incidentes de saúde para as suas necessidades analíticas.

![Screenshot da consulta de alertas](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Saúde dos recursos Azure 

Os livros de trabalho apoiam a obtenção da saúde dos recursos Azure e combinam-na com outras fontes de dados para criar relatórios de saúde ricos e interativos

Para fazer um controlo de consulta utilizar esta fonte de dados, utilize o **tipo de consulta** para escolher a saúde Azure e selecione os recursos para o alvo. Utilize as gotas de queda do filtro de saúde para selecionar um subconjunto interessante de problemas de recursos para as suas necessidades analíticas.

![Screenshot da consulta de alertas](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Data Explorer (pré-visualização)

Os livros de trabalho têm agora suporte para consulta a partir de clusters [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/) com a poderosa linguagem de consulta [Kusto.](https://docs.microsoft.com/azure/kusto/query/index)   

![Screenshot da janela de consulta de Kusto](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Passos seguintes

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.
