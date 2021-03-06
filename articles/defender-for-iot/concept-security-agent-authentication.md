---
title: Autenticação de agente de segurança (Pré-visualização)
description: Realizar a autenticação do micro-agente com dois métodos possíveis.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: 8dd8abaedaaababf4d84330c5bf8cb030bac55bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779244"
---
# <a name="micro-agent-authentication-methods-preview"></a>Métodos de autenticação de micro-agente (Pré-visualização)

Existem duas opções de autenticação com o Defender para O Micro Agente IoT: 

- Cadeia de ligação 

- Certificado 

## <a name="authentication-using-a-connection-string"></a>Autenticação usando uma cadeia de ligação 

Para utilizar uma cadeia de ligação, é necessário adicionar um ficheiro que utilize a cadeia de ligação codificada no utf-8 no diretório do agente de defesa num ficheiro denominado `connection_string.txt` . Por exemplo,

```azurecli
echo “<connection string>” > connection_string.txt 
```

Depois de o ter feito, deverá reiniciar o serviço utilizando este comando.

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>Autenticação utilizando um certificado 


Para efetuar a autenticação através de um certificado: 

1. Coloque a parte pública codificada do PEM de um certificado no diretório do agente de defesa, num ficheiro chamado `certificate_public.pem` .
1. Coloque a chave privada codificada pelo PEM no diretório do agente de defesa, num ficheiro chamado `certificate_private.pem` .
1. Coloque a cadeia de ligação apropriada num ficheiro denominado `connection_string.txt` . Por exemplo,

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    Esta ação indica que o agente de defesa espera que seja fornecido um certificado para autenticação. 

1. reiniciar o serviço utilizando o seguinte código: 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>Certifique-se de que o micro-agente está a funcionar corretamente 

1. Execute o seguinte comando: 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. Verifique se o serviço está estável, certificando-se de que está **ativo** e que o tempo de funcionamento do processo é adequado: 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="Certifique-se de que o seu serviço está estável, certificando-se de que está ativo.":::

## <a name="next-steps"></a>Passos seguintes

Verifique a sua [postura de Segurança – benchmark CIS](concept-security-posture.md).
