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
ms.openlocfilehash: c20a14ef2bc74d73b93ab39ee52fe1be8a5f984f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184377"
---
Uma das principais funcionalidades do Azure IoT Edge é a capacidade de implementar o código para os seus dispositivos IoT Edge a partir da cloud. **Módulos do IoT Edge** são implementados como contentores de pacotes executáveis. Nesta secção, vai implementar um módulo previamente criado a partir da [seção de módulos do IoT Edge do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). 

O módulo que implementar nesta secção simula um sensor e envia os dados gerados. Este módulo é uma parte útil do código, quando está começando a usar com o IoT Edge porque pode usar os dados simulados para desenvolvimento e teste. Se quiser ver exatamente o que esse módulo faz, pode ver o [simulated o código de origem do sensor de temperatura](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs). 

Para implementar o seu primeiro módulo do Azure Marketplace, utilize os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com), introduza **Sensor de temperatura simulada** para a pesquisa e abra o resultado do Marketplace.

   ![Sensor de temperatura simulada na pesquisa de portal do Azure](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Escolha um dispositivo IoT Edge para receber este módulo. Sobre o **dispositivos de destino para o módulo do IoT Edge** página, forneça as seguintes informações:

   1. **Subscrição**: selecione a subscrição que contém o hub IoT que está a utilizar.

   2. **IoT Hub**: selecione o nome do hub IoT que está a utilizar.

   3. **Nome de dispositivo do IoT Edge**: Se tiver utilizado o nome do dispositivo sugerido anteriormente neste início rápido, introduza **myEdgeDevice**. Em alternativa, selecione **localizar dispositivo** para escolher a partir de uma lista de dispositivos do IoT Edge no seu IoT hub. 
   
   4. Selecione **Criar**.

3. Agora que optou por um módulo do IoT Edge do Azure Marketplace e escolhido um dispositivo IoT Edge para receber o módulo, é levado para um Assistente de três etapas que o ajuda a definir exatamente como implementar o módulo. Na **adicionar módulos** passo do assistente, tenha em atenção que o **SimulatedTemperatureSensor** módulo é preenchido automaticamente. Nos tutoriais, utilize esta página para adicionar módulos adicionais à sua implementação. Neste início rápido, só implemente esta um módulo. Selecione **seguinte** para continuar para o passo seguinte do assistente.

4. Na **especificar rotas** passo do assistente, define como as mensagens são passadas entre módulos e para o IoT Hub. O guia de introdução, pretende obter todas as mensagens de todos os módulos para ir para o IoT Hub (`$upstream`). Se não for preenchido automaticamente, adicione o seguinte código, em seguida, selecione **seguinte**:

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

5. Na **implementação de revisão** passo do assistente, pode visualizar o ficheiro JSON que define todos os módulos implementados para o seu dispositivo IoT Edge. Tenha em atenção que o **SimulatedTemperatureSensor** módulo está incluído e dois módulos de sistema adicionais chamado **edgeAgent** e **edgeHub**. Selecione **submeter** quando concluir revisão.

   Quando submete uma nova implementação para um dispositivo IoT Edge, nada é enviada por push para o seu dispositivo. Em vez disso, o dispositivo consulta regularmente o IoT Hub para quaisquer instruções de novo. Se o dispositivo encontra um manifesto de implantação atualizado, ele usa as informações sobre a nova implementação de nuvem, onde as imagens de módulo, em seguida, começa a executar os módulos localmente. Este processo pode demorar alguns minutos. 

6. Depois de submeter os detalhes de implementação do módulo, o Assistente de volta para o **IoT Edge** página do IoT hub. Selecione o seu dispositivo da lista de dispositivos do IoT Edge para ver os respetivos detalhes. 

7. Na página de detalhes do dispositivo, desloque para baixo para o **módulos** secção. Três módulos deverá ser listados: $edgeAgent $edgeHub e SimulatedTemperatureSensor. Se um ou mais dos módulos são listadas como especificadas na implementação, mas não reportados pelo dispositivo, o IoT Edge dispositivo é ainda iniciá-los. Aguarde uns instantes e selecione **atualizar** na parte superior da página. 

   ![Vista SimulatedTemperatureSensor na lista de módulos implementados](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
