---
title: incluir ficheiro
description: incluir ficheiro
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 6b9ec2017ffa5d4e4148b441aa23ed2eca6ee8b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99628940"
---
Uma das principais capacidades do Azure IoT Edge é a implementação de código para os seus dispositivos IoT Edge a partir da nuvem. *Os módulos IoT Edge* são pacotes executáveis implementados como recipientes. Nesta secção, irá implantar um módulo pré-construído a partir da [secção IoT Edge Modules do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) diretamente do Azure IoT Hub.

O módulo que implementa nesta secção simula um sensor e envia dados gerados. Este módulo é um código útil quando está a começar com o IoT Edge porque pode usar os dados simulados para desenvolvimento e teste. Se quiser ver exatamente o que este módulo faz, pode ver o [código-fonte do sensor de temperatura simulado](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Siga estes passos para implementar o seu primeiro módulo a partir do Azure Marketplace.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e vá para o seu hub IoT.

1. A partir do menu à esquerda, em **Gestão Automática de Dispositivos,** selecione **IoT Edge**.

1. Selecione o ID do dispositivo do dispositivo alvo na lista de dispositivos.

1. Na barra superior, selecione **Módulos de Conjunto**.

   ![Screenshot que mostra a seleção de Módulos de Conjunto.](./media/iot-edge-deploy-module/select-set-modules.png)

1. Nos **Módulos IoT Edge,** abra o menu **Add** drop-down e, em seguida, selecione **o Módulo de Mercado**.

   ![Screenshot que mostra o menu Add drop-down.](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. No **IoT Edge Module Marketplace,** procure e selecione o `Simulated Temperature Sensor` módulo.

   O módulo é adicionado à secção IoT Edge Modules com o estado de **funcionamento** pretendido.

1. Selecione **Seguinte: Rotas** para continuar até ao próximo passo do assistente.

   ![Screenshot que mostra continuar para o próximo passo após a adição do módulo.](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. No separador **Rotas,** remova a rota padrão, **rota** e, em seguida, selecione **Seguinte: Review + create** para continuar para o próximo passo do assistente.

   >[!Note]
   >As rotas são construídas utilizando pares de nome e valor. Devia ver duas rotas nesta página. A rota padrão, **rota,** envia todas as mensagens para ioT Hub (que é chamado `$upstream` de ). Uma segunda rota, **SimulatedTemperatureSensorToIoTHub,** foi criada automaticamente quando adicionou o módulo do Azure Marketplace. Esta rota envia todas as mensagens do módulo de temperatura simulada para o IoT Hub. Pode apagar a rota padrão porque é redundante neste caso.

   ![Screenshot que mostra remover a rota padrão e depois passar para o passo seguinte.](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. Reveja o ficheiro JSON e, em seguida, **selecione Criar**. O ficheiro JSON define todos os módulos que implementa no seu dispositivo IoT Edge. Você verá o módulo **SimulaedTemperatureSensor** e os dois módulos de tempo de execução, **edgeAgent** e **edgeHub**.

   >[!Note]
   >Quando submete uma implementação nova para um dispositivo do IoT Edge, nada é enviado para o seu dispositivo. Em vez disso, o dispositivo consulta regularmente o Hub IoT para obter novas instruções. Se o dispositivo encontrar um manifesto de implementação atualizado, este irá utilizar as informações sobre a nova implementação para obter as imagens do módulo da cloud e, em seguida, começa a executar os módulos localmente. Este processo pode demorar alguns minutos.

1. Depois de criar os detalhes da implementação do módulo, o assistente devolve-o à página de detalhes do dispositivo. Ver o estado de implantação no **separador Módulos.**

   Deverá ver três módulos: **$edgeAgent,** **$edgeHub** e **Simulação DetemperatureSensor**. Se um ou mais dos módulos tiver **SIM** em **ESPECIFICAÇÃO EM IMPLEMENTAÇÃO,** mas não sob **O DISPOSITIVO REPORTADO POR DISPOSITIVO,** o seu dispositivo IoT Edge ainda os está a iniciar. Espere alguns minutos e, em seguida, refresque a página.

   ![Screenshot que mostra sensor de temperatura simulado na lista de módulos implantados.](./media/iot-edge-deploy-module/view-deployed-modules.png)
