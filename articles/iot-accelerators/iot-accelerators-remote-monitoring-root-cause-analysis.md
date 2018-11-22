---
title: Realizar uma análise da causa raiz num alerta – Azure | Microsoft Docs
description: Neste tutorial, irá aprender a realizar uma análise da causa raiz num alerta com o Azure Time Series Insights.
author: aditidugar
ms.author: adugar
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 70d29359d4a4bcf9f5badbbf0c553d7bed88a02b
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284574"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>Tutorial: Realizar uma análise da causa raiz num alerta

Neste tutorial, irá aprender a utilizar o acelerador de soluções da Monitorização Remota para diagnosticar a causa raiz de um alerta. Verá que um alerta foi acionado no dashboard da solução de Monitorização Remota e, em seguida, utilizará o explorador do Azure Time Series Insights para investigar a causa raiz.

O tutorial utiliza dois dispositivos simulados de camiões de entregas que enviam telemetria de localização, altitude, velocidade e de temperatura da carga. Os camiões são geridos por uma organização chamada Contoso e estão ligados ao acelerador de soluções de Monitorização Remota. Enquanto operador da Contoso, tem de perceber por que razão um dos seus camiões (delivery-truck-02) registou um alerta de temperatura baixa.

Neste tutorial:

>[!div class="checklist"]
> * Filtrar os dispositivos no dashboard
> * Ver telemetria em tempo real
> * Explorar dados no explorador do Time Series Insights
> * Realizar uma análise da causa raiz
> * Criar uma nova regra com base no que aprendeu

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Selecionar os dispositivos a apresentar

Para selecionar os dispositivos ligados que são apresentados na página **Dashboard**, utilize os filtros. Para apresentar apenas os dispositivos de **Camião**, escolha o filtro **Camiões** incorporado na lista pendente de filtros:

[![Filtrar camiões no dashboard](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

Quando aplicar um filtro, apenas os dispositivos que coincidam com as condições de filtro são apresentados no mapa e no painel de telemetria sobre o **Dashboard**. Pode ver que existem dois camiões ligados ao acelerador de soluções, incluindo o **truck-02**.

## <a name="view-real-time-telemetry"></a>Ver telemetria em tempo real

O acelerador de soluções rastreia a telemetria em tempo real no gráfico na página **Dashboard**. Por predefinição, o gráfico mostra a telemetria de altitude, que varia ao longo do tempo:

[![Gráfico de telemetria da altitude do camião](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

Para ver a telemetria de temperatura dos camiões, clique em **Temperatura** no **Painel de Telemetria**. Pode ver como a temperatura dos dois camiões variou durante os últimos 15 minutos. Também pode ver que foi acionado um alerta de temperatura baixa para o delivery-truck-02 no painel de alertas.

[![Dashboard de RM com alerta de temperatura baixa](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>Explorar os dados

Para identificar a causa do alarme de temperatura baixa, abra os dados telemétricos do camião de entregas no explorador do Time Series Insights. Clique em qualquer uma das ligações **Explorar no Time Series Insights** no dashboard:

[![Dashboard de RM com as ligações do TSI destacadas](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

Quando inicia o explorador, vê uma lista de todos os seus dispositivos:

[![Vista inicial do Explorador do TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Filtrar os dispositivos, escrevendo **camião de entregas** na caixa do filtro e selecione **temperatura** como o **medida** no painel à esquerda:

[![Temperatura do camião no Explorador do TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

Consulte a vista mesmo que viu no dashboard de monitorização remota. Além disso, pode agora ampliar mais próximo para o intervalo de tempo que o alerta foi acionado em:

[![Ampliação no Explorador do TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

Também pode adicionar outros fluxos de telemetria provenientes dos camiões. Clique nas **adicionar** botão no canto superior esquerdo. É apresentado um novo painel:

[![Explorador do TSI com novo painel](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

No novo painel, mude o nome da nova etiqueta para **Dispositivos**, para que corresponda à anterior. Selecione **altitude** como o **medida** e **iothub-ligação-dispositivo-id** como o **dividido por** valor a adicionar a telemetria em altitude em a vista:

[![Explorador do TSI com temperatura e altitude](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>Diagnosticar o alerta

Quando examinar os fluxos na vista atual, pode ver que os perfis de altitude para os dois trucks são diferentes. Além disso, a queda de temperatura no **delivery-truck-02** ocorre quando o camião atinge uma altitude elevada. Esta informação é inesperada, porque foi atribuída a mesma rota aos dois camiões.

Para confirmar a suspeita de que os camiões estão a fazer percursos diferentes, adicione outro painel ao painel lateral com o botão **Adicionar**. No novo painel, mude o nome da nova etiqueta para **Dispositivos**, para que corresponda à anterior. Selecione **longitude** como a **Medida** e **iothub-connection-device-id** como o valor **Dividir Por** para adicionar a telemetria de longitude à sua vista. Pode ver que os camiões fizeram percursos diferentes ao observar a diferença entre os fluxos de **longitude**:

[![Explorador do TSI com temperatura, altitude e longitude](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Criar uma nova regra

Embora as rotas de camião normalmente são otimizadas com antecedência, percebe que padrões de tráfego, meteorologia e outros eventos imprevisíveis podem causar atrasos e deixar o último minuto decisões de rota para drivers de camião com base em seu bom senso. No entanto, uma vez que a temperatura de seus ativos de dentro do veículo é essencial, deve criar uma regra adicional na sua solução de monitorização remota. Esta regra é garantir que receba um aviso caso a altitude média ao longo de um intervalo de 1 minuto mais pés 350:

[![Separador de regras da Monitorização Remota para definir uma regra de altitude](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

Para saber como criar e editar regras, consulte o tutorial anterior sobre como [detetar problemas de dispositivo](iot-accelerators-remote-monitoring-automate.md).

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar o explorador do Time Series Insights com o acelerador de soluções da Monitorização Remota para diagnosticar a causa raiz de um alerta. Para saber como utilizar o acelerador de soluções para identificar e corrigir problemas com os seus dispositivos ligados, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Utilizar alertas de dispositivos para identificar e corrigir problemas com os dispositivos ligados à sua solução de monitorização](iot-accelerators-remote-monitoring-maintain.md)
