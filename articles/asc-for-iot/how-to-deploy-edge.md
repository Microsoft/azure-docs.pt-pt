---
title: Implantar a central de segurança do Azure para IoT Edge Module | Microsoft Docs
description: Saiba mais sobre como implantar uma central de segurança do Azure para o agente de segurança do IoT no IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2019
ms.author: mlottner
ms.openlocfilehash: 128265cd3e69cd27bab6538c9eb376410439824d
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176666"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Implantar um módulo de segurança em seu dispositivo IoT Edge


A **central de segurança do Azure para** o módulo IOT fornece uma solução de segurança abrangente para seus dispositivos IOT Edge.
O módulo de segurança coleta, agrega e analisa dados brutos de segurança do sistema operacional e do sistema de contêiner em alertas e recomendações de segurança acionáveis.
Para saber mais, consulte [módulo de segurança para IOT Edge](security-edge-architecture.md).

Neste artigo, você aprenderá a implantar um módulo de segurança em seu dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Implantar o módulo de segurança

Use as etapas a seguir para implantar um módulo de segurança da central de segurança do Azure para IoT para IoT Edge.

### <a name="prerequisites"></a>Pré-requisitos

1. No Hub IoT, verifique se o dispositivo está [registrado como um dispositivo IOT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

1. A central de segurança do Azure para IoT Edge módulo requer que a [estrutura auditada](https://linux.die.net/man/8/auditd) esteja instalada no dispositivo IOT Edge.

    - Instale a estrutura executando o seguinte comando em seu dispositivo de IoT Edge:
   
    `sudo apt-get install auditd audispd-plugins`

    - Verifique se a auditoria está ativa executando o seguinte comando: 
   
    `sudo systemctl status auditd`<br>
    - A resposta esperada é: `active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Implantação usando portal do Azure

1. No portal do Azure, abra o **Marketplace**.

1. Selecione **Internet das coisas**, em seguida, pesquise a **central de segurança do Azure para IOT** e selecione-a.

   ![Selecione a central de segurança do Azure para IoT](media/howto/edge-onboarding-8.png)

1. Clique em **criar** para configurar a implantação. 

1. Escolha a **assinatura** do Azure do Hub IOT e selecione o **Hub IOT**.<br>Selecione **implantar em um dispositivo** para direcionar um único dispositivo ou selecione **implantar em escala** para vários dispositivos de destino e clique em **criar**. Para obter mais informações sobre a implantação em escala, consulte [como implantar](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Se você selecionou **implantar em escala**, adicione o nome do dispositivo e os detalhes antes de continuar para a guia **Adicionar módulos** nas instruções a seguir.     

Há três etapas para criar uma implantação de IoT Edge para a central de segurança do Azure para IoT. As seções a seguir percorrem cada uma delas. 

#### <a name="step-1-add-modules"></a>Etapa 1: Adicionar módulos

1. Na guia **Adicionar módulos** , na área **módulos de implantação** , clique na opção **Configurar** para **AzureSecurityCenterforIoT**. 
   
1. Altere o **nome** para **azureiotsecurity**.
1. Altere o **URI da imagem** para **MCR.Microsoft.com/ascforiot/azureiotsecurity:1.0.0**.
1. Verifique se o valor das **Opções de criação do contêiner** está definido como:      
    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
1. Verifique se **definir propriedades desejadas do módulo Set** está selecionado e altere o objeto de configuração para:
      
    ``` json
    { 
       "properties.desired":{ 
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{ 

          }
       }
    }
    ```

1. Clique em **Guardar**.
1. Role até a parte inferior da guia e selecione **definir configurações avançadas de tempo de execução do Edge**. 
   
1. Altere a **imagem** em **Hub de borda** para **MCR.Microsoft.com/azureiotedge-Hub:1.0.9-RC2**.

1. Verifique se a **opção criar opções** está definida como: 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
1. Clique em **Guardar**.
   
1. Clique em **Seguinte**.

#### <a name="step-2-specify-routes"></a>Etapa 2: especificar rotas 

1. Na guia **especificar rotas** , verifique se você tem uma rota (explícita ou implícita) que encaminhará as mensagens do módulo **azureiotsecurity** para **$upstream** de acordo com os exemplos a seguir, somente clique em **Avançar**. 

~~~Default implicit route
"route": "FROM /messages/* INTO $upstream" 
~~~

~~~Explicit route
"ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
~~~

#### <a name="step-3-review-deployment"></a>Etapa 3: examinar a implantação

- Na guia **examinar implantação** , examine as informações de implantação e, em seguida, selecione **Enviar** para concluir a implantação.

## <a name="diagnostic-steps"></a>Etapas de diagnóstico

Se você encontrar um problema, os logs de contêiner serão a melhor maneira de aprender sobre o estado de um dispositivo de módulo de segurança IoT Edge. Utilize as ferramentas e comandos nesta secção para recolher informações.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Verifique se os contêineres necessários estão instalados e funcionando conforme o esperado

1. Execute o seguinte comando em seu dispositivo IoT Edge:
    
    `sudo docker ps`
   
1. Verifique se os seguintes contêineres estão em execução:
   
   | Nome | IMAGEM |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.0 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.9-rc2 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Se os contêineres mínimos necessários não estiverem presentes, verifique se o manifesto de implantação do IoT Edge está alinhado com as configurações recomendadas. Para obter mais informações, consulte [implantar IOT Edge Module](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Inspecione os logs de módulo para obter erros
   
1. Execute o seguinte comando em seu dispositivo IoT Edge:

   `sudo docker logs azureiotsecurity`
   
1. Para logs mais detalhados, adicione a seguinte variável de ambiente à implantação do módulo **azureiotsecurity** : `logLevel=Debug`.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as opções de configuração, vá para o guia de instruções para configuração de módulo. 
> [!div class="nextstepaction"]
> [Guia de instruções de configuração de módulo](./how-to-agent-configuration.md)
