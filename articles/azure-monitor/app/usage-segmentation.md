---
title: Análise de utilizador, sessão e eventos em Insights de Aplicação Azure
description: Análise demográfica dos utilizadores da sua aplicação web.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 10ea3c68695e7e4fb4950fbcc4a190e9f1d83f49
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797706"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Análise de utilizadores, sessões e eventos em Insights de Aplicação

Descubra quando as pessoas usam a sua aplicação web, quais as páginas em que estão mais interessados, onde estão localizados os seus utilizadores e quais os navegadores e sistemas operativos que utilizam. Analise a telemetria de negócios e de utilização utilizando [insights de aplicação Azure.](../../azure-monitor/app/app-insights-overview.md)

![Screenshot dos utilizadores de insights de aplicação](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Introdução

Se ainda não vir dados nos utilizadores, sessões ou lâminas de eventos no portal Application Insights, [aprenda a começar com as ferramentas](usage-overview.md)de utilização .

## <a name="the-users-sessions-and-events-segmentation-tool"></a>A ferramenta de segmentação de Utilizadores, Sessões e Eventos

Três das lâminas de utilização usam a mesma ferramenta para cortar e pidar a telemetria da sua aplicação web a partir de três perspetivas. Filtrando e dividindo os dados, pode descobrir insights sobre o uso relativo de diferentes páginas e funcionalidades.

* **Ferramenta de utilizadores**: Quantas pessoas usaram a sua aplicação e as suas funcionalidades.  Os utilizadores são contados usando IDs anónimos armazenados em cookies de navegador. Uma única pessoa que utilize diferentes navegadores ou máquinas será contada como mais do que um utilizador.
* Ferramenta de **sessões**: Quantas sessões de atividade do utilizador incluíram determinadas páginas e funcionalidades da sua aplicação. Uma sessão é contada após meia hora de inatividade do utilizador, ou após 24 horas de utilização contínua.
* **Ferramenta de eventos**: Com que frequência são utilizadas determinadas páginas e funcionalidades da sua aplicação. Uma visualização de página é contada quando um navegador carrega uma página da sua aplicação, desde que a tenha [instrumentado](../../azure-monitor/app/javascript.md). 

    Um evento personalizado representa uma ocorrência de algo acontecendo na sua aplicação, muitas vezes uma interação do utilizador como um clique de botão ou a conclusão de alguma tarefa. Insere código na sua aplicação para [gerar eventos personalizados.](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)

## <a name="querying-for-certain-users"></a>Consulta para certos utilizadores

Explore diferentes grupos de utilizadores ajustando as opções de consulta no topo da ferramenta Utilizadores:

* Mostrar: Escolha um grupo de utilizadores para analisar.
* Quem usou: Escolha eventos personalizados e visualizações de página.
* Durante: Escolha um intervalo de tempo.
* Por: Escolha como balder os dados, seja por um período de tempo ou por outra propriedade, como o navegador ou a cidade.
* Split By: Escolha uma propriedade para dividir ou segmentar os dados. 
* Adicionar Filtros: Limite a consulta a determinados utilizadores, sessões ou eventos com base nas suas propriedades, como navegador ou cidade. 
 
## <a name="saving-and-sharing-reports"></a>Relatórios de poupança e partilha 
Pode guardar relatórios de Utilizadores, seja privado apenas para si na secção My Reports, ou partilhado com todos os outros com acesso a este recurso Desinformação de Aplicação na secção Relatórios Partilhados.

Para partilhar um link para um relatório de Utilizadores, Sessões ou Eventos; clique em **Partilhar** na barra de ferramentas e, em seguida, copiar o link.

Para partilhar uma cópia dos dados num relatório de Utilizadores, Sessões ou Eventos; clique em **Partilhar** na barra de ferramentas e, em seguida, clique no **ícone Word** para criar um documento Word com os dados. Ou, clique no **ícone Word** acima do gráfico principal.

## <a name="meet-your-users"></a>Conheça os seus utilizadores

A secção **Meet your users** mostra informações sobre cinco utilizadores de amostras correspondidas à consulta atual. Considerar e explorar os comportamentos dos indivíduos, além de agregados, pode fornecer insights sobre como as pessoas realmente usam a sua app.

## <a name="next-steps"></a>Passos seguintes

- Para permitir experiências de utilização, comece a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [visualizações de páginas.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Se já envia eventos personalizados ou visualizações de página, explore as ferramentas de Utilização para saber como os utilizadores usam o seu serviço.
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Fluxos do Utilizador](usage-flows.md)
    - [Livros](../../azure-monitor/platform/workbooks-overview.md)
    - [Adicionar contexto de utilizador](usage-send-user-context.md)
