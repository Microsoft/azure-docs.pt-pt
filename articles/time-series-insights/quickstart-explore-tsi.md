---
title: 'Quickstart: Explore o ambiente de demonstração da Gen2 - Azure Time Series Insights Gen2 / Microsoft Docs'
description: Explore as principais características do ambiente de demonstração da Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 07/07/2020
ms.openlocfilehash: 135fbe914a1bf99f1cc32bd9291658f60282aa0e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102323"
---
# <a name="quickstart-explore-the-azure-time-series-insights-gen2-demo-environment"></a>Quickstart: Explore o ambiente de demonstração da Azure Time Series Insights Gen2

Este quickstart faz-te começar com um ambiente Azure Time Series Insights Gen2. Na demo gratuita, você percorre as principais funcionalidades que foram adicionadas ao Azure Time Series Insights Gen2.

O ambiente de demonstração da Azure Time Series Insights Gen2 contém uma empresa de cenários, a Contoso, que opera duas explorações de turbinas eólicas. Cada quinta tem 10 turbinas. Cada turbina tem 20 sensores que reportam dados a cada minuto ao Azure IoT Hub. Os sensores recolhem informações sobre as condições meteorológicas, o arremesso da lâmina e a posição do bocejo. Também são registadas informações sobre o desempenho do gerador, o comportamento da caixa de velocidades e os monitores de segurança.

Neste arranque rápido, aprende-se a usar a Azure Time Series Insights Gen2 para encontrar insights accuíveis nos dados do Contoso. Também realiza uma análise de causa de raiz curta para prever melhor falhas críticas e para realizar a manutenção.

> [!IMPORTANT]
> Crie uma [conta Azure gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)   se não tiver uma.

## <a name="explore-the-azure-time-series-insights-gen2-explorer-in-a-demo-environment"></a>Explore o Azure Time Series Insights Gen2 Explorer num ambiente de demonstração

O Azure Time Series Insights Gen2 Explorer demonstra dados históricos e análise de causas de raiz. Para começar:

1. Vá ao ambiente de demonstração do [Parque Eólico Contoso.](https://insights.timeseries.azure.com/preview/samples)  

1. Se for solicitado, inscreva-se no Azure Time Series Insights Gen2 Explorer utilizando as suas credenciais de conta Azure.

## <a name="work-with-historical-data"></a>Trabalhar com dados históricos

1. Na **Planta Contoso 1,** veja a turbina eólica **W7.**  

   1. Altere o intervalo de visualização para **1/1/17 20:00:00.00 para 3/10/17 20:00:00.00 (UTC)**.
   1. Para selecionar um sensor, **selecione Contoso Plant 1**  >  **W7**  >  **Generator**  >  **SystemSpeed**. Em seguida, reveja os valores que são mostrados.

      [![W7 na Fábrica Contoso 1](media/v2-update-quickstart/quick-start-generator-speed.png)](media/v2-update-quickstart/quick-start-generator-speed.png#lightbox)

1. Recentemente, Contoso encontrou um incêndio na turbina eólica **W7.** As opiniões variam sobre o que causou o incêndio. Em Azure Time Series Insights Gen2, é exibido o sensor de alerta de incêndio que foi ativado durante o incêndio.

   1. Altere o intervalo de visualização para **3/9/17 20:00:00.00 para 3/10/17 20:00:00.00 (UTC)**.
   1. Selecione **Sistema de Segurança**  >  **FireAlert**.

      [![Contoso encontrou um incêndio na turbina eólica W7](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Reveja outros eventos na época do incêndio para entender o que aconteceu. A pressão do óleo e os avisos ativos aumentaram pouco antes do incêndio.

   1. Selecione **o sistema**de  >  **arremesso HydraulicoilPressure**.
   1. Selecione **Pitch System**  >  **ActiveWarning**.

      [![Reveja outros eventos ao mesmo tempo](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. A pressão do óleo e os sensores de aviso ativos aumentaram mesmo antes do incêndio. Expanda as séries de tempo exibidas para rever outros sinais que eram evidentes que conduziam ao incêndio. Ambos os sensores oscilaram consistentemente ao longo do tempo. As flutuações indicam um padrão persistente e preocupante.

    * Altere o intervalo de visualização para **2/24/17 20:00:00.00 para 3/10/17 20:00:00.00 (UTC)**.

      [![A pressão do óleo e os sensores de alerta ativos também aumentaram](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Examinar dois anos de dados históricos revela outro evento de incêndio que teve as mesmas flutuações de sensores.

    * Altere o intervalo de visualização para **1/1/16 para 12/31/17** (todos os dados).

      [![Procure padrões históricos](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Ao usar a Azure Time Series Insights Gen2 e a telemetria de sensores, descobrimos uma tendência a longo prazo escondida nos dados históricos. Com estas novas ideias, podemos:

* Explique o que realmente aconteceu.
* Corrija o problema.
* Coloque melhores sistemas de notificação de alerta no lugar.

## <a name="root-cause-analysis"></a>Análise da origem do problema

1. Alguns cenários requerem uma análise sofisticada para descobrir pistas em dados. Selecione o moinho de vento **W6** na data **6/25**.

    1. Altere o intervalo de visualização para **6/1/17 20:00:00.00 para 7/1/17 20:00:00.00 (UTC)**.
    1. **Selecione Contoso Plant 1**  >  **W6**  >  **Safety System**  >  **VoltageActuatorSwitchWarning**.

       [![Altere o intervalo de visualização e selecione W6](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. O aviso indica um problema com a tensão do gerador. A potência global do gerador encontra-se dentro dos parâmetros normais no intervalo atual. Ao aumentar o nosso intervalo, surge outro padrão. Uma entrega é evidente.

    1. Retire o **sensor TensgeActuatorSwitchWarning.**
    1. Selecione **O Sistema de Gerador**  >  **ActivoPower**.
    1. Altere o intervalo para **3d**.

       [![Altere o intervalo para 3d](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Ao alargar o intervalo de tempo, podemos determinar se a questão parou ou se continua.

    * Prolongue o tempo para 60 dias.

      [![Prolongar o tempo para 60 dias](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Outros pontos de dados do sensor podem ser adicionados para proporcionar um maior contexto. Quanto mais sensores vemos, mais completa é a nossa compreensão do problema. Vamos deixar cair um marcador para mostrar os valores reais. 

    1. Selecione **o Sistema gerador**, e, em seguida, selecione três sensores: **GridVoltagePhase1,** **GridVoltagePhase2**e **GridVoltagePhase3**.
    1. Deixe cair um marcador no último ponto de dados na área visível.

       [![Deixe cair um marcador](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Dois dos sensores de tensão estão a funcionar comparativamente e dentro dos parâmetros normais. Parece que o sensor **GridVoltagePhase3** é o culpado.

1. Com dados altamente contextuais adicionados, a entrega da fase 3 parece ser ainda mais o problema. Agora, temos uma boa pista sobre a causa do aviso. Estamos prontos para remeter o assunto para a nossa equipa de manutenção.  

    * Altere o visor para sobrepor todos os sensores **do Sistema Gerador** na mesma escala de gráfico.

      [![Altere o ecrã para incluir tudo](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Agora que completou o quickstart, limpe os recursos que criou:

1. A partir do menu esquerdo no [portal Azure](https://portal.azure.com), selecione **Todos os recursos**e localize o seu grupo de recursos Azure Time Series Insights Gen2.
1. Ou elimina todo o grupo de recursos (e todos os recursos contidos no mesmo) selecionando **Eliminar** ou remover cada recurso individualmente.

## <a name="next-steps"></a>Passos seguintes

Estás pronto para criar o teu próprio ambiente Azure Time Series Insights Gen2. Para começar:

> [!div class="nextstepaction"]
> [Planeie o seu ambiente Azure Time Series Insights Gen2](time-series-insights-update-plan.md)

Aprenda a usar a demonstração e as suas características:

> [!div class="nextstepaction"]
> [The Azure Time Series Insights Gen2 Explorer](time-series-insights-update-explorer.md)