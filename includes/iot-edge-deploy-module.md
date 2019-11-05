---
title: incluir ficheiro
description: incluir ficheiro
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/04/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 12661c77c6a950b482187b09e4465c964e6d6652
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494075"
---
Um dos principais recursos do Azure IoT Edge é a capacidade de implantar código em seus dispositivos IoT Edge da nuvem. **Módulos de IOT Edge** são pacotes executáveis implementados como contêineres. Nesta seção, você implantará um módulo predefinido da [seção módulos de IOT Edge do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). 

O módulo implantado nesta seção simula um sensor e envia os dados gerados. Esse módulo é uma parte útil do código quando você está começando a usar o IoT Edge, pois você pode utilizar os dados simulados para desenvolvimento e teste. Se você quiser ver exatamente o que esse módulo faz, você pode exibir o [código-fonte do sensor de temperatura simulado](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs). 

Para implantar seu primeiro módulo do Azure Marketplace, use as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com), insira o **sensor de temperatura simulada** na pesquisa e abra o resultado do Marketplace.

   ![Sensor de temperatura simulada na pesquisa portal do Azure](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Escolha um dispositivo IoT Edge para receber este módulo. Na página **dispositivos de destino para IOT Edge módulo** , forneça as seguintes informações:

   1. **Assinatura**: selecione a assinatura que contém o Hub IOT que você está usando.

   2. **Hub IOT**: selecione o nome do Hub IOT que você está usando.

   3. **Nome do dispositivo IOT Edge**: se você usou o nome do dispositivo sugerido anteriormente neste guia de início rápido, insira **myEdgeDevice**. Ou selecione **Localizar dispositivo** para escolher entre uma lista de dispositivos IOT Edge no Hub IOT. 
   
   4. Selecione **Criar**.

3. Agora que você escolheu um módulo de IoT Edge do Azure Marketplace e escolheu um dispositivo de IoT Edge para receber o módulo, você é levado para um assistente de três etapas que ajuda a definir exatamente como implantar o módulo. Na etapa **Adicionar módulos** do assistente, observe que o módulo **SimulatedTemperatureSensor** está preenchido automaticamente. Nos tutoriais, você usa essa página para adicionar mais módulos à sua implantação. Para este guia de início rápido, implante apenas este módulo. Selecione **Avançar** para continuar na próxima etapa do assistente.

4. Na etapa **especificar rotas** do assistente, você define como as mensagens são passadas entre os módulos e o Hub IOT. Para o início rápido, você deseja que todas as mensagens de todos os módulos acessem o Hub IoT (`$upstream`). Se não for preenchido automaticamente, adicione o seguinte código:

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```
   Em seguida, selecione **Seguinte**.

5. Na etapa **examinar a implantação** do assistente, você pode visualizar o arquivo JSON que define todos os módulos que são implantados em seu dispositivo IOT Edge. Observe que o módulo **SimulatedTemperatureSensor** está incluído e dois módulos de sistema adicionais chamados **edgeAgent** e **edgeHub**. Selecione **Enviar** quando terminar de revisar.

   Quando você envia uma nova implantação para um dispositivo IoT Edge, nada é enviado por push para seu dispositivo. Em vez disso, o dispositivo consulta o Hub IoT regularmente em busca de novas instruções. Se o dispositivo encontrar um manifesto de implantação atualizado, ele usará as informações sobre a nova implantação para efetuar pull das imagens de módulo da nuvem e, em seguida, começará a executar os módulos localmente. Esse processo pode levar alguns minutos. 

6. Depois de enviar os detalhes de implantação do módulo, o assistente o retornará à página **IOT Edge** do Hub IOT. Selecione seu dispositivo na lista de dispositivos IoT Edge para ver seus detalhes. 

7. Na página detalhes do dispositivo, role para baixo até a seção **módulos** . Três módulos devem ser listados: $edgeAgent, $edgeHub e SimulatedTemperatureSensor. Se um ou mais dos módulos estiverem listados como especificado na implantação, mas não forem relatados pelo dispositivo, seu dispositivo de IoT Edge ainda estará iniciando-os. Aguarde alguns instantes e selecione **Atualizar** na parte superior da página. 

   ![Exibir SimulatedTemperatureSensor na lista de módulos implantados](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
