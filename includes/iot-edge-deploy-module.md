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
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564813"
---
Um dos principais recursos do Azure IoT Edge é a capacidade de implantar código em seus dispositivos IoT Edge da nuvem. **Módulos de IOT Edge** são pacotes executáveis implementados como contêineres. Nesta seção, você implantará um módulo pré-compilado da [seção módulos de IOT Edge do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) diretamente do Hub IOT do Azure.

O módulo implantado nesta seção simula um sensor e envia os dados gerados. Esse módulo é uma parte útil do código quando você está começando a usar o IoT Edge, pois você pode utilizar os dados simulados para desenvolvimento e teste. Se você quiser ver exatamente o que esse módulo faz, você pode exibir o [código-fonte do sensor de temperatura simulado](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Para implantar seu primeiro módulo do Azure Marketplace, use as seguintes etapas:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

1. No menu no painel esquerdo, em gerenciamento de **dispositivo automático**, selecione **IOT Edge**.

1. Clique na ID do dispositivo do dispositivo de destino na lista de dispositivos.

1. Na barra superior, selecione **definir módulos**.

1. Na seção **módulos IOT Edge** da página, clique em **Adicionar**.

1. No menu suspenso, selecione **módulo do Marketplace**.

   ![Sensor de temperatura simulada na pesquisa de portal do Azure](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. No **Marketplace do módulo IOT Edge**, pesquise "sensor de temperatura simulada" e selecione esse módulo.

1. Observe que o módulo SimulatedTemperatureSensor é populado automaticamente. Nos tutoriais, você usa essa página para adicionar mais módulos à sua implantação. Para este guia de início rápido, implante apenas este módulo. Nenhuma credencial é necessária porque é pública.

   ![Definir módulos no dispositivo](./media/iot-edge-deploy-module/set-modules-on-device.png)

   Selecione **Avançar: rotas** para continuar na próxima etapa do assistente.

1. Na guia **rotas** do assistente, você define como as mensagens são passadas entre os módulos e o Hub IOT. As mensagens são construídas usando pares de nome/valor. Para o início rápido, você deseja que todas as mensagens de todos os módulos acessem o Hub IoT (`$upstream`). Se não for populado automaticamente, adicione o seguinte código para o **valor** para o **nome** `upstream`:

   ```sql
    FROM /messages/* INTO $upstream
   ```

   Selecione **Avançar: examinar + criar** para continuar na próxima etapa do assistente.

1. Na guia **revisar + criar** do assistente, você pode visualizar o arquivo JSON que define todos os módulos que são implantados em seu dispositivo IOT Edge. Observe que o módulo **SimulatedTemperatureSensor** está incluído e dois módulos de sistema adicionais chamados **edgeAgent** e **edgeHub**. Selecione **criar** quando terminar de revisar.

   Quando você envia uma nova implantação para um dispositivo IoT Edge, nada é enviado por push para seu dispositivo. Em vez disso, o dispositivo consulta o Hub IoT regularmente em busca de novas instruções. Se o dispositivo encontrar um manifesto de implantação atualizado, ele usará as informações sobre a nova implantação para efetuar pull das imagens de módulo da nuvem e, em seguida, começará a executar os módulos localmente. Esse processo pode levar alguns minutos.

1. Depois de criar os detalhes de implantação do módulo, o assistente o retornará à página **IOT Edge** do Hub IOT. Selecione seu dispositivo na lista de dispositivos IoT Edge para ver seus detalhes.

1. Na página detalhes do dispositivo, role para baixo até a guia **módulos** . Três módulos devem ser listados: $edgeAgent, $edgeHub e SimulatedTemperatureSensor. Se um ou mais dos módulos estiverem listados como especificado na implantação, mas não forem relatados pelo dispositivo, seu dispositivo de IoT Edge ainda estará iniciando-os. Aguarde alguns instantes e selecione **Atualizar** na parte superior da página.

   ![Vista SimulatedTemperatureSensor na lista de módulos implementados](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
