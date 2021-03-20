---
title: incluir ficheiro
description: incluir ficheiro
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0fc6a62a94f31617209bcc60bfaa95bc8927551a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050299"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Implementar módulo de borda IoT da grelha de eventos

Existem várias formas de implantar módulos para um dispositivo IoT Edge e todos eles trabalham para a Azure Event Grid em IoT Edge. Este artigo descreve os passos para implementar a Grade de Eventos no IoT Edge a partir do portal Azure.

>[!NOTE]
> Neste tutorial, irá implantar o módulo De Grelha de Eventos sem persistência. Significa que quaisquer tópicos e subscrições que criar neste tutorial serão eliminados quando recolocar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [Persist state in Linux](../articles/event-grid/edge/persist-state-linux.md) ou [Persist state in Windows](../articles/event-grid/edge/persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que instale o módulo De Grelha de Eventos com persistência.

>[!IMPORTANT]
> Neste tutorial, o módulo Event Grid será implementado com autenticação do cliente desligado e permitirá os subscritores HTTP. Para cargas de trabalho de produção, recomendamos que permita apenas pedidos DE HTTPS e assinantes com autenticação do cliente ativado. Para obter mais informações sobre como configurar o módulo de Grelha de Eventos de forma segura, consulte [Segurança e autenticação.](../articles/event-grid/edge/security-authentication.md)
 
### <a name="select-your-iot-edge-device"></a>Selecione o seu dispositivo IoT Edge

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Navegue até ao seu Hub IoT.
1. Selecione **IoT Edge** do menu na secção De Gestão automática de **Dispositivos.** 
1. Clique no ID do dispositivo alvo a partir da lista de dispositivos
1. Selecione **módulos de conjunto**. Mantenha a página aberta. Continuará com os passos na próxima secção.

### <a name="configure-a-deployment-manifest"></a>Configure um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implementar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. O portal Azure tem um assistente que o acompanha através da criação de um manifesto de implantação, em vez de construir o documento JSON manualmente.  Tem três etapas: **Adicionar módulos,** **especificar rotas** e **revisão.**

### <a name="add-modules"></a>Adicionar módulos

1. Na secção **de Módulos de Implementação,** selecione **Adicionar**
1. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
1. Fornecer o nome, imagem, recipiente criar opções do recipiente:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Nome**: eventgridmodule
   * **Imagem URI:**`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opções de criação de contentores:**

```json
    {
      "Env": [
        "inbound__clientAuth:clientCert__enabled=false",
        "outbound__webhook__httpsOnly=false"
      ],
      "HostConfig": {
        "PortBindings": {
          "4438/tcp": [
            {
              "HostPort": "4438"
            }
          ]
        }
      }
    }
```

 1. Clique em **Guardar**
 1. Clique **ao lado** para continuar na secção de rotas

    > [!NOTE]
    > Se estiver a utilizar um Azure VM como dispositivo de borda, adicione uma regra de entrada para permitir o tráfego de entrada na porta 4438. Para obter instruções sobre a adição da regra, consulte [como abrir portas a um VM](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Rotas de configuração

 Mantenha as rotas predefinidos e selecione **Seguinte** para continuar na secção de revisão

### <a name="review-deployment"></a>Implementação de revisão

1. A secção de revisão mostra-lhe o manifesto de implantação JSON que foi criado com base nas suas seleções nas duas secções anteriores. Confirme que vê os dois módulos da lista: **$edgeAgent** e **$edgeHub**. Estes dois módulos compõem o tempo de funcionamento do IoT Edge e são necessários predefinidos em todas as implementações.
1. Reveja as suas informações de implementação e, em seguida, **selecione Enviar por isso**.

### <a name="verify-your-deployment"></a>Verifique a sua implantação

1. Depois de submeter a implementação, volte à página IoT Edge do seu hub IoT.
1. Selecione o **dispositivo IoT Edge** que direcionou com a implementação para abrir os seus detalhes.
1. Nos dados do dispositivo, verifique se o módulo 'Grade de Evento' está listado como **especificado na implementação** e **reportado pelo dispositivo**.

Pode levar alguns momentos para que o módulo seja iniciado no dispositivo e depois reportado de volta ao IoT Hub. Refresque a página para ver um estado atualizado.