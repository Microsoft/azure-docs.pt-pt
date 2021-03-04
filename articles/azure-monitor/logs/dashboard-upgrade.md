---
title: Atualizar as visualizações do Painel de Registos
description: Saiba como atualizar as visualizações do Painel de Registos com consultas que podem fornecer insights poderosos.
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: 276ea1e2f083da7ab4a6ab44e60bc7e0832e8651
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030941"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>Atualizar as visualizações do Painel de Registos

Em fevereiro de 2020, introduzimos uma tecnologia de visualização melhorada. Melhorando e melhorando a sua capacidade de visualizar resultados de consulta e chegar a insights poderosos, rapidamente. 

Pode ler mais sobre esta atualização neste [Azure Update](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/). 

Esta nova tecnologia de visualização está a preparar o caminho para novas e melhoradas experiências em torno do seu conjunto de resultados de consulta. 

## <a name="dashboards-in-azure"></a>Dashboards em Azure

Os painéis Azure são uma forma de visualizar o estado de toda a sua área de superfície Azure. Eles são projetados para fornecer um único painel de vidro para o seu estado de propriedade Azure e permitir uma variedade de atalhos para ações comuns. 

Para mais informações, consulte [os dashboards Azure](../../azure-portal/azure-portal-dashboards.md)


## <a name="upgrading-log-analytics-dashboard-parts"></a>Atualizar peças do painel de instrumentos do Log Analytics

A nova tecnologia de visualização aborda algumas questões comuns com a implementação antiga e introduz algumas novas capacidades para as peças de Log Analytics fixadas: 

- **Os mesmos tipos disponíveis** - Todos os tipos de visualização disponíveis no Log Analytics estão disponíveis como peças presas em dashboards.

- **Look-and-feel consistente** - O visual e a sensação de visualização das peças presas são agora quase idênticos aos do Log Analytics. As diferenças devem-se a otimizações que requerem diferenças subtis no conteúdo dos dados do visual. Consulte as considerações como parte deste documento para obter mais informações sobre essas diferenças.

- **Dicas de ferramentas e etiquetas** – Novas visualizações de Log Analytics fixas suportam as ferramentas. Gráficos de torta e donut agora suportam rótulos.

- **Lendas interativas** – Clicar na lenda da visualização permite adicionar/remover dimensões do visual fixado como no Log Analytics.

## <a name="stage-1---opt-in-upgrade-message"></a>Fase 1 - Mensagem de atualização de opt-in

Quando uma peça fixa do Log Analytics é capaz de ser atualizada, uma nova notificação *de opt-in* aparece no Log Analytics peças fixadas em dashboards que permitem aos utilizadores atualizar a sua visualização. Se quiser experimentar as novas visualizações para atualizar visualizações selecionadas no seu painel de instrumentos.

 
![Barra lateral](media/dashboard-upgrade/update-message-1.png)
 
![Screenshot que mostra como atualizar a visualização de azulejos.](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> Uma vez publicado o painel de instrumentos, a atualização é irreversível. No entanto, as alterações são descartadas se navegar para longe do painel de instrumentos sem voltar a publicar.  

Uma vez clicado, a visualização será atualizada para a nova tecnologia. Podem ocorrer diferenças subtis na visualização para se alinharem com o seu look-and-feel no Log Analytics.

Depois de atualizar as visualizações, é necessário reeditar o painel de instrumentos para que a alteração produza efeitos.

![Screenshot que mostra visualizações melhoradas.](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>Fase 2 - Migração de todos os painéis

Depois de terminado um período inicial de opt-in, a equipa do Log Analytics irá atualizar todos os dashboards do sistema. Alinhar todos os dashboards Azure permite que a equipa introduza mais visualizações e melhorias de experiência em todo o tabuleiro.

## <a name="considerations"></a>Considerações

As visualizações do Log Analytics fixadas a um dashboard têm algum comportamento específico projetado para uma experiência ideal. Reveja as seguintes considerações de design ao fixar uma visualização num painel de instrumentos.

### <a name="query-time-scope---30-day-limit"></a>Âmbito de tempo de consulta - limite de 30 dias

Como os dashboards podem conter várias visualizações de várias consultas, o âmbito de tempo para uma única consulta afixada está limitado a 30 dias. Uma única consulta só pode ser executada num intervalo de tempo menor ou igual a 30 dias. Esta limitação é para garantir um tempo razoável de carga do painel de instrumentos.

### <a name="query-data-values---25-values-and-other-grouping"></a>Valores de dados de consulta - 25 valores e outros agrupamentos

Os dashboards podem ser visualmente densos e complexos. De forma a reduzir a carga cognitiva ao visualizar um dashboard, otimizamos as visualizações limitando o ecrã a 25 tipos de dados diferentes. Quando há mais de 25, o Log Analytics otimiza os dados. Mostra individualmente os 25 tipos com a maioria dos dados como separados e, em seguida, agrupe os valores restantes num valor "outro". O gráfico que se segue mostra tal caso.  

![Screenshot que mostra um dashboard com 25 tipos de dados diferentes.](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>Atualização do painel na carga

Os tabliers são refrescados na carga. Todas as consultas relacionadas com visualizações de Log Analytics com fixação de painel são executadas e o painel de instrumentos é atualizado uma vez que carrega. Se a página do painel permanecer aberta, os dados no painel de instrumentos são atualizados a cada 60 minutos.

## <a name="next-steps"></a>Passos seguintes

[Criar e partilhar dashboards no Log Analytics](../visualize/tutorial-logs-dashboards.md)