---
title: Utilizador, sessão e análise de eventos em Azure Application Insights
description: Análise demográfica dos utilizadores da sua aplicação web.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6eb91734afac81e103cebea48865793fa687ad71
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024834"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Utilizadores, sessões e análise de eventos em Insights de Aplicações

Descubra quando as pessoas usam a sua aplicação web, quais as páginas em que estão mais interessados, onde os seus utilizadores estão localizados, e quais os navegadores e sistemas operativos que utilizam. Analise a telemetria de negócios e de utilização utilizando [o Azure Application Insights](./app-insights-overview.md).

![Screenshot dos utilizadores de insights de aplicações](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Introdução

Se ainda não vir dados nos utilizadores, sessões ou lâminas de eventos no portal Application Insights, [aprenda a começar com as ferramentas de utilização](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>A ferramenta de segmentação de Utilizadores, Sessões e Eventos

Três das lâminas de utilização usam a mesma ferramenta para cortar e picar telemetria a partir da sua aplicação web a partir de três perspetivas. Ao filtrar e dividir os dados, pode descobrir insights sobre o uso relativo de diferentes páginas e funcionalidades.

* **Ferramenta dos utilizadores**: Quantas pessoas utilizaram a sua aplicação e as suas funcionalidades.  Os utilizadores são contados usando IDs anónimos armazenados em cookies de navegador. Uma única pessoa que utilize diferentes navegadores ou máquinas será contada como mais do que um utilizador.
* **Ferramenta de sessões**: Quantas sessões de atividade do utilizador incluíram certas páginas e funcionalidades da sua aplicação. Uma sessão é contada após meia hora de inatividade do utilizador, ou após 24 horas de utilização contínua.
* **Ferramenta eventos**: Quantas vezes são utilizadas determinadas páginas e funcionalidades da sua aplicação. Uma vista de página é contada quando um navegador carrega uma página da sua aplicação, desde que a tenha [instrumentalizado](./javascript.md). 

    Um evento personalizado representa uma ocorrência de algo acontecendo na sua app, muitas vezes uma interação do utilizador como um clique de botão ou a conclusão de alguma tarefa. Insere código na sua aplicação para [gerar eventos personalizados.](./api-custom-events-metrics.md#trackevent)

## <a name="querying-for-certain-users"></a>Consulta para determinados utilizadores

Explore diferentes grupos de utilizadores ajustando as opções de consulta no topo da ferramenta Utilizadores:

* Mostrar: Escolha um grupo de utilizadores para analisar.
* Quem usou: Escolha eventos personalizados e vistas de página.
* Durante: Escolha um intervalo de tempo.
* Por: Escolha como baldear os dados, seja por um período de tempo ou por outra propriedade, como navegador ou cidade.
* Split By: Escolha um imóvel para dividir ou segmentar os dados. 
* Adicionar Filtros: Limitar a consulta a determinados utilizadores, sessões ou eventos com base nas suas propriedades, como navegador ou cidade. 
 
## <a name="saving-and-sharing-reports"></a>Guardar e partilhar relatórios 
Pode guardar relatórios dos Utilizadores, privados apenas para si na secção My Reports, ou partilhados com todos os outros com acesso a este recurso De Insights de Aplicação na secção Relatórios Partilhados.

Para partilhar um link para um relatório de Utilizadores, Sessões ou Eventos; clique em **Partilhar** na barra de ferramentas e, em seguida, copiar o link.

Para partilhar uma cópia dos dados num relatório de Utilizadores, Sessões ou Eventos; clique em **Partilhar** na barra de ferramentas e, em seguida, clicar no **ícone Word** para criar um documento Word com os dados. Ou, clique no **ícone Word** acima do gráfico principal.

## <a name="meet-your-users"></a>Conheça os seus utilizadores

A secção **Meet, os seus utilizadores,** mostra informações sobre cinco utilizadores de amostras que correspondem à consulta atual. Considerar e explorar os comportamentos dos indivíduos, além de agregados, pode fornecer insights sobre como as pessoas realmente usam a sua app.

## <a name="next-steps"></a>Passos seguintes

- Para ativar experiências de utilização, comece a enviar [eventos personalizados](./api-custom-events-metrics.md#trackevent) ou [vistas de página.](./api-custom-events-metrics.md#page-views)
- Se já envia eventos personalizados ou vistas de página, explore as ferramentas de Utilização para saber como os utilizadores usam o seu serviço.
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Fluxos de Utilizador](usage-flows.md)
    - [Livros](../visualize/workbooks-overview.md)
    - [Adicionar contexto de utilizador](./usage-overview.md)