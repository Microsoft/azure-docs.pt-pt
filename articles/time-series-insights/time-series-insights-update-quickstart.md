---
title: 'Quickstart: Explore o ambiente de demonstração de pré-visualização - Azure Time Series Insights [ Microsoft Docs'
description: Explore as principais características do ambiente de demonstração de visualização da Série de Tempo Azure Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/27/2020
ms.openlocfilehash: c6cfec1d00fbd5b18e01afb0b51598e615cb0946
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82189203"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Quickstart: Explore o ambiente de demonstração de visualização da Série de Tempo Azure Insights

Este quickstart faz com que comececom o ambiente de pré-visualização da Série De Tempo Azure Insights. Na demonstração gratuita, você visita as principais características que foram adicionadas à Pré-Visualização de Insights da Série Tempo.

O ambiente de demonstração de pré-visualização da Time Series Insights contém uma empresa de cenários, Contoso, que opera dois parques de turbinas eólicas. Cada quinta tem 10 turbinas. Cada turbina tem 20 sensores que reportam dados a cada minuto ao Hub Azure IoT. Os sensores recolhem informações sobre as condições meteorológicas, o arremesso da lâmina e a posição do boceh. Informações sobre o desempenho do gerador, o comportamento da caixa de velocidades e monitores de segurança também são registadas.

Neste arranque rápido, aprende-se a usar insights da Time Series para encontrar insights atuais nos dados de Contoso. Também realiza uma análise de causa de raiz curta para prever melhor as falhas críticas e realizar a manutenção.

> [!IMPORTANT]
> Crie uma [conta](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Azure gratuita se não tiver uma.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Explore o explorador de Insights da Série Do Tempo num ambiente de demonstração

O explorador de pré-visualização da Série Time Insights demonstra dados históricos e análise de causas de raiz. Para começar:

1. Vá ao ambiente de demonstração do [Parque Eólico De Contoso.](https://insights.timeseries.azure.com/preview/samples)  

1. Se for solicitado, inscreva-se no explorador time series Insights utilizando as credenciais da sua conta Azure.

## <a name="work-with-historical-data"></a>Trabalhar com dados históricos

1. Na **Planta Contoso 1,** olhe para a turbina eólica **W7**.  

   1. Alterar o intervalo de visualização para **1/1/17 20:00:00 para 3/10/17 20:00:00.00 (UTC)**.
   1. Para selecionar um sensor, selecione **Contoso Plant 1** > **W7** > Generator Generator**Generator** > **GeneratorSpeed**. Em seguida, reveja os valores que são mostrados.

      [![W7 na Planta Contoso 1](media/v2-update-quickstart/quick-start-generator-speed.png)](media/v2-update-quickstart/quick-start-generator-speed.png#lightbox)

1. Recentemente, Contoso encontrou um incêndio na turbina **eólica W7.** As opiniões variam sobre o que causou o incêndio. Em Time Series Insights, é apresentado o sensor de alerta de incêndio que foi ativado durante o incêndio.

   1. Alterar o intervalo de visualização para **3/9/17 20:00:00 para 3/10/17 20:00:00.00 (UTC)**.
   1. Selecione **Safety System** > **FireAlert**.

      [![Contoso encontrou um incêndio na turbina eólica W7](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Reveja outros eventos na época do incêndio para entender o que aconteceu. A pressão do óleo e os avisos ativos aumentaram pouco antes do incêndio.

   1. Selecione **sistema de arremesso** > **pressão hidráulica**.
   1. Selecione **Pitch System** > **ActiveWarning**.

      [![Reveja outros eventos ao mesmo tempo](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. A pressão do óleo e os sensores de aviso ativos aumentaram mesmo antes do incêndio. Expanda a série de tempo exibida para rever outros sinais que eram evidentes que antecederam o incêndio. Ambos os sensores oscilaram consistentemente ao longo do tempo. As flutuações indicam um padrão persistente e preocupante.

    * Alterar o intervalo de visualização para **2/24/17 20:00:00,00 para 3/10/17 20:00:00.00 (UTC)**.

      [![Pressão do óleo e sensores de alerta ativos também aumentaram](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Examinar dois anos de dados históricos revela outro evento de incêndio que teve as mesmas flutuações sensoriais.

    * Alterar o intervalo de visualização para **1/1/16 para 12/31/17** (todos os dados).

      [![Procure padrões históricos](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Ao usar mossa da Time Series e telemetria de sensores, descobrimos uma tendência a longo prazo escondida nos dados históricos. Com estas novas ideias, podemos:

* Explique o que realmente aconteceu.
* Corrija o problema.
* Coloque melhores sistemas de notificação de alerta no lugar.

## <a name="root-cause-analysis"></a>Análise da origem do problema

1. Alguns cenários requerem uma análise sofisticada para descobrir pistas nos dados. Selecione o moinho de vento **W6** na data **6/25**.

    1. Alterar o intervalo de visualização para **6/1/17 20:00:00,00 para 7/1/17 20:00:00.00 (UTC)**.
    1. Selecione **Planta Contoso 1** > **W6** > **Safety System** > **VoltageActuatorSwitchWarning**.

       [![Altere a gama de visualizações e selecione W6](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. O aviso indica um problema com a tensão do gerador. A potência global do gerador encontra-se dentro dos parâmetros normais no intervalo atual. Ao aumentar o nosso intervalo, outro padrão emerge. Uma entrega é evidente.

    1. Remova o sensor **VoltageActuatorSwitchWarning.**
    1. Selecione**ActivePower**do **Sistema** > gerador .
    1. Mude o intervalo para **3d**.

       [![Alterar o intervalo para 3d](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Ao expandir o intervalo de tempo, podemos determinar se a questão parou ou se continua.

    * Prolongue o tempo até 60 dias.

      [![Prolongar o tempo de duração para 60 dias](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Outros pontos de dados do sensor podem ser adicionados para fornecer um maior contexto. Quanto mais sensores vemos, mais completa é a nossa compreensão do problema. Vamos deixar cair um marcador para mostrar os valores reais. 

    1. Selecione **o Sistema gerador**, e, em seguida, selecione três sensores: **GridVoltagePhase1,** **GridVoltagePhase2**, e **GridVoltagePhase3**.
    1. Deixe cair um marcador no último ponto de dados na área visível.

       [![Largue um marcador](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Dois dos sensores de tensão estão a funcionar comparativamente e dentro de parâmetros normais. Parece que o sensor **GridVoltagePhase3** é o culpado.

1. Com dados altamente contextuais adicionados, a queda da fase 3 parece ainda mais ser o problema. Agora, temos uma boa pista sobre a causa do aviso. Estamos prontos para remeter o assunto para a nossa equipa de manutenção.  

    * Altere o visor para sobrepor todos os sensores do **Sistema gerador** na mesma escala de gráficos.

      [![Altere o ecrã para incluir tudo](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Agora que completou o início rápido, limpe os recursos que criou:

1. A partir do menu esquerdo no [portal Azure,](https://portal.azure.com)selecione **Todos os recursos,** e localize o seu grupo de recursos Azure Time Series Insights.
1. Ou elimina todo o grupo de recursos (e todos os recursos contidos nele) selecionando **Apagar** ou remover cada recurso individualmente.

## <a name="next-steps"></a>Passos seguintes

Está pronto para criar o seu próprio ambiente de pré-visualização de Insights da Série Tempo. Para começar:

> [!div class="nextstepaction"]
> [Planeje o ambiente de pré-visualização da sua série de tempo Insights](time-series-insights-update-plan.md)

Aprenda a usar a demonstração e as suas características:

> [!div class="nextstepaction"]
> [O explorador de pré-visualização da Série De Tempo Insights](time-series-insights-update-explorer.md)