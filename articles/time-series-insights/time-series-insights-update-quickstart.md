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
ms.date: 12/03/2018
ms.openlocfilehash: de5e853db6c6a0e98dea9251cc07b526288574e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805191"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Início rápido: Explorar o ambiente de demonstração de pré-visualização do Azure Time Series Insights

Este guia de introdução mostra-lhe como utilizar o Explorador de pré-visualização do Azure Time Series Insight num ambiente de demonstração gratuito. Saiba como utilizar o seu navegador da web para visualizar grandes volumes de dados históricos de IoT industriais e tour das principais funcionalidades do Explorador do Time Series Insights pré-visualização.

O Time Series Insights fornece uma plataforma de ponto-a-ponto a como uma oferta de serviço (PaaS). Ele pode ingerir, processar, armazenar e consultar contextualizados altamente otimizado de série de tempo, dados de dimensionamento IoT para exploração de dados improvised. Ele também fornece análise operacional. O Time Series Insights é uma oferta diferenciada que é adequada para as necessidades exclusivas de implementações de IoT industriais.

O ambiente de demonstração mostra uma empresa de geração de eletricidade, Contoso. No ambiente, utilize o Time Series Insights para localizar informações acionáveis em dados da Contoso e realizar uma análise da causa curto. Contoso opera dois farms de turbina vento, cada um com 10 turbines. Cada turbina tem 20 sensores que reportam a cada minuto de dados para o IoT Hub do Azure. Os sensores de reunir informações sobre as condições de Meteorologia, pitch do painel e yaw posição, desempenho de gerador, comportamento gearbox e monitores de segurança.

Utilize pré-visualização do Time Series Insights para analisar cada vez maior conjunto de dados da Contoso dos últimos dois anos, que está atualmente em 40 GB. Ele pode ajudá-lo a compreender melhor e preveja falhas críticas e problemas de manutenção de movimento lento.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Explore o Explorador do Time Series Insights num ambiente de demonstração

1. No seu browser, vá para o [ambiente de Farm de vento Contoso](https://insights.timeseries.azure.com/preview/samples).  

1. Se lhe for pedido, inicie sessão no Explorador do Time Series Insights com as suas credenciais de conta do Azure.

### <a name="demo-step-1"></a>Passo 1 da demonstração

1. Vamos dar uma olhada na turbina eólica **W7** na **Contoso fábrica 1**.  

    * **Ação**: Atualizar o intervalo de visualização para **1/1/17 00:20 para 3/10/17 20:00 (UTC)**, adicione o **Contoso fábrica 1** > **W7** > **gerador de sistema**   >  **GeneratorSpeed** valores de sensor e, em seguida, apresentar resultante.

       ![Início rápido, um][1]

1. Recentemente, o Contoso encontrados de incêndio na turbina eólica **W7**. Vejamos com mais detalhes aqui. Podemos ver que o sensor de alerta de fogo foi ativado durante a acionar.

    * **Ação**: Atualizar o intervalo de visualização para **3/9/17 00:20 para 3/10/17 20:00 (UTC)** e adicione o **sistema de segurança** > **FireAlert** sensor.

      ![Início rápido dois][2]

1. Vamos ver o que mais tenham acontecido perto da hora a acionar. A pressão de petróleo e avisos ativos pelo apenas antes do fire mas naquela época, era muito tarde para evitar o problema.

    * **Ação**: Adicionar a **sistema de argumento de venda** > **HydraulicOilPressure** sensor e a **Pitch sistema** > **ActiveWarning**sensor.

      ![Início rápido três][3]

1. Se Vamos reduzir, podemos ver havia sinais que levou à acionar. Variou entre ambos os sensores. Então, aconteceu antes deste problema?

    * **Ação**: Atualizar o intervalo de visualização para **2/24/17 00:20 para 3/10/17 20:00 (UTC)**.

      ![Início rápido quatro][4]

1. Se examinar os todos dois anos de dados, podemos ver um evento de fogo anterior com o mesmo inicia de sessão. Com esses dados, podemos criar sistemas para capturar problemas assim um desde o início.

    * **Ação**: Atualizar o intervalo de visualização para **1/1/16, a 12/31/17** (todos os dados).

       ![Início rápido cinco][5]

### <a name="demo-step-2"></a>Passo 2 da demonstração

1. Outros problemas são mais sutis e mais difícil de diagnosticar. O Time Series Insights fornece uma variedade de formas de ajudar-na rastrear problemas difíceis. Aqui podemos ver uma interrupção do sensor de aviso no **W6** nos **6/25**. Mas o que realmente está acontecendo?

    * **Ação**: Remover os sensores atuais, atualize o intervalo de visualização para **6/1/17 20:00 para 7/1/17 20:00 (UTC)** e, em seguida, adicione o **Contoso fábrica 1** > **W6**  >  **Sistema de segurança** > **VoltageActuatorSwitchWarning** sensor.

       ![Início rápido seis][6]

1. O aviso indica um problema com a tensão que está a ser o resultado pelo gerador de. Mas o que é a causa? A eficiência geral de saída o gerador parece bem num intervalo granular. Mas ao agregar os dados, podemos ver uma entrega definitiva.

    * **Ação**: Remover o **VoltageActuatorSwitchWarning** sensor, adicione o **gerador de sistema** > **ActivePower** sensor e atualizar o intervalo para **3d**.

       ![Início rápido sete][7]

1. Se, navegar para a frente do conjunto de dados, podemos ver que este problema não é transitório. Ele continua.

    * **Ação**: Expanda o intervalo de tempo para a direita.

       ![Início rápido oito][8]

1. Vamos explorar ainda mais. Podemos adicionar outros pontos de dados de sensor para ver a tensão por fase. Mas os pontos de dados todos os procurar comparáveis. Vamos remover um marcador para ver os valores reais. Parece que existe um problema com a saída de fase 3.

    * **Ação**: Adicione **sistema Generator** > **GridVoltagePhase1**, **GridVoltagePhase2**, e **GridVoltagePhase3** sensores. Remova um marcador no último ponto de dados na área visível.

       ![Início rápido oito][8]

1. Se todos os pontos de dados de três podemos ver na mesma escala, a entrega de fase 3 aparece ainda mais óbvia. Neste momento, estamos prontos para fazer referência a nossa equipa de manutenção com uma oportunidade potencial boa sobre a causa do aviso o problema.  

    * **Ação**: Atualize a apresentação de colocar todos os sensores na mesma escala de gráfico.

       ![Início rápido nove][9]

## <a name="next-steps"></a>Passos Seguintes

Está pronto para criar seu próprio ambiente de pré-visualização do Time Series Insights:

> [!div class="nextstepaction"]
> [Planejar seu ambiente de pré-visualização do Time Series Insights](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png
