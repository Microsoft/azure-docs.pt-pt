---
title: Configurar dispositivos em Solução de Monitorização Remota - Azure / Microsoft Docs
description: Este tutorial mostra-lhe como configurar dispositivos ligados ao acelerador de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 432386809596fb2ef040a05d1fe0d12294a1abef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534527"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>Tutorial: Configurar dispositivos ligados à sua solução de monitorização

Neste tutorial, vai utilizar o acelerador de soluções de Monitorização Remota para configurar e gerir os seus dispositivos IoT ligados. Adicione um novo dispositivo ao acelerador de solução e configuure o dispositivo.

A Contoso encomendou novas máquinas para expandir uma das suas instalações. Enquanto aguarda pela entrega das novas máquinas, quer executar uma simulação para testar o comportamento da sua solução. Para executar a simulação, adicione um novo dispositivo simulado do motor ao acelerador de solução de monitorização remota e teste que este dispositivo simulado responde corretamente às atualizações de configuração. Enquanto este tutorial utiliza dispositivos simulados, um desenvolvedor de dispositivos pode implementar métodos diretos num [dispositivo real ligado ao acelerador de solução de monitorização remota](iot-accelerators-connecting-devices.md).

Neste tutorial:

>[!div class="checklist"]
> * Aprovisionar um dispositivo simulado.
> * Testar um dispositivo simulado.
> * Reconfigurar um dispositivo.
> * Organizar os seus dispositivos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Adicionar um dispositivo simulado

Navegue na página **Do Explorador de Dispositivos** na solução e clique **em + Novo dispositivo**:

[![Fornecimento de um dispositivo simulado](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

No painel **Novo dispositivo**, selecione **Simulado**, deixe o número de dispositivos a aprovisionar em **1**, selecione o modelo de dispositivo **Motor com Falhas** e, em seguida, selecione **Aplicar** para criar o dispositivo simulado:

[![Fornecimento de um dispositivo de motor simulado](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Testar o dispositivo simulado

Para testar o seu dispositivo de motor simulado está a enviar valores de telemetria e de reporte de propriedade, selecione-o na lista de dispositivos na página **Device Explorer.** São apresentadas informações em direto sobre o motor no painel **Detalhes do Dispositivo**:

[![Veja o novo dispositivo simulado do motor](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

Em **Detalhes do Dispositivo**, verifique se o dispositivo novo está a enviar telemetria. Para ver o fluxo de telemetria de vibração do dispositivo, clique em **Vibração**:

[![Selecione um fluxo de telemetria para visualizar](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

O painel **Detalhes do Dispositivo** apresenta outras informações sobre o dispositivo, como valores de etiqueta, os métodos que suporta e as propriedades comunicadas pelo dispositivo.

Para ver diagnósticos detalhados, desloque-se para baixo no painel **Detalhes do Dispositivo** para ver a secção **Diagnóstico**.

## <a name="reconfigure-a-device"></a>Reconfigurar um dispositivo

Para testar que pode atualizar as propriedades de configuração do motor, selecione-o na lista de dispositivos na página **Device Explorer.** Em seguida, clique em **Jobs**e, em seguida, escolha **Propriedades**. O painel de tarefas mostra os valores de propriedade atualizáveis para o dispositivo selecionado:

[![Reconfigurar um dispositivo](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Para atualizar a localização do motor, defina o nome da tarefa como **UpdateEngineLocation**, defina a longitude como **-122,15**, defina a localização como **Fábrica 2**, defina a latitude como **47,62** e clique em **Aplicar**:

[![Screenshot que mostra a página "Device Explorer" com a janela "Jobs" realçada.](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Para controlar o estado da tarefa, clique em **Ver estado da tarefa**:

[![Atualizar o valor da propriedade do dispositivo](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Depois de concluída a tarefa, navegue para a página **Dashboard**. O dispositivo de motor é apresentado no mapa na sua nova localização:

[![Ver localização do motor](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Organizar os seus dispositivos

Para facilitar a organização e a gestão dos seus dispositivos por um operador, pode etiquetá-los com um nome de equipa. A Contoso tem duas equipas diferentes para atividades de serviço de campo:

* A equipa Smart Vehicle gere camiões e dispositivos para criação de protótipos.
* A equipa Smart Building gere chillers, elevadores e motores.

Para exibir todos os seus dispositivos, navegue na página **Device Explorer** e escolha o filtro Todos **os dispositivos:**

[![Mostrar todos os dispositivos](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Adicionar etiquetas

Selecione todos os **Camiões** e dispositivos para **Criação de protótipos**. Em seguida, clique em **Jobs**.

No painel **Tarefas**, selecione **Etiqueta**, defina o nome da tarefa como **AddConnectedVehicleTag** e, em seguida, adicione uma etiqueta de texto denominada **FieldService** com um valor **ConnectedVehicle**. Em seguida, clique em **Aplicar**:

[![Adicione etiqueta ao protótipo e dispositivos de caminhão](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Na página do dispositivo, selecione todos os dispositivos **Chiller**, **Elevador** e **Motor**. Em seguida, clique em **Jobs**.

No painel **Tarefas**, selecione **Etiqueta**, defina o nome da tarefa como **AddSmartBuildingTag** e, em seguida, adicione uma etiqueta de texto denominada **FieldService** com um valor **SmartBuilding**. Em seguida, clique em **Aplicar**:

[![Adicione etiqueta aos dispositivos de refrigeração, elevador e motor](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Criar filtros

Agora, pode utilizar os valores de etiqueta para criar filtros. Na página Do Explorador de **Dispositivos,** clique **em Gerir grupos de dispositivos:**

[![Gerir grupos de dispositivos](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Crie um filtro de texto que utilize o nome de etiqueta **FieldService** e o valor **SmartBuilding** na condição. Guarde o filtro como **Smart Building**:

[![Criar filtro de construção inteligente](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Crie um filtro de texto que utilize o nome de etiqueta **FieldService** e o valor **ConnectedVehicle** na condição. Guarde o filtro como **Connected Vehicle**.

[![Screenshot que mostra a página "Device Explorer" com a janela "Gerir grupos de dispositivos" realçada.](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Agora, o operador da Contoso pode consultar dispositivos com base na equipa operacional:

[![Criar filtro de veículo conectado](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Este tutorial mostrou como configurar e gerir os dispositivos ligados ao acelerador de soluções de Monitorização Remota. Para saber como utilizar o acelerador de soluções para efetuar uma análise da causa raiz num alerta inesperado, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Realizar uma análise da causa raiz num alerta](iot-accelerators-remote-monitoring-root-cause-analysis.md)
