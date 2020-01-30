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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844598"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Implantar o módulo de IoT Edge da grade de eventos

Há várias maneiras de implantar módulos em um dispositivo IoT Edge e todos eles funcionam para a grade de eventos do Azure no IoT Edge. Este artigo descreve as etapas para implantar a grade de eventos em IoT Edge do portal do Azure.

>[!NOTE]
> Neste tutorial, você implantará o módulo de grade de eventos sem persistência. Isso significa que todos os tópicos e assinaturas criados neste tutorial serão excluídos quando você reimplantar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [persistir estado no Linux](../articles/event-grid/edge/persist-state-linux.md) ou [persistir o estado no Windows](../articles/event-grid/edge/persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que você instale o módulo de grade de eventos com persistência.

>[!IMPORTANT]
> Neste tutorial, o módulo Event Grid será implantado com a autenticação do cliente desligada e permitirá aos subscritores http. Para cargas de trabalho de produção, recomendamos que apenas permita pedidos HTTPS e assinantes com autenticação do cliente ativado. Para obter mais informações sobre como configurar o módulo de grade de eventos com segurança, consulte [segurança e autenticação](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Selecione seu dispositivo IoT Edge

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Navegue até o Hub IoT.
1. Selecione **IOT Edge** no menu da seção **Gerenciamento de dispositivo automático** . 
1. Clique na ID do dispositivo de destino na lista de dispositivos
1. Selecione **Definir Módulos**. Mantenha a página aberta. Você continuará com as etapas na próxima seção.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. O portal do Azure tem um assistente que o orienta na criação de um manifesto de implantação, em vez de criar o documento JSON manualmente.  Ele tem três passos: **adicionar módulos**, **especificar rotas**, e **rever implementação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de implantação** , selecione **Adicionar**
1. Nos tipos de módulos na lista suspensa, selecione **IOT Edge módulo**
1. Forneça as opções nome, imagem e contêiner criar do contêiner:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Nome**: eventgridmodule
   * **URI da imagem**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opções de criação de contêiner**:

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

 1. Clicar em **Guardar**
 1. Clique em **Avançar** para continuar na seção rotas

    > [!NOTE]
    > Se estiver a utilizar um VM Azure como dispositivo de borda, adicione uma regra de entrada para permitir o tráfego de entrada na porta 4438. Para obter instruções sobre como adicionar a regra, consulte [como abrir portas para uma VM](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Rotas de instalação

 Mantenha as rotas padrão e selecione **Avançar** para continuar na seção revisão

### <a name="review-deployment"></a>Implementação de revisão

1. A revisão de seção mostra que a implementação de JSON de manifesto que foi criado com base nas suas seleções nas duas secções anteriores. Confirme que vê os dois módulos na lista: **$edgeAgent** e **$edgeHub**. Estes dois módulos compõem o tempo de funcionamento do IoT Edge e são exigidos predefinidos em todas as implementações.
1. Reveja as suas informações de implantação, em seguida, selecione **submeter**.

### <a name="verify-your-deployment"></a>Verificar sua implantação

1. Depois de enviar a implantação, você retornará para a página IoT Edge do Hub IoT.
1. Selecione o **dispositivo de IOT Edge** de destino com a implantação para abrir seus detalhes.
1. Nos detalhes do dispositivo, verifique se o módulo De Rede de Eventos está listado como **especificado na implementação** e **reportado pelo dispositivo**.

Pode levar alguns minutos para que o módulo seja iniciado no dispositivo e reportado de volta ao Hub IoT. Atualize a página para ver um status atualizado.