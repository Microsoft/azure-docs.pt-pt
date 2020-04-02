---
title: Implementar o Centro de Segurança Azure para módulo IoT Edge. Microsoft Docs
description: Saiba como implantar um Centro de Segurança Azure para agente de segurança IoT em IoT Edge.
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
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 3aee30e8ad82f9657c3bc9e97a7657a1e8c7989d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548916"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Implemente um módulo de segurança no seu dispositivo IoT Edge


**O Azure Security Center para o módulo IoT** fornece uma solução de segurança abrangente para os seus dispositivos IoT Edge.
O módulo de segurança recolhe, agrega e analisa dados de segurança bruto do seu Sistema Operativo e sistema de contentores em recomendações e alertas de segurança acionáveis.
Para saber mais, consulte o módulo de [segurança para IoT Edge](security-edge-architecture.md).

Neste artigo, você vai aprender a implementar um módulo de segurança no seu dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Implementar módulo de segurança

Utilize os seguintes passos para implantar um Centro de Segurança Azure para módulo de segurança IoT para IoT Edge.

### <a name="prerequisites"></a>Pré-requisitos

1. No seu Hub IoT, certifique-se de que o seu dispositivo está [registado como um dispositivo IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

1. O Azure Security Center para o módulo IoT Edge requer que a [estrutura Auditada](https://linux.die.net/man/8/auditd) seja instalada no dispositivo IoT Edge.

    - Instale a estrutura executando o seguinte comando no seu dispositivo IoT Edge:
   
    `sudo apt-get install auditd audispd-plugins`

    - Verificar se o AuditD está ativo executando o seguinte comando: 
   
    `sudo systemctl status auditd`<br>
    - A resposta esperada é:`active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Implantação utilizando portal Azure

1. Do portal Azure, open **Marketplace.**

1. Selecione **Internet das Coisas,** em seguida, procure o **Azure Security Center para IoT** e selecione-o.

   ![Selecione Centro de Segurança Azure para IoT](media/howto/edge-onboarding-8.png)

1. Clique em **Criar** para configurar a implementação. 

1. Escolha a **Assinatura** Azure do seu Hub IoT e, em seguida, selecione o seu **Hub IoT**.<br>Selecione **'Implementar' para um dispositivo** para direcionar um único dispositivo ou selecionar **o 'Deploy' em Escala** para direcionar vários dispositivos, e clique em **Criar**. Para obter mais informações sobre a implantação em escala, consulte [Como implementar](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Se selecionou **A implantação à escala,** adicione o nome do dispositivo e os detalhes antes de continuar no separador **Adicionar Módulos** nas seguintes instruções.     

Complete cada passo para completar a sua implantação IoT Edge para o Azure Security Center for IoT. 

#### <a name="step-1-modules"></a>Passo 1: Módulos

1. Selecione o módulo **AzureSecurityCenterforIoT.**
1. No separador Definições do **Módulo,** altere o **nome** para **segurança azuleiotsecurity**.
1. No separador **Variáveis Enviroment,** adicione uma variável se necessário (por exemplo, nível de depuração).
1. No separador Criação de Opções do **Recipiente,** adicione a seguinte configuração:

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
    
1. No separador **Definições Twin Module,** adicione a seguinte configuração:
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
    ```

1. Selecione **Atualizar**.

#### <a name="step-2-runtime-settings"></a>Passo 2: Definições de tempo de execução

1. Selecione Definições de tempo de **execução**.
1. Under **Edge Hub,** mude a **Imagem** para **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**.
1. Verificar **as Opções create** está definida para a seguinte configuração: 
         
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
    
1. Selecione **Guardar**.
   
1. Selecione **Next**.

#### <a name="step-3-specify-routes"></a>Passo 3: Especificar rotas 

1. No **separador 'Especificar Rotas',** certifique-se de que tem uma rota (explícita ou implícita) que irá encaminhar mensagens do módulo de **segurança azul** para **$upstream** de acordo com os seguintes exemplos. Só quando a rota estiver no lugar, selecione **Next**.

   Rotas de exemplo:

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. Selecione **Next**.

#### <a name="step-4-review-deployment"></a>Passo 4: Revisão da implantação

- No separador **'Implementação de Revisão',** reveja as informações de implementação e, em seguida, selecione **Criar** para completar a implementação.

## <a name="diagnostic-steps"></a>Passos de diagnóstico

Se encontrar um problema, os registos de contentores são a melhor forma de aprender sobre o estado de um dispositivo de módulo de segurança IoT Edge. Utilize as ferramentas e comandos nesta secção para recolher informações.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Verifique se os recipientes necessários estão instalados e funcionando como esperado

1. Execute o seguinte comando no seu dispositivo IoT Edge:
    
    `sudo docker ps`
   
1. Verifique se os seguintes contentores estão em funcionamento:
   
   | Nome | IMAGEM |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   Se os recipientes mínimos necessários não estiverem presentes, verifique se o manifesto de implantação do IoT Edge está alinhado com as definições recomendadas. Para mais informações, consulte [o módulo Deploy IoT Edge](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Inspecione os registos do módulo para verificar se há erros
   
1. Execute o seguinte comando no seu dispositivo IoT Edge:

   `sudo docker logs azureiotsecurity`
   
1. Para obter mais registos verbosos, adicione a seguinte variável `logLevel=Debug`ambiental à implantação do módulo de **segurança azul:** .

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as opções de configuração, continue a orientar-se para a configuração do módulo. 
> [!div class="nextstepaction"]
> [Configuração do módulo como guiar](./how-to-agent-configuration.md)
