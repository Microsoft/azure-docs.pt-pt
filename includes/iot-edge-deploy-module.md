---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 76c8eb7acf20d8cf68d3573defd947efbc6c3c43
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801741"
---
Uma das principais capacidades do Azure IoT Edge é conseguir implementar código nos seus dispositivos IoT Edge a partir da nuvem. *Os módulos IoT Edge* são pacotes executáveis implementados como recipientes. Nesta secção, você implanta um módulo pré-construído a partir da [secção IoT Edge Modules do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) diretamente do seu Azure IoT Hub.

O módulo que implementa nesta secção simula um sensor e envia dados gerados. Este módulo é um código útil quando está a começar com o IoT Edge porque pode usar os dados simulados para desenvolvimento e teste. Se quiser ver exatamente o que este módulo faz, pode ver o [código-fonte do sensor de temperatura simulado](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Para implementar o seu primeiro módulo a partir do Mercado Azure, utilize os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

1. A partir do menu no painel esquerdo, em **Gestão Automática de Dispositivos,** selecione **IoT Edge**.

1. Clique no ID do dispositivo do dispositivo alvo a partir da lista de dispositivos.

1. Na barra superior, selecione **Módulos de Conjunto**.

   ![Selecione Definir Módulos a partir da página de detalhes do dispositivo](./media/iot-edge-deploy-module/select-set-modules.png)

1. Na secção **módulos IoT Edge da** página, clique em **Adicionar** e selecione Módulo **de Mercado** a partir do menu suspenso.

   ![Adicionar módulo de mercado](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. No Mercado do **Módulo IoT Edge,** procure "Sensor de Temperatura Simulado" e selecione esse módulo.

1. Note que o módulo SimuladoSteperatureSensor é adicionado à secção módulos IoT Edge Modules, com o estado desejado **em funcionamento**.

   Selecione **Seguinte: Rotas** para continuar até ao próximo passo do assistente.

   ![Passe para o próximo passo assim que o módulo sensor de temperatura estiver listado](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. No separador **Rotas** do assistente, pode definir como as mensagens são passadas entre os módulos e o Hub IoT. As rotas são construídas com par de nome/valor. Devia ver duas rotas nesta página. A rota padrão chamada **rota** envia todas as mensagens para IoT Hub (que é chamado `$upstream` de ). Uma segunda rota chamada **SimulatedTemperatureSensorToIoTHub** foi criada automaticamente quando adicionou o módulo do Marketplace. Esta rota envia todas as mensagens especificamente do módulo de temperatura simulado para o IoT Hub. Pode apagar a rota padrão porque é redundante neste caso.

   Selecione **Seguinte: Revisão + criar** para continuar até ao próximo passo do assistente.

   ![Elimine a rota predefinida e, em seguida, passar para o passo seguinte](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. No **separador 'Rever +' criar** o separador do assistente, pode pré-visualizar o ficheiro JSON que define todos os módulos que são implantados no seu dispositivo IoT Edge. Note que o módulo **SimulaedTemperatureSensor** está incluído, bem como os dois módulos de tempo de execução, **edgeAgent** e **edgeHub**. **Selecione Criar** quando terminar de rever.

   Quando submete uma implementação nova para um dispositivo do IoT Edge, nada é enviado para o seu dispositivo. Em vez disso, o dispositivo consulta regularmente o Hub IoT para obter novas instruções. Se o dispositivo encontrar um manifesto de implementação atualizado, este irá utilizar as informações sobre a nova implementação para obter as imagens do módulo da cloud e, em seguida, começa a executar os módulos localmente. Este processo pode demorar alguns minutos.

1. Depois de criar os detalhes da implementação do módulo, o assistente devolve-o à página de detalhes do dispositivo. Na página de detalhes do dispositivo, veja o estado de implantação no **separador Módulos.** Devem ser listados três módulos: $edgeAgent, $edgeHub e SimulaçãoSteperatureSensor. Se um ou mais dos módulos estiverem listados como especificado na implementação, mas não reportados pelo dispositivo, o seu dispositivo IoT Edge ainda os está a iniciar. Aguarde alguns momentos e **selecione Refresh** no topo da página.

   ![Ver SimulaçãoSteperatureSensor na lista de módulos implantados](./media/iot-edge-deploy-module/view-deployed-modules.png)
