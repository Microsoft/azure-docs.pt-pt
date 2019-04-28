---
title: Utilizar alertas e corrigir problemas de dispositivos na solução de monitorização remota - Azure | Microsoft Docs
description: Este tutorial mostra como Utilizar alertas para identificar e corrigir problemas com dispositivos ligados ao acelerador de soluções de Monitorização Remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1cd1eb9a0bd4b8457ea82303a747acb2553ab707
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61452668"
---
# <a name="tutorial-troubleshoot-and-fix-device-issues"></a>Tutorial: Resolver problemas e corrigir erros do dispositivo

Neste tutorial, vai utilizar o acelerador de soluções de Monitorização Remota para identificar e corrigir problemas com os seus dispositivos IoT ligados. Utilize alertas no dashboard do acelerador de soluções para identificar problemas e, em seguida, executar tarefas remotas para corrigir esses problemas.

A Contoso está a testar um novo dispositivo **Protótipo** no campo. Enquanto operador da Contoso, observe durante os testes que o dispositivo **Protótipo** está a acionar um alerta de temperatura inesperadamente no dashboard. Agora deve investigar o comportamento deste dispositivo **Protótipo** com falhas e resolver o problema.

Neste tutorial:

>[!div class="checklist"]
> * Investigar um alerta de um dispositivo
> * Resolver o problema do dispositivo

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="investigate-an-alert"></a>Investigar um alerta

Na página **Dashboard**, observe que existem alertas de temperatura inesperados provenientes da regra associada aos dispositivos **Protótipo**:

[![Alertas apresentados no dashboard](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

Para investigar melhor o problema, escolha a opção **Explorar Alerta** junto ao alerta:

[![Explorar alerta do dashboard](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

A vista de detalhe do alerta mostra:

* Quando o alerta foi acionado
* Informações de estado sobre os dispositivos associados ao alerta
* Telemetria dos dispositivos associados ao alerta

[![Detalhes do alerta](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

Para reconhecer o alerta, selecione todas as **Ocorrências de alerta** e escolha **Reconhecer**. Esta ação permite que outros operadores saibam que viu o alerta e está a tentar resolver o problema:

[![Reconhecer os alertas](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

Ao reconhecer o alerta, o estado da ocorrência é alterado para **Reconhecido**.

Na lista de dispositivos com alertas, pode ver o dispositivo **Protótipo** responsável por acionar o alerta de temperatura do dispositivo:

[![Listar os dispositivos que causam o alerta](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>Resolver o problema

Para resolver o problema com o dispositivo **Protótipo**, terá de chamar o método **DecreaseTemperature** no dispositivo.

Para tomar decisões sobre um dispositivo, selecione-o na lista de dispositivos com alertas e, em seguida, selecione **Tarefas**. O modelo do dispositivo **Protótipo** suporta seis métodos:

[![Ver os métodos que o dispositivo suporta](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

Escolha **DecreaseTemperature** e defina o nome da tarefa como **DecreaseTemperature**. Em seguida, clique em **Aplicar**:

[![Criar a tarefa para diminuir a temperatura](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

Para controlar o estado da tarefa, clique em **Ver estado da tarefa**. Utilize a vista **Tarefas** para controlar todas as tarefas e chamadas de método na solução:

[![Monitorizar a tarefa para diminuir a temperatura](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

Pode verificar que a temperatura do dispositivo diminuiu ao ver a telemetria na página **Dashboard**:

[![Ver a diminuição na temperatura](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Este tutorial mostrou-lhe como utilizar os alertas para identificar problemas com os seus dispositivos e como agir sobre estes dispositivos para resolver os problemas. Para saber como ligar um dispositivo real do solution accelerator, avance para os artigos de procedimentos.

Agora que aprendeu a gerir problemas de dispositivos, o próximo passo sugerido é saber como [Ligar o seu dispositivo ao acelerador de soluções de Monitorização Remota](iot-accelerators-connecting-devices.md).
