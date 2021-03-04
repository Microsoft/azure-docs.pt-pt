---
title: Instale o Defender para o micro agente IoT
titleSuffix: Azure Defender for IoT
description: Aprenda a instalar e autentica o Micro-Agente Defender.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 3/3/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: ccf28c47e2e1438a141e2497da70d32c1832ddb9
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120441"
---
# <a name="install-defender-for-iot-micro-agent"></a>Instale o Defender para o micro agente IoT 

Este artigo fornece uma explicação de como instalar e autenticar o micro-agente Defender.

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o módulo Defender para IoT, tem de criar uma identidade de módulo no Hub IoT. Para obter mais informações sobre como criar uma identidade de módulo, consulte [Criar um módulo de micro-agente Defender IoT twin ](quickstart-create-micro-agent-module-twin.md).

## <a name="install-the-package"></a>Instale o pacote

Instale e configuure o repositório do pacote da Microsoft seguindo [estas instruções](/windows-server/administration/linux-package-repository-for-microsoft-software). 

Para Debian 9, as instruções não incluem o repositório que precisa de ser adicionado, utilize os seguintes comandos para adicionar o repositório: 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Para instalar o pacote de micro-agente Defender nas distribuições Linux baseadas em Debian e Ubuntu, utilize o seguinte comando:

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>Métodos de autenticação de micro-agentes 

As duas opções utilizadas para autenticar o Defender para o micro-agente IoT são: 

- Cadeia de ligação. 

- Um certificado.

### <a name="authenticate-using-a-connection-string"></a>Autenticar usando uma cadeia de ligação

Para autenticar usando uma cadeia de ligação:

1. Coloque um ficheiro com o nome `connection_string.txt` da cadeia de ligação codificada no utf-8 no caminho do diretório do agente de `/var/defender_iot_micro_agent` defesa, introduzindo o seguinte comando:

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    O `connection_string.txt` local deve agora ser localizado no seguinte local do `/var/defender_iot_micro_agent/connection_string.txt` percurso.

1. Reiniciar o serviço utilizando este comando:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>Autenticar usando um certificado

Para autenticar usando um certificado:

1. Obter um certificado seguindo [estas instruções.](../iot-hub/iot-hub-security-x509-get-started.md)

1. Coloque a parte pública codificada do PEM do certificado, e a chave privada, no Diretório do Agente Defensor no ficheiro chamado `certificate_public.pem` , e `certificate_private.pem` . 

1. Coloque o fio de ligação apropriado no `connection_string.txt` ficheiro. a cadeia de ligação deve ser assim: 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    Esta cadeia alerta o agente defensor, para esperar que seja fornecido um certificado para a autenticação. 

1. Reiniciar o serviço utilizando o seguinte comando:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>Valide a sua instalação

Para validar a sua instalação:

1. Certificando-se de que o micro-agente está a funcionar corretamente com o seguinte comando:  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. Certifique-se de que o serviço é estável, certificando-se de que é `active` e que o tempo de funcionamento do processo é adequado

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Verifique se o seu serviço está estável e ativo.":::
 
## <a name="testing-the-system-end-to-end"></a>Testar o sistema de ponta a ponta 

Pode testar o sistema de ponta a ponta criando um ficheiro de gatilho no dispositivo. O ficheiro do gatilho fará com que a verificação de linha de base no agente detete o ficheiro como uma violação de base. 

Criar um ficheiro no sistema de ficheiros com o seguinte comando:

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
No centro, ocorrerá uma recomendação de falha de validação de base, com um `CceId` de CIS-debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0.0: 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="A recomendação de falha de validação de base que ocorre no centro." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Deixe aparecer até uma hora para que a recomendação apareça no centro. 

## <a name="micro-agent-versioning"></a>Versão de micro-agente 

Para instalar uma versão específica do micro-agente Defender IoT, executar o seguinte comando: 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Passos seguintes

[Construção do micro agente Defender a partir do código fonte](quickstart-building-the-defender-micro-agent-from-source.md)