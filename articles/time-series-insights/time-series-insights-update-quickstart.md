---
title: 'Início rápido: Explorar o ambiente de demonstração de pré-visualização do Azure Time Series Insights | Documentos da Microsoft'
description: Compreenda o ambiente de demonstração de pré-visualização do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: dbdbfc797d37ed38936d6cfd354383d412c6b52d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205827"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Início rápido: Explorar o ambiente de demonstração de pré-visualização do Azure Time Series Insights

Este início rápido permite-lhe começar com a pré-visualização do Azure Time Series Insights. Por meio de demonstração gratuita, vai realizar uma visita guiada recursos-chave que foram adicionados a pré-visualização do Time Series Insights.

O ambiente de demonstração de pré-visualização contém uma empresa de cenário, a Contoso, que opera dois farms de turbina do vento, cada um com 10 turbines. Cada turbina tem 20 sensores que reportam a cada minuto de dados para o IoT Hub do Azure. Os sensores de reunir informações sobre as condições de Meteorologia, pitch do painel e yaw posição. Além disso, o desempenho de gerador, comportamento gearbox e segurança monitoriza.

 Irá aprender a utilizar o Time Series Insights para localizar informações acionáveis em dados da Contoso. Também vai realizar uma análise da causa curto para melhor prever falhas críticas e realizar a manutenção.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Explore o Explorador do Time Series Insights num ambiente de demonstração

O Explorador do Time Series Insights pré-visualização demonstra os dados históricos e análise de causa raiz. Para começar:

1. Criar uma [conta do Azure gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) se um não tenha sido criado.

1. Navegue para o [demonstração de Farm de vento Contoso](https://insights.timeseries.azure.com/preview/samples) ambiente.  

1. Se lhe for pedido, inicie sessão no Explorador do Time Series Insights com as suas credenciais de conta do Azure.

## <a name="work-with-historical-data"></a>Trabalhar com dados históricos

1. Examinar a turbina eólica **W7** na **Contoso fábrica 1**.  

    * Atualizar o intervalo de visualização para **1/1/17 00:20 para 3/10/17 20:00 (UTC)**.
    * Selecione o **Contoso fábrica 1** > **W7** > **gerador sistema** > **GeneratorSpeed** sensor. Em seguida, reveja os valores resultantes.

      [![W7 na fábrica de Contoso 1](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Recentemente, o Contoso encontrados de incêndio na turbina eólica **W7**. Variam opiniões sobre o que foi a causa próximo a acionar. Após a inspeção de mais de perto, Vemos que o sensor de alerta de fogo foi ativado durante a acionar.

    * Atualizar o intervalo de visualização para **3/9/17 00:20 para 3/10/17 20:00 (UTC)**.
    * Selecione o **sistema de segurança** > **FireAlert** sensor.

      [![Foi encontrado um fire na turbina eólica W7 de contoso](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Rever outros eventos por volta da hora do fogo para compreender o que ocorreu. A pressão de petróleo e avisos ativos pelo apenas antes da acionar.

    * Selecione o **sistema de argumento de venda** > **HydraulicOilPressure** sensor.
    * Selecione o **sistema de argumento de venda** > **ActiveWarning** sensor.

      [![Rever outros eventos quase ao mesmo tempo](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. A pressão de petróleo e o Active Directory sensores de aviso pelo logo antes de acionar. Expanda a série de hora apresentada para ver outros sinais presente que levou à acionar. Ambos os sensores variou entre forma consistente ao longo do tempo com a indicação de um padrão de persistente e preocupante.

    * Atualizar o intervalo de visualização para **2/24/17 00:20 para 3/10/17 20:00 (UTC)**.

      [![Pressão de petróleo e do Active Directory sensores de aviso também pelo](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. Examinando a dois anos de dados históricos revela outro evento de fogo com os mesmo flutuações de sensor.

    * Atualizar o intervalo de visualização para **1/1/16, a 12/31/17** (todos os dados).

      [![Procurar padrões históricos](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Utilizar o Azure Time Series Insights e o nosso telemetria de sensores, Descobrimos uma tendência de longo prazo e problemática ocultada em nossos dados históricos. Com estas novas informações, podemos:

> [!div class="checklist"]
> * Explique o que ocorreu efetivamente.
> * Corrigi o problema.
> * Coloque os sistemas de notificação de alerta superior no lugar certo.

## <a name="root-cause-analysis"></a>Análise da causa raiz

1. Alguns cenários requerem uma análise sofisticada para descobrir pistas sutis nos dados. Selecione o windmill **W6** data **6/25**

    * Atualizar o intervalo de visualização para **6/1/17 20:00 para 7/1/17 20:00 (UTC)**.
    * Em seguida, selecione o **Contoso fábrica 1** > **W6** > **sistema de segurança** > **VoltageActuatorSwitchWarning**  sensor.

      [![Atualizar o intervalo de visualização e selecione W6](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. O aviso indica um problema com a tensão que está a ser o resultado pelo gerador de. Geral saída de energia de gerador de está a funcionar dentro de parâmetros normais, Considerando nosso intervalo atual. Aumente o intervalo de nossa, outro padrão surge: não existe uma entrega definida.

    * Remover os **VoltageActuatorSwitchWarning** sensor.
    * Selecione o **sistema Generator** > **ActivePower** sensor.
    * Atualizar o intervalo para **3d**.

      [![Atualizar o intervalo para 3d](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Ao expandir o intervalo de tempo, podemos determinar se o problema foi parado ou se ele continua.

    * Expanda o intervalo de tempo para 60 dias.

      [![Expandir o intervalo de tempo para 60 dias](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Podem ser adicionados outros pontos de dados de sensor para fornecer contexto superior. Os sensores de mais, pode ver, é o mais completo de nossa compreensão do problema. Vamos remover um marcador para ver os valores reais. 

    * Selecione o **sistema Generator** > **GridVoltagePhase1**, **GridVoltagePhase2**, e **GridVoltagePhase3** sensores .
    * Remova um marcador no último ponto de dados na área visível.

      [![Remover um marcador](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    Os sensores de três tensão estão a funcionar comparativamente e dentro de parâmetros normais. Ele se parece com o **GridVoltagePhase3** sensor é o culpado.

1. Com dados contextuais altamente adicionados, a entrega de fase 3 aparece ainda mais como o problema. Agora estamos prontos para fazer referência a nossa equipa de manutenção com uma oportunidade potencial boa sobre a causa do aviso o problema.  

    * Atualizar a apresentação de colocar todas **gerador de sistema** sensores na mesma escala do gráfico.

       [![Atualizar a exibição para incluir tudo](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

Está pronto para criar seu próprio ambiente de pré-visualização do Time Series Insights:

> [!div class="nextstepaction"]
> [Planejar seu ambiente de pré-visualização do Time Series Insights](time-series-insights-update-plan.md)

Aprenda a navegar a demonstração e as respetivas funcionalidades:

> [!div class="nextstepaction"]
> [O Explorador do Time Series Insights pré-visualização](time-series-insights-update-explorer.md)
