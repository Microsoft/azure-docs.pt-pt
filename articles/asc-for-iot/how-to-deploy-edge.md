---
title: Implementar o Centro de segurança do Azure para o módulo do IoT Edge | Documentos da Microsoft
description: Saiba mais sobre como implementar um centro de segurança do Azure para o agente de segurança de IoT no IoT Edge.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/1/2019
ms.author: mlottner
ms.openlocfilehash: 85e342f08e5402e50e5b0dfd1fe2df90337f29ca
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254301"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Implementar um módulo de segurança no seu dispositivo IoT Edge

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Centro de segurança do Azure (ASC) para IoT** módulo fornece uma solução de segurança abrangente para o seu dispositivo IoT Edge.
Módulo de segurança recolhe, agrega e analisa os dados de segurança bruto do seu sistema de sistema operativo e um contentor em alertas e recomendações de segurança acionáveis.
Para obter mais informações, consulte [módulo de segurança para o IoT Edge](security-edge-architecture.md).

Neste guia, irá aprender a implementar um módulo de segurança no seu dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Implementar o módulo de segurança

Utilize os seguintes passos para implementar um ASC para o módulo de segurança de IoT para o IoT Edge.

### <a name="prerequisites"></a>Pré-requisitos

- No seu IoT Hub, certifique-se de que o dispositivo está [registado como um dispositivo IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

- Requer o ASC para o módulo do IoT Edge [AuditD framework](https://linux.die.net/man/8/auditd) está instalado no dispositivo IoT Edge.

    - Instale o framework executando o seguinte comando no seu dispositivo IoT Edge:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - Certifique-se de que auditd está ativo, executando o seguinte comando:
   
      `sudo systemctl status auditd`
      
        A resposta esperada é `active (running)`. 

### <a name="deployment-using-azure-portal"></a>Implementação com o portal do Azure

1. A partir do portal do Azure, abra **Marketplace**.

1. Selecione **Internet das coisas**, em seguida, procure **Centro de segurança do Azure para IoT** e selecioná-lo.

   ![Selecione o Centro de segurança do Azure para IoT](media/howto/edge-onboarding-8.png)

1. Clique em **criar** para configurar a implementação. 

1. Escolha do Azure **subscrição** do IoT Hub, em seguida, selecione seu **IoT Hub**.<br>Selecione **implementar num dispositivo** para um único dispositivo de destino ou selecione **implementar em escala** para vários dispositivos de destino e clique em **criar**. Para obter mais informações sobre a implantação em escala, veja [como implementar](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Se tiver selecionado **implementar em escala**, adicionar os detalhes e o nome do dispositivo antes de continuar para o **adicionar módulos** separador as instruções seguintes.     

Existem três passos para criar uma implementação de IoT Edge para o Centro de segurança do Azure para IoT. As seções a seguir, percorra cada um deles. 

#### <a name="step-1-add-modules"></a>Passo 1: Adicionar módulos

1. Partir do **adicionar módulos** separador, **módulos de implementação** área, clique em **AzureSecurityCenterforIoT**. 
   
1. Alteração da **name** ao **azureiotsecurity**.
1. Alteração da **URI de imagem** ao **mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3**.
1. Verifique se o **opções de criar contentor** valor está definido como:      
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
1. Certifique-se de que **duplo do módulo de conjunto de propriedades pretendidas** está selecionada e alterar o objeto de configuração para:
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. Clique em **Guardar**.
1. Desloque-se para a parte inferior do separador e selecione **configurar definições de Runtime do Edge avançadas**.
   
   >[!Note]
   > Fazer **não** desativar a comunicação AMQP para o Hub do IoT Edge.
   > Centro de segurança do Azure para o módulo de IoT requer comunicação AMQP com o Hub do IoT Edge.
   
1. Alteração da **imagem** sob **Edge Hub** para **mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview**.

   >[!Note]
   > Centro de segurança do Azure para o módulo de IoT requer uma versão bifurcada do IoT Hub do Edge, com base na versão 1.20 do SDK.
   > Ao alterar a imagem de Hub do IoT Edge, são instruindo o seu dispositivo IoT Edge para substituir a versão estável mais recente com a versão bifurcada do Hub do IoT Edge, que não é oficialmente suportado pelo serviço de IoT Edge.

1. Certifique-se **criar opções** está definida como: 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. Clique em **Guardar**.
   
1. Clique em **Seguinte**.

#### <a name="step-2-specify-routes"></a>Passo 2: Especificar rotas 

1. Na **especificar rotas** separador, defina o **ASCForIoTToIoTHub** encaminhar para **"da/mensagens/módulos/azureiotsecurity/\* em $ a montante"** e clique em  **Próxima**.

   ![Especificar rotas](media/howto/edge-onboarding-9.png)

#### <a name="step-3-review-deployment"></a>Passo 3: Implementação de revisão

1. Na **rever implantação** separador, reveja as suas informações de implantação, em seguida, selecione **submeter** para concluir a implementação.

## <a name="diagnostic-steps"></a>Passos de diagnóstico

Se ocorrer um problema, registos de contentor são a melhor forma de saber mais sobre o estado de um dispositivo de módulo de segurança de IoT Edge. Utilize as ferramentas e comandos nesta secção para recolher informações.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Certifique-se de que os contentores necessários estão instalados e está a funcionar conforme esperado

1. Execute o seguinte comando no seu dispositivo IoT Edge:
    
     `sudo docker ps`
   
1. Certifique-se de que os contentores seguintes estão em execução:
   
   | Name | IMAGEM |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Se o mínimo necessário contentores não estão presentes, verifique se o manifesto de implantação do IoT Edge está em sintonia com as definições recomendadas. Para obter mais informações, consulte [módulo do IoT Edge/implementar](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Inspecione os registos de módulo para erros
   
1. Execute o seguinte comando no seu dispositivo IoT Edge:

   `sudo docker logs azureiotsecurity`
   
1. Para os registos mais detalhados, adicione a seguinte variável de ambiente para **azureiotsecurity** implementação do módulo: `logLevel=Debug`.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as opções de configuração, avance para o guia de procedimentos de configuração do módulo. 
> [!div class="nextstepaction"]
> [Guia de procedimentos de configuração do módulo](./how-to-agent-configuration.md)
