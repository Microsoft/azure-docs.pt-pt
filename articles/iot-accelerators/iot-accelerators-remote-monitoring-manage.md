---
title: Configurar dispositivos um remoto tutorial de solução de monitorização - Azure | Documentos da Microsoft
description: Este tutorial mostra-lhe como configurar dispositivos ligados ao acelerador de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: d23b7c8fa10127094fec67535333ae169f0f38f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183395"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>Tutorial: Configurar dispositivos ligados à sua solução de monitorização

Neste tutorial, vai utilizar o acelerador de soluções de Monitorização Remota para configurar e gerir os seus dispositivos IoT ligados. Adicionar um novo dispositivo para o solution accelerator e configurar o dispositivo.

A Contoso encomendou novas máquinas para expandir uma das suas instalações. Enquanto aguarda pela entrega das novas máquinas, quer executar uma simulação para testar o comportamento da sua solução. Para executar a simulação, adicione um novo dispositivo simulado motor para o solution accelerator do monitoramento remoto e o teste que esse dispositivo responde corretamente às atualizações de configuração. Embora este tutorial utiliza dispositivos simulados, um desenvolvedor de dispositivo pode implementar os métodos diretos num [dispositivo real ligado para o acelerador de solução de monitorização remota](iot-accelerators-connecting-devices.md).

Neste tutorial:

>[!div class="checklist"]
> * Aprovisionar um dispositivo simulado.
> * Testar um dispositivo simulado.
> * Reconfigurar um dispositivo.
> * Organizar os seus dispositivos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Adicionar um dispositivo simulado

Navegue para o **Device Explorer** página na solução e, em seguida, clique em **+ novo dispositivo**:

[![Aprovisionar um dispositivo simulado](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

No painel **Novo dispositivo**, selecione **Simulado**, deixe o número de dispositivos a aprovisionar em **1**, selecione o modelo de dispositivo **Motor com Falhas** e, em seguida, selecione **Aplicar** para criar o dispositivo simulado:

[![Aprovisionar um dispositivo de motor simulado](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Testar o dispositivo simulado

Para testar seu mecanismo de simulado dispositivo está a enviar telemetria e de geração de relatórios de valores de propriedades, selecione-o na lista de dispositivos no **Device Explorer** página. São apresentadas informações em direto sobre o motor no painel **Detalhes do Dispositivo**:

[![Ver o novo dispositivo de motor simulado](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

Em **Detalhes do Dispositivo**, verifique se o dispositivo novo está a enviar telemetria. Para ver o fluxo de telemetria de vibração do dispositivo, clique em **Vibração**:

[![Selecionar um fluxo de telemetria para visualização](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

O painel **Detalhes do Dispositivo** apresenta outras informações sobre o dispositivo, como valores de etiqueta, os métodos que suporta e as propriedades comunicadas pelo dispositivo.

Para ver diagnósticos detalhados, desloque-se para baixo no painel **Detalhes do Dispositivo** para ver a secção **Diagnóstico**.

## <a name="reconfigure-a-device"></a>Reconfigurar um dispositivo

Para testar o que é possível atualizar as propriedades de configuração do mecanismo, selecione-o na lista de dispositivos no **Device Explorer** página. Em seguida, clique em **trabalhos**e, em seguida, escolha **propriedades**. O painel de tarefas mostra os valores de propriedade atualizáveis para o dispositivo selecionado:

[![Reconfigurar um dispositivo](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Para atualizar a localização do motor, defina o nome da tarefa como **UpdateEngineLocation**, defina a longitude como **-122,15**, defina a localização como **Fábrica 2**, defina a latitude como **47,62** e clique em **Aplicar**:

[![Atualizar um valor de propriedade do dispositivo](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Para controlar o estado da tarefa, clique em **Ver estado da tarefa**:

[![Atualizar um valor de propriedade do dispositivo](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Depois de concluída a tarefa, navegue para a página **Dashboard**. O dispositivo de motor é apresentado no mapa na sua nova localização:

[![Ver localização do motor](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Organizar os seus dispositivos

Para facilitar a organização e a gestão dos seus dispositivos por um operador, pode etiquetá-los com um nome de equipa. A Contoso tem duas equipas diferentes para atividades de serviço de campo:

* A equipa Smart Vehicle gere camiões e dispositivos para criação de protótipos.
* A equipa Smart Building gere chillers, elevadores e motores.

Para apresentar todos os seus dispositivos, navegue para o **Device Explorer** página e escolha o **todos os dispositivos** filtro:

[![Ver todos os dispositivos](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Adicionar etiquetas

Selecione todos os **Camiões** e dispositivos para **Criação de protótipos**. Em seguida, clique em **Tarefas**.

No painel **Tarefas**, selecione **Etiqueta**, defina o nome da tarefa como **AddConnectedVehicleTag** e, em seguida, adicione uma etiqueta de texto denominada **FieldService** com um valor **ConnectedVehicle**. Em seguida, clique em **Aplicar**:

[![Adicionar uma etiqueta a camiões e dispositivos protótipos](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Na página do dispositivo, selecione todos os dispositivos **Chiller**, **Elevador** e **Motor**. Em seguida, clique em **Tarefas**.

No painel **Tarefas**, selecione **Etiqueta**, defina o nome da tarefa como **AddSmartBuildingTag** e, em seguida, adicione uma etiqueta de texto denominada **FieldService** com um valor **SmartBuilding**. Em seguida, clique em **Aplicar**:

[![Adicionar uma etiqueta a dispositivos chiller, elevador e motor](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Criar filtros

Agora, pode utilizar os valores de etiqueta para criar filtros. Sobre o **Device Explorer** página, clique em **gerir grupos de dispositivos**:

[![Gerir grupos de dispositivos](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Crie um filtro de texto que utilize o nome de etiqueta **FieldService** e o valor **SmartBuilding** na condição. Guarde o filtro como **Smart Building**:

[![Criar filtro smart building](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Crie um filtro de texto que utilize o nome de etiqueta **FieldService** e o valor **ConnectedVehicle** na condição. Guarde o filtro como **Connected Vehicle**.

[![Criar filtro connected vehicle](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Agora, o operador da Contoso pode consultar dispositivos com base na equipa operacional:

[![Criar filtro connected vehicle](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Este tutorial mostrou como configurar e gerir os dispositivos ligados ao acelerador de soluções de Monitorização Remota. Para saber como utilizar o acelerador de soluções para efetuar uma análise da causa raiz num alerta inesperado, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Realizar uma análise da causa raiz num alerta](iot-accelerators-remote-monitoring-root-cause-analysis.md)
