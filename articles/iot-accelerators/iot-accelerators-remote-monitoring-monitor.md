---
title: Monitorize os seus dispositivos IoT a partir de um tutorial de solução do Azure – Azure | Documentos da Microsoft
description: Neste tutorial irá aprender a monitorizar os seus dispositivos IoT com o acelerador de soluções de Monitorização Remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: d6d850fa8f896809318be77529e10abddaf6ea9a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58173826"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Tutorial: Monitorize os seus dispositivos IoT

Neste tutorial, vai utilizar o acelerador de soluções de Monitorização Remota para monitorizar os seus dispositivos IoT ligados. Utilize o dashboard de soluções para ver telemetria, informações do dispositivo, alertas e KPIs.

O tutorial utiliza dois dispositivos de camião simulados que enviam a localização, a velocidade e a telemetria de temperatura da carga. Os camiões são geridos por uma organização chamada Contoso e estão ligados ao acelerador de soluções de Monitorização Remota. Enquanto operador da Contoso, tem de monitorizar a localização e o comportamento de um dos camiões (camião-02) no campo.

Neste tutorial:

>[!div class="checklist"]
> * Filtrar os dispositivos no dashboard
> * Ver telemetria em tempo real
> * Ver detalhes do dispositivo
> * Ver alertas a partir dos seus dispositivos
> * Ver os KPIs do sistema

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Selecionar os dispositivos a apresentar

Para selecionar os dispositivos ligados que são apresentados na página **Dashboard**, utilize os filtros. Para apresentar apenas os dispositivos de **Camião**, escolha o filtro **Camiões** incorporado na lista pendente de filtros:

[![Filtrar camiões no dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Quando aplicar um filtro, apenas os dispositivos que correspondem às condições do filtro são apresentados no mapa e no painel de telemetria. Pode ver que existem dois camiões ligados ao acelerador de soluções, incluindo o camião-02:

[![São apresentados apenas os camiões no mapa](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Para criar, editar e eliminar filtros, clique em **Gerir grupos de dispositivos**.

## <a name="view-real-time-telemetry"></a>Ver telemetria em tempo real

O acelerador de soluções rastreia a telemetria em tempo real no gráfico na página **Dashboard**. A parte superior do gráfico de telemetria mostra os tipos de telemetria disponíveis para os dispositivos, incluindo o camião-02, selecionados pelo filtro atual. Por predefinição, o gráfico mostra a latitude dos camiões e o camião-02 parece estar estacionário:

[![Tipos de telemetria de camião](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Para ver a telemetria da temperatura dos camiões, clique em **Temperatura**. Pode ver como a temperatura do camião-02 tem variado durante a última hora:

[![Gráfico da telemetria de temperatura de camião](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>Ver o mapa

O mapa apresenta informações sobre os camiões simulados selecionados pelo filtro atual. Pode ampliar e fazer um deslocamento panorâmico do mapa para apresentar localizações com mais ou menos detalhe. A cor do ícone de um dispositivo no mapa indica se estão ativos **Alertas** (azul escuro) ou **Avisos** (vermelho) para o dispositivo. É apresentado um resumo do número de **Alertas** e **Avisos** à esquerda do mapa.

Para ver os detalhes do camião-02, faça um deslocamento panorâmico e amplie o mapa para localizá-lo e, em seguida, selecione o camião no mapa. Em seguida, clique na etiqueta do dispositivo para abrir o painel **Detalhes do dispositivo**. Os detalhes do dispositivo incluem:

* Valores de telemetria recentes
* Métodos que o dispositivo suporta
* Propriedades do dispositivo

[![Ver detalhes do dispositivo no dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Ver alertas

O painel **Alertas** mostra informações detalhadas sobre os alertas mais recentes dos seus dispositivos. Os alertas do camião-02 indicam uma temperatura de carga superior à normal:

[![Ver alertas do dispositivo no dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

Pode utilizar um filtro para ajustar o intervalo de tempo para os alertas recentes. Por predefinição, o painel apresenta alertas desde a última hora.

## <a name="view-the-system-kpis"></a>Ver os KPIs do sistema

A página **Dashboard** apresenta KPIs do sistema calculados pelo acelerador de soluções no painel **Análise**:

[![KPIs do Dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

O dashboard mostra três KPIs para os alertas selecionados pelos filtros atuais do dispositivo e do período de tempo:

* O número de alertas ativos para as regras que acionaram a maioria dos alertas.
* A proporção de alertas por tipo de dispositivo.
* A percentagem de alertas que são alertas críticos.

Para o camião-02, todos os alertas são avisos de temperatura de carga superior à normal.

Os mesmos filtros que definem o período de tempo para os alertas, e controlam os dispositivos que são apresentados, determinam como os KPIs são agregados. Por predefinição, o painel mostra KPIs agregados ao longo da última hora.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Este tutorial mostrou como utilizar a página **Dashboard** no acelerador de soluções da Monitorização Remota, para filtrar e monitorizar os camiões simulados. Para saber como utilizar o acelerador de soluções para detetar problemas com os seus dispositivos ligados, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Detetar problemas dos dispositivos ligados à sua solução de monitorização](iot-accelerators-remote-monitoring-automate.md)