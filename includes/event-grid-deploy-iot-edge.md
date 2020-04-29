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
ms.openlocfilehash: b453a04a170764a037eed7415eaf71e5a4d37526
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76844598"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Implementar módulo De Borda da Grelha de Eventos IoT

Existem várias formas de implementar módulos para um dispositivo IoT Edge e todos eles trabalham para a Grelha de Eventos Azure em IoT Edge. Este artigo descreve os passos para implantar a Grelha de Eventos na Borda IoT do portal Azure.

>[!NOTE]
> Neste tutorial, irá implantar o módulo Da Grelha de Eventos sem persistência. Significa que quaisquer tópicos e subscrições que criar neste tutorial serão eliminados quando reimplantar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [Persistir no estado de Linux](../articles/event-grid/edge/persist-state-linux.md) ou Persistir no [Windows](../articles/event-grid/edge/persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que instale o módulo Da Grelha de Eventos com persistência.

>[!IMPORTANT]
> Neste tutorial, o módulo Event Grid será implantado com a autenticação do cliente desligada e permitirá aos subscritores http. Para cargas de trabalho de produção, recomendamos que apenas permita pedidos HTTPS e assinantes com autenticação do cliente ativado. Para obter mais informações sobre como configurar o módulo de Rede de Eventos de forma segura, consulte [segurança e autenticação.](../articles/event-grid/edge/security-authentication.md)
 
### <a name="select-your-iot-edge-device"></a>Selecione o seu dispositivo IoT Edge

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Navegue para o seu Hub IoT.
1. Selecione **IoT Edge** do menu na secção De Gestão automática de **Dispositivos.** 
1. Clique na identificação do dispositivo alvo a partir da lista de dispositivos
1. Selecione **Módulos de Conjunto**. Mantenha a página aberta. Continuará com os passos na próxima secção.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implantar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. O portal Azure tem um assistente que o acompanha através da criação de um manifesto de implantação, em vez de construir manualmente o documento JSON.  Tem três **passos: Adicionar módulos,** **especificar rotas,** e **rever a implementação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na secção Módulos de **Implementação,** selecione **Adicionar**
1. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
1. Forneça o nome, imagem, recipiente criar opções do recipiente:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Nome**: eventgridmodule
   * **Imagem URI:**`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **O recipiente cria opções:**

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
 1. Clique **em próximo** para continuar na secção rotas

    > [!NOTE]
    > Se estiver a utilizar um VM Azure como dispositivo de borda, adicione uma regra de entrada para permitir o tráfego de entrada na porta 4438. Para obter instruções sobre a adição da regra, consulte [Como abrir portas a um VM](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Rotas de configuração

 Mantenha as rotas predefinidas e selecione **Next** para continuar na secção de revisão

### <a name="review-deployment"></a>Revisão da implantação

1. A secção de revisão mostra-lhe o manifesto de implantação jSON que foi criado com base nas suas seleções nas duas secções anteriores. Confirme que vê os dois módulos na lista: **$edgeAgent** e **$edgeHub**. Estes dois módulos compõem o tempo de funcionamento do IoT Edge e são exigidos predefinidos em todas as implementações.
1. Reveja as informações de implementação e, em seguida, selecione **Enviar**.

### <a name="verify-your-deployment"></a>Verifique a sua implantação

1. Depois de submeter a implementação, volte à página IoT Edge do seu hub IoT.
1. Selecione o **dispositivo IoT Edge** que apontou com a implementação para abrir os seus detalhes.
1. Nos detalhes do dispositivo, verifique se o módulo De Rede de Eventos está listado como **especificado na implementação** e **reportado pelo dispositivo**.

Pode levar alguns momentos para o módulo ser iniciado no dispositivo e depois reportado de volta ao IoT Hub. Refresque a página para ver um estado atualizado.