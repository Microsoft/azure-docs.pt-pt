---
title: 'Início rápido: Explorar o ambiente de demonstração do Azure Time Series Insights Preview | Microsoft Docs'
description: Entenda o ambiente de demonstração do Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: dpalled
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 07/29/2019
ms.openlocfilehash: 3e93fdc903b8e0d4ee4233e007014bb82ea22b6b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638894"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Início rápido: Explorar o ambiente de demonstração do Azure Time Series Insights Preview

Este início rápido apresenta o ambiente de visualização de Azure Time Series Insights. Na demonstração gratuita, você pode fazer um tour pelos principais recursos que foram adicionados ao Time Series Insights Preview.

O ambiente de demonstração do Time Series Insights Preview contém uma empresa de cenário, contoso, que opera dois farms de turbina de vento. Cada Farm tem 10 turbinas. Cada turbina tem 20 sensores que relatam dados a cada minuto para o Hub IoT do Azure. Os sensores coletam informações sobre condições do clima, a densidade da folha e a posição da guinada. Informações sobre desempenho de gerador, comportamento Gearbox e monitores de segurança também são registradas.

Neste guia de início rápido, você aprende a usar Time Series Insights para encontrar informações acionáveis nos dados da contoso. Você também realiza uma análise de causa raiz curta para prever melhor as falhas críticas e realizar a manutenção.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Explorar o Time Series Insights Explorer em um ambiente de demonstração

O Time Series Insights Preview Explorer demonstra dados históricos e análise de causas raiz. Para começar:

1. Crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)se você não tiver uma.

1. Vá para o ambiente de [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) .  

1. Se você for solicitado, entre no Time Series Insights Explorer usando suas credenciais de conta do Azure.

## <a name="work-with-historical-data"></a>Trabalhar com dados históricos

1. Na **contoso Plant 1**, examine a turbina do vento **W7**.  

   1. Altere o intervalo de exibição para **1/1/17 20:00 para 3/10/17 20:00 (UTC)** .
   1. Para selecionar um sensor, selecione **contoso planta 1** > **W7** > **Generator System** > **GeneratorSpeed**. Em seguida, examine os valores mostrados.

      [![W7 na contoso Plant 1](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Recentemente, a contoso encontrou um incêndio na turbina de vento **W7**. As opiniões variam sobre o que causou o incêndio. No Time Series Insights, podemos ver que o sensor de alerta de acionamento foi ativado durante o incêndio.

   1. Altere o intervalo de exibição para **3/9/17 20:00 para 3/10/17 20:00 (UTC)** .
   1. Selecione **sistema** > de segurança**FireAlert**.

      [![A contoso encontrou um incêndio na turbina de vento W7](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Examine outros eventos ao mesmo tempo do incêndio para entender o que ocorreu. A pressão do óleo e os avisos ativos são picodos logo antes do incêndio.

   1. Selecione**HydraulicOilPressure**do **sistema** > de pitch.
   1. Selecione**ActiveWarning**do **sistema** > de pitch.

      [![Examinar outros eventos ao mesmo tempo](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. A pressão do óleo e os sensores ativos de aviso são picos logo antes do incêndio. Expanda a série temporal exibida para ver outros sinais que ficaram evidentes levando até o incêndio. Ambos os sensores flutuam consistentemente ao longo do tempo. As flutuações indicam um padrão persistente e preocupante.

    * Altere o intervalo de exibição para **2/24/17 20:00 para 3/10/17 20:00 (UTC)** .

      [![A pressão do óleo e os sensores de aviso ativos também são picos](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. Examinar dois anos de dados históricos revela outro evento de incêndio que tinha as mesmas flutuações de sensor.

    * Altere o intervalo de exibição para **1/1/16 para 12/31/17** (todos os dados).

      [![Procurar padrões de histórico](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Usando Time Series Insights e nossa telemetria de sensor, descobrimos uma tendência de longo prazo e problemática oculta nos dados históricos. Com essas novas informações, podemos:

* Explique o que realmente ocorreu.
* Corrija o problema.
* Coloque sistemas de notificação de alerta superiores em vigor.

## <a name="root-cause-analysis"></a>Análise de causa raiz

1. Alguns cenários exigem uma análise sofisticada para revelar pistas sutis nos dados. Selecione Windmill **W6** na data **6/25**.

    1. Altere o intervalo de exibição para **6/1/17 20:00 para 7/1/17 20:00 (UTC)** .
    1. Selecione **contoso Plant 1** > **W6** > de**segurança do sistema** > **VoltageActuatorSwitchWarning**.

       [![Altere o intervalo de exibição e selecione W6](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. O aviso indica um problema com a tensão sendo saída pelo gerador. A saída geral de energia do gerador está operando dentro dos parâmetros normais no intervalo atual. Ao aumentar nosso intervalo, surge outro padrão. Uma queda definitiva é evidente.

    1. Remova o sensor **VoltageActuatorSwitchWarning** .
    1. Selecione**ActivePower**do **sistema** > do gerador.
    1. Altere o intervalo para **3D**.

       [![Alterar o intervalo para 3D](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Ao expandir o intervalo de tempo, podemos determinar se o problema foi interrompido ou se ele continua.

    * Estenda o período de tempo para 60 dias.

      [![Estender o período de tempo para 60 dias](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Outros pontos de dados do sensor podem ser adicionados para fornecer um contexto maior. Quanto mais sensores veremos, mais a nossa compreensão do problema é. Vamos remover um marcador para ver os valores reais. 

    1. Selecione **sistema gerador**e, em seguida, selecione três sensores: **GridVoltagePhase1**, **GridVoltagePhase2**e **GridVoltagePhase3**.
    1. Solte um marcador no último ponto de dados na área visível.

       [![Descartar um marcador](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    Dois dos sensores de tensão estão operando comparativamente e dentro dos parâmetros normais. Parece que o sensor **GridVoltagePhase3** é o culpado.

1. Com dados altamente contextuais adicionados, a lista suspensa da fase 3 parece ser ainda mais o problema. Agora, temos um bom líder sobre a causa do aviso. Estamos prontos para referir o problema à nossa equipe de manutenção.  

    * Altere a exibição para sobrepor todos os sensores do **sistema do gerador** na mesma escala do gráfico.

      [![Altere a exibição para incluir tudo](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Agora que você concluiu o tutorial, limpe os recursos que criou:

1. No menu à esquerda na [portal do Azure](https://portal.azure.com), selecione **todos os recursos**, localize o grupo de recursos Azure Time Series insights.
1. Exclua todo o grupo de recursos (e todos os recursos contidos nele) selecionando **excluir** ou remover cada recurso individualmente.

## <a name="next-steps"></a>Passos Seguintes

Você está pronto para criar seu próprio ambiente de visualização de Time Series Insights. Para iniciar:

> [!div class="nextstepaction"]
> [Planejar seu ambiente de Time Series Insights Preview](time-series-insights-update-plan.md)

Saiba como navegar na demonstração e seus recursos:

> [!div class="nextstepaction"]
> [O Gerenciador de visualização do Time Series Insights](time-series-insights-update-explorer.md)
