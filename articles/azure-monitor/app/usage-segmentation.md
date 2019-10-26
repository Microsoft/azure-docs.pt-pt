---
title: Análise de usuário, sessão e evento no Aplicativo Azure insights | Microsoft docs
description: Análise demográfica de usuários de seu aplicativo Web.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 16b0b734b6b680bea75786bfdbe77eac5e590cfe
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899404"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Análise de usuários, sessões e eventos no Application Insights

Descubra quando as pessoas usam seu aplicativo Web, em quais páginas eles estão mais interessados, onde os usuários estão localizados e quais navegadores e sistemas operacionais eles usam. Analise a telemetria de negócios e uso usando o [aplicativo Azure insights](../../azure-monitor/app/app-insights-overview.md).

![Captura de tela de Application Insights usuários](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Introdução

Se você ainda não vê dados nas folhas usuários, sessões ou eventos no Portal Application Insights, [saiba como começar a usar as ferramentas de uso](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>A ferramenta de segmentação usuários, sessões e eventos

Três das lâminas de uso usam a mesma ferramenta para fatiar e a telemetria de seus aplicativos Web a partir de três perspectivas. Filtrando e dividindo os dados, você pode revelar informações sobre o uso relativo de diferentes páginas e recursos.

* **Ferramenta de usuários**: quantas pessoas usaram seu aplicativo e seus recursos.  Os usuários são contados usando IDs anônimas armazenadas em cookies do navegador. Uma única pessoa que usa diferentes navegadores ou máquinas será contada como mais de um usuário.
* **Ferramenta de sessões**: quantas sessões de atividade do usuário incluíram determinadas páginas e recursos de seu aplicativo. Uma sessão é contada após metade de uma hora de inatividade do usuário ou após 24 horas de uso contínuo.
* **Ferramenta de eventos**: com que frequência determinadas páginas e recursos de seu aplicativo são usados. Uma exibição de página é contada quando um navegador carrega uma página do seu aplicativo, desde que você a tenha [instrumentado](../../azure-monitor/app/javascript.md). 

    Um evento personalizado representa uma ocorrência de algo que ocorre em seu aplicativo, geralmente uma interação do usuário, como um clique de botão ou a conclusão de alguma tarefa. Você insere o código em seu aplicativo para [gerar eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Consultando determinados usuários

Explore diferentes grupos de usuários ajustando as opções de consulta na parte superior da ferramenta de usuários:

* Mostrar: escolha um coorte de usuários para analisar.
* Quem usou: escolha eventos personalizados e exibições de página.
* Durante: escolha um intervalo de tempo.
* Por: escolha como Bucket os dados, seja por um período de tempo ou por outra propriedade, como navegador ou cidade.
* Dividir por: escolha uma propriedade pela qual dividir ou segmentar os dados. 
* Adicionar filtros: limite a consulta a determinados usuários, sessões ou eventos com base em suas propriedades, como navegador ou cidade. 
 
## <a name="saving-and-sharing-reports"></a>Salvando e compartilhando relatórios 
Você pode salvar relatórios de usuários, seja privado apenas para você na seção meus relatórios ou compartilhados com qualquer pessoa com acesso a esse Application Insights recurso na seção relatórios compartilhados.

Para compartilhar um link para um relatório de usuários, sessões ou eventos; clique em **compartilhar** na barra de ferramentas e copie o link.

Para compartilhar uma cópia dos dados em um relatório de usuários, sessões ou eventos; clique em **compartilhar** na barra de ferramentas e, em seguida, clique no **ícone do Word** para criar um documento do Word com os dados. Ou então, clique no **ícone do Word** acima do gráfico principal.

## <a name="meet-your-users"></a>Atenda aos seus usuários

A seção **atender aos usuários** mostra informações sobre cinco usuários de exemplo que correspondem à consulta atual. Considerar e explorar os comportamentos de indivíduos, além de agregações, pode fornecer informações sobre como as pessoas realmente usam seu aplicativo.

## <a name="next-steps"></a>Passos seguintes

- Para habilitar as experiências de uso, comece a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [exibições de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se você já enviar eventos personalizados ou exibições de página, explore as ferramentas de uso para saber como os usuários usam seu serviço.
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Fluxos do Utilizador](usage-flows.md)
    - [Livros](../../azure-monitor/app/usage-workbooks.md)
    - [Adicionar contexto de usuário](usage-send-user-context.md)