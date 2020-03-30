---
title: incluir ficheiro
description: incluir ficheiro
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/31/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: abbde0cb8137650faad563248c8fafe7dc0f469a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75564813"
---
Uma das principais capacidades do Azure IoT Edge é conseguir implementar código para os seus dispositivos IoT Edge a partir da nuvem. Os **módulos IoT Edge** são pacotes executáveis implementados como recipientes. Nesta secção, você implementa um módulo pré-construído a partir da [secção módulos IoT Edge do Mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) diretamente do seu Hub Azure IoT.

O módulo que implementa nesta secção simula um sensor e envia dados gerados. Este módulo é um código útil quando está a começar com o IoT Edge porque pode utilizar os dados simulados para desenvolvimento e teste. Se quiser ver exatamente o que este módulo faz, pode ver o código fonte do sensor de [temperatura simulado](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Para implementar o seu primeiro módulo a partir do Mercado Azure, utilize os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

1. A partir do menu no painel esquerdo, sob **Gestão automática**de dispositivos, selecione **IoT Edge**.

1. Clique na identificação do dispositivo-alvo do dispositivo-alvo a partir da lista de dispositivos.

1. Na barra superior, selecione **Módulos de Conjunto**.

1. Na secção **Módulos IoT Edge** da página, clique em **Adicionar**.

1. A partir do menu suspenso, selecione **Módulo Marketplace**.

   ![Sensor de Temperatura Simulada na pesquisa do portal do Azure](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. No Mercado do **Módulo IoT Edge,** procure "Sensor de Temperatura Simulada" e selecione esse módulo.

1. Note que o módulo SimuladoTemperatureSensor é auto-povoado. Nos tutoriais, utiliza esta página para adicionar módulos adicionais à sua implementação. Para este arranque rápido, basta implementar este módulo. Não são necessárias credenciais porque é pública.

   ![Definir módulos no dispositivo](./media/iot-edge-deploy-module/set-modules-on-device.png)

   Selecione **Seguinte: Rotas** para continuar até ao próximo passo do assistente.

1. No separador **Rotas** do assistente, define como as mensagens são passadas entre os módulos e o Hub IoT. As mensagens são construídas utilizando pares de nome/valor. Para o arranque rápido, você quer que todas as mensagens`$upstream`de todos os módulos vão para IoT Hub ( ). Se não estiver povoado automaticamente, adicione o seguinte código para o **valor** do **nome:** `upstream`

   ```sql
    FROM /messages/* INTO $upstream
   ```

   Selecione **Seguinte: Rever + criar** para continuar até ao próximo passo do assistente.

1. No **separador Review + criar** o separador do assistente, pode pré-visualizar o ficheiro JSON que define todos os módulos que são implantados no seu dispositivo IoT Edge. Note que o módulo **SimuladoTemperatureSensor** está incluído, e dois módulos adicionais de sistema **chamados edgeAgent** e **edgeHub**. Selecione **Criar** quando terminar de rever.

   Quando submete uma implementação nova para um dispositivo do IoT Edge, nada é enviado para o seu dispositivo. Em vez disso, o dispositivo consulta regularmente o Hub IoT para obter novas instruções. Se o dispositivo encontrar um manifesto de implementação atualizado, este irá utilizar as informações sobre a nova implementação para obter as imagens do módulo da cloud e, em seguida, começa a executar os módulos localmente. Este processo pode demorar alguns minutos.

1. Depois de criar os detalhes de implementação do módulo, o assistente devolve-o à página **IoT Edge** do seu hub IoT. Selecione o seu dispositivo da lista de dispositivos de IoT Edge para ver os respetivos detalhes.

1. Na página de detalhes do dispositivo, desloque-se para baixo até ao separador **Módulos.** Devem ser listados três módulos: $edgeAgent, $edgeHub e Sensor simulado de Temperatura. Se um ou mais dos módulos estiverem listados conforme especificado na implementação, mas não reportados pelo dispositivo, o dispositivo IoT Edge ainda os está a iniciar. Aguarde alguns momentos e selecione **Refresh** no topo da página.

   ![Ver Sensor de Temperatura Simulado na lista de módulos implantados](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
