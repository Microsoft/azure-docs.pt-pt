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
ms.openlocfilehash: 8656bbb070e2b05a06ea22dd1634a40182b440cb
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73098677"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Implantar o módulo de IoT Edge da grade de eventos

Há várias maneiras de implantar módulos em um dispositivo IoT Edge e todos eles funcionam para a grade de eventos do Azure no IoT Edge. Este artigo descreve as etapas para implantar a grade de eventos em IoT Edge do portal do Azure.

>[!NOTE]
> Neste tutorial, você implantará o módulo de grade de eventos sem persistência. Isso significa que todos os tópicos e assinaturas criados neste tutorial serão excluídos quando você reimplantar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [persistir estado no Linux](../articles/event-grid/edge/persist-state-linux.md) ou [persistir o estado no Windows](../articles/event-grid/edge/persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que você instale o módulo de grade de eventos com persistência.

>[!IMPORTANT]
> Neste tutorial, o módulo de grade de eventos será implantado com a autenticação de cliente desativada e permitirá assinantes HTTP. Para cargas de trabalho de produção, recomendamos que você habilite somente solicitações HTTPS e assinantes com autenticação de cliente habilitada. Para obter mais informações sobre como configurar o módulo de grade de eventos com segurança, consulte [segurança e autenticação](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Selecione seu dispositivo IoT Edge

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Navegue até o Hub IoT.
1. Selecione **IOT Edge** no menu da seção **Gerenciamento de dispositivo automático** . 
1. Clique na ID do dispositivo de destino na lista de dispositivos
1. Selecione **Definir Módulos**. Mantenha a página aberta. Você continuará com as etapas na próxima seção.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos serão implantados, como os dados fluem entre os módulos e as propriedades desejadas do módulo gêmeos. O portal do Azure tem um assistente que o orienta na criação de um manifesto de implantação, em vez de criar o documento JSON manualmente.  Ele tem três etapas: **Adicionar módulos**, **especificar rotas**e **examinar a implantação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de implantação** , selecione **Adicionar**
1. Nos tipos de módulos na lista suspensa, selecione **IOT Edge módulo**
1. Forneça as opções nome, imagem e contêiner criar do contêiner:

   * **Nome**: eventgridmodule
   * **URI da imagem**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opções de criação de contêiner**:

    ```json
        {
          "Env": [
            "inbound:clientAuth:clientCert:enabled=false",
            "outbound:webhook:httpsOnly=false"
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

 1. Clicar em **Guardar**
 1. Clique em **Avançar** para continuar na seção rotas

    > [!NOTE]
    > Se você estiver usando uma VM do Azure como um dispositivo de borda, adicione uma regra de porta de entrada para permitir o tráfego de entrada na porta 4438. Para obter instruções sobre como adicionar a regra, consulte [como abrir portas para uma VM](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Rotas de instalação

 Mantenha as rotas padrão e selecione **Avançar** para continuar na seção revisão

### <a name="review-deployment"></a>Examinar a implantação

1. A seção revisão mostra o manifesto de implantação JSON que foi criado com base em suas seleções nas duas seções anteriores. Confirme que você vê os dois módulos na lista: **$edgeAgent** e **$edgeHub**. Esses dois módulos compõem o tempo de execução de IoT Edge e são padrões necessários em cada implantação.
1. Examine as informações de implantação e, em seguida, selecione **Enviar**.

### <a name="verify-your-deployment"></a>Verificar sua implantação

1. Depois de enviar a implantação, você retornará para a página IoT Edge do Hub IoT.
1. Selecione o **dispositivo de IOT Edge** de destino com a implantação para abrir seus detalhes.
1. Nos detalhes do dispositivo, verifique se o módulo grade de eventos está listado como ambos **especificados na implantação** e **relatados pelo dispositivo**.

Pode levar alguns minutos para que o módulo seja iniciado no dispositivo e reportado de volta ao Hub IoT. Atualize a página para ver um status atualizado.