---
title: Fontes de dados dos livros Azure Monitor Microsoft docs
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394073"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Fontes de dados dos livros Azure Monitor

Os livros de trabalho são compatíveis com um grande número de fontes de dados. Este artigo irá acompanhá-lo através de fontes de dados que estão atualmente disponíveis para os livros do Monitor Do Azure.

## <a name="logs"></a>Registos

Os livros de reprodução permitem a consulta de registos das seguintes fontes:

* Registos de Monitores Azure (Recursos de Insights de Aplicação e Espaços de Trabalho de Log Analytics.)
* Dados centrados em recursos (registos de atividade)

Os autores de livros podem usar consultas KQL que transformam os dados de recursos subjacentes para selecionar um conjunto de resultados que pode visualizar como texto, gráficos ou grelhas.

![Screenshot de livros de trabalho logs interface relatório](./media/workbooks-overview/logs.png)

Os autores de livros podem facilmente consultar vários recursos criando uma experiência de reporte verdadeiramente unificada e rica.

## <a name="metrics"></a>Métricas

Os recursos azure emitem [métricas](data-platform-metrics.md) que podem ser acedidas através de livros. As métricas podem ser acedidas em livros através de um controlo especializado que lhe permite especificar os recursos-alvo, as métricas desejadas e a sua agregação. Estes dados podem então ser traçados em gráficos ou grelhas.

![Screenshot dos gráficos de métricas do livro da utilização do CPU](./media/workbooks-overview/metrics-graph.png)

![Screenshot da interface métricas do livro](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

Os livros de trabalho apoiam a consulta de recursos e os seus metadados utilizando o Azure Resource Graph (ARG). Esta funcionalidade é usada principalmente para construir âmbitos de consulta personalizados para relatórios. O âmbito do recurso é expresso através de um subconjunto KQL que a ARG suporta – o que é muitas vezes suficiente para casos de uso comum.

Para fazer um controlo de consulta, utilize esta fonte de dados, utilize o drop-down do tipo Consulta para escolher o Azure Resource Graph e selecione as subscrições para o alvo. Utilize o controlo de consulta para adicionar o subconjunto ARG KQL que seleciona um subconjunto de recursos interessante.


![Screenshot da consulta do gráfico de recursos azure KQL](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Alertas (pré-visualização)

Os livros de redação permitem aos utilizadores visualizar os alertas ativos relacionados com os seus recursos. Esta funcionalidade permite a criação de relatórios que reúnem dados de notificação (alerta) e informações de diagnóstico (métricas, registos) num único relatório. Esta informação também pode ser unida para criar relatórios ricos que combinam insights através destas fontes de dados.

Para fazer um controlo de consulta use esta fonte de dados, use o tipo Descaído do tipo Consulta para escolher Alertas e selecione as subscrições, grupos de recursos ou recursos para o alvo. Utilize as descidas do filtro de alerta para selecionar um subconjunto interessante de alertas para as suas necessidades analíticas.

![Screenshot da consulta de alertas](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Saúde da carga de trabalho (pré-visualização)

O Azure Monitor tem uma funcionalidade que monitoriza proativamente a disponibilidade e desempenho dos sistemas operativos windows ou linux. O Azure Monitor modela componentes-chave e as suas relações, critérios para medir a saúde desses componentes e quais os componentes que o alertam quando é detetada uma condição pouco saudável. Os livros de recção permitem que os utilizadores utilizem esta informação para criar relatórios interativos ricos.

Para fazer um controlo de consulta use esta fonte de dados, use o **tipo Descaído** do tipo Consulta para escolher a Saúde da Carga de Trabalho e selecione recursos de subscrição, grupo de recursos ou VM para o alvo. Utilize as quedas do filtro de saúde para selecionar um subconjunto interessante de incidentes de saúde para as suas necessidades analíticas.

![Screenshot da consulta de alertas](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Saúde dos recursos azure 

Os livros de trabalho apoiam a saúde dos recursos do Azure e combinam-no com outras fontes de dados para criar relatórios de saúde ricos e interativos

Para fazer um controlo de consulta, utilize esta fonte de dados, utilize o drop-down do **tipo Consulta** para escolher a saúde do Azure e selecione os recursos para o alvo. Utilize as quedas do filtro de saúde para selecionar um subconjunto interessante de problemas de recursos para as suas necessidades analíticas.

![Screenshot da consulta de alertas](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Explorador de Dados Azure (pré-visualização)

Os livros de trabalho têm agora suporte para consulta de clusters [do Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/) com a poderosa linguagem de consulta [Kusto.](https://docs.microsoft.com/azure/kusto/query/index)   

![Screenshot da janela de consulta de Kusto](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Passos seguintes

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos recursos do seu livro.
