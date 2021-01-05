---
title: Implementar módulo de segurança IoT Edge
description: Saiba como implantar um Defensor para agente de segurança IoT no IoT Edge.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: c2b440413599ce07112231af17daa0bc14817b76
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832782"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Implemente um módulo de segurança no seu dispositivo IoT Edge

O módulo **Defender for IoT** fornece uma solução de segurança abrangente para os seus dispositivos IoT Edge.
O módulo de segurança recolhe, agrega e analisa dados de segurança bruto do seu Sistema Operativo e sistema de Contentores em recomendações e alertas de segurança acccionáveis.
Para saber mais, consulte [o módulo de Segurança para IoT Edge](security-edge-architecture.md).

Neste artigo, você vai aprender a implementar um módulo de segurança no seu dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Implementar módulo de segurança

Utilize os seguintes passos para implantar um módulo de segurança Defender para IoT edge.

### <a name="prerequisites"></a>Pré-requisitos

1. No seu Hub IoT, certifique-se de que o seu dispositivo está [registado como um dispositivo IoT Edge](../iot-edge/how-to-manual-provision-symmetric-key.md#register-a-new-device).

1. O módulo Defender for IoT Edge requer que a [estrutura AuditD](https://linux.die.net/man/8/auditd) esteja instalada no dispositivo IoT Edge.

    - Instale a estrutura executando o seguinte comando no seu dispositivo IoT Edge:

    `sudo apt-get install auditd audispd-plugins`

    - Verifique se o AuditD está ativo executando o seguinte comando:

    `sudo systemctl status auditd`<br>
    - A resposta esperada é: `active (running)`

### <a name="deployment-using-azure-portal"></a>Implantação usando o portal Azure

1. A partir do portal Azure, **open Marketplace**.

1. Selecione **Internet of Things** e, em seguida, procure por Defender para **IoT** e selecione-o.

   :::image type="content" source="media/howto/edge-onboarding-8.png" alt-text="Selecione Defender para IoT":::

1. Clique em **Criar** para configurar a implementação.

1. Escolha a **Assinatura** Azure do seu Hub IoT e, em seguida, selecione o seu **Hub IoT**.<br>Selecione **Implementar para um dispositivo** para direcionar um único dispositivo ou selecione Implementar na **Escala** para atingir vários dispositivos e clique em **Criar**. Para obter mais informações sobre a implantação em escala, consulte [Como implementar](../iot-edge/how-to-deploy-at-scale.md).

    >[!Note]
    >Se selecionar **Implementar à Escala,** adicione o nome do dispositivo e os detalhes antes de continuar no separador **'Adicionar Módulos'** nas seguintes instruções.

Complete cada passo para completar a sua implementação IoT Edge para Defender para IoT.

#### <a name="step-1-modules"></a>Passo 1: Módulos

1. Selecione o módulo **AzureSecurityCenterforIoT.**
1. No **separador Definições** do Módulo, altere o **nome** para **azureiotsecurity**.
1. No separador **Variáveis de Enviroment,** adicione uma variável se necessário (por exemplo, nível de depuração).
1. No **separador Opções de Criação de Recipientes,** adicione a seguinte configuração:

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

1. No **separador Definições Gémeas** do Módulo, adicione a seguinte configuração:

   Propriedade Módulo Twin:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Módulo Twin Property Content: 

   ```json
     {

     }
   ```
    
   Para obter mais informações sobre a configuração do agente, consulte [os agentes de segurança Configure](./how-to-agent-configuration.md).

1. Selecione **Atualizar**.

#### <a name="step-2-runtime-settings"></a>Passo 2: Definições de tempo de execução

1. Selecione **definições de tempo de execução**.
1. Em **Edge Hub**, altere a **imagem** para **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**.
1. Verifique se **as opções de criar** estão definidas para a seguinte configuração:

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

1. Selecione **Seguinte**.

#### <a name="step-3-specify-routes"></a>Passo 3: Especificar rotas

1. No **separador Rotas Especificas,** certifique-se de que tem uma rota (explícita ou implícita) que irá encaminhar mensagens do módulo **de insegurança azureiot** para **$upstream** de acordo com os seguintes exemplos. Só quando o percurso estiver no lugar, selecione **Next**.

   Percursos de exemplo:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Selecione **Seguinte**.

#### <a name="step-4-review-deployment"></a>Passo 4: Implantação de revisão

- No separador **'Implementação de revisão',** reveja as informações de implementação e, em seguida, selecione **Criar** para completar a implementação.

## <a name="diagnostic-steps"></a>Etapas de diagnóstico

Se encontrar um problema, os registos de contentores são a melhor forma de aprender sobre o estado de um dispositivo de módulo de segurança IoT Edge. Utilize as ferramentas e comandos nesta secção para recolher informações.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Verifique se os recipientes necessários estão instalados e funcionando como esperado

1. Executar o seguinte comando no seu dispositivo IoT Edge:

    `sudo docker ps`

1. Verifique se os seguintes recipientes estão em funcionamento:

   | Nome | IMAGEM |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Se os recipientes mínimos exigidos não estiverem presentes, verifique se o seu manifesto de implantação IoT Edge está alinhado com as definições recomendadas. Para obter mais informações, consulte [o módulo Implementar IoT Edge.](#deployment-using-azure-portal)

### <a name="inspect-the-module-logs-for-errors"></a>Inspecione os registos do módulo para obter erros

1. Executar o seguinte comando no seu dispositivo IoT Edge:

   `sudo docker logs azureiotsecurity`

1. Para obter mais registos verbosos, adicione a seguinte variável ambiental à implantação do módulo **de insegurança azureiot:** `logLevel=Debug` .

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as opções de configuração, continue a guiar como fazer para a configuração do módulo.
> [!div class="nextstepaction"]
> [Configuração do módulo como guiar](./how-to-agent-configuration.md)