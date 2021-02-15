---
title: Métodos de autenticação de agentes de segurança
description: Saiba mais sobre os diferentes métodos de autenticação disponíveis ao utilizar o serviço Defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: c72fa87201fb0d7fdd526f0e6f4eeb2c35192fc2
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521650"
---
# <a name="security-agent-authentication-methods"></a>Métodos de autenticação de agentes de segurança

Este artigo explica os diferentes métodos de autenticação que pode utilizar com o agente AzureIoTSecurity para autenticar com o IoT Hub.

Para cada dispositivo a bordo do Defender para IoT no IoT Hub, é necessário um módulo de segurança. Para autenticar o dispositivo, o Defender para IoT pode utilizar um de dois métodos. Escolha o método que funciona melhor para a sua solução IoT existente.

> [!div class="checklist"]
> * Opção SecurityModule
> * Opção dispositivo

## <a name="authentication-methods"></a>Métodos de autenticação

Os dois métodos para o Defensor do IoT AzureIoTSecurity agente realizar a autenticação:

- Modo de autenticação **SecurityModule**<br>
O agente é autenticado utilizando a identidade do módulo de segurança independentemente da identidade do dispositivo.
Utilize este tipo de autenticação se pretender que o agente de segurança utilize um método de autenticação dedicado através de módulo de segurança (apenas tecla simétrica).

- **Modo** de autenticação do dispositivo<br>
Neste método, o agente de segurança autentica-se pela primeira vez com a identidade do dispositivo. Após a autenticação inicial, o Agente Defender para IoT efetua uma chamada **REST** para o Hub IoT utilizando a API REST com os dados de autenticação do dispositivo. O Agente Defender para IoT solicita então o método de autenticação do módulo de segurança e os dados do IoT Hub. Na etapa final, o agente Defender para IoT realiza uma autenticação contra o módulo Defender para IoT.

Utilize este tipo de autenticação se pretender que o agente de segurança reutile um método de autenticação do dispositivo existente (certificado auto-assinado ou chave simétrica).

Consulte os parâmetros de [instalação do agente](#security-agent-installation-parameters) de segurança para aprender a configurar.

## <a name="authentication-methods-known-limitations"></a>Métodos de autenticação conhecidos limitações

- O modo de autenticação **SecurityModule** suporta apenas a autenticação simétrica da chave.
- CA-Signed certificado não é suportado pelo modo de autenticação **do Dispositivo.**

## <a name="security-agent-installation-parameters"></a>Parâmetros de instalação do agente de segurança

Ao [utilizar um agente de segurança,](how-to-deploy-agent.md)os dados de autenticação devem ser fornecidos como argumentos.
Estes argumentos estão documentados na tabela seguinte.

|Nome do parâmetro linux | Nome do parâmetro do Windows | Parâmetro de abreviatura |Description|Opções|
|---------------------|---------------|---------|---------------|---------------|
|autenticação-identidade|AutenticaçãoDídeo|aui|Identidade de autenticação| **SegurançaModule** ou **Dispositivo**|
|método de autenticação|AutenticaçãoMethod|aum|Método de autenticação|**SimétricaKey** ou **SelfSignedCertificate**|
|caminho de arquivo|FilePath|f|Caminho completo absoluto para o ficheiro que contém o certificado ou a chave simétrica| |
|nome hospedeiro|Nome do Anfitrião|hn|FQDN do IoT Hub|Exemplo: ContosoIotHub.azure-devices.net|
|dispositivo id|DeviceId|di|ID do Dispositivo|Exemplo: MyDevice1|
|tipo certificado-localização|CertificadoLocalizaçãoKind|cl|Local de armazenamento de certificados|**LocalFile** ou **Loja**|
|

Ao utilizar o script do agente de segurança de instalação, a seguinte configuração é executada automaticamente. Para editar manualmente a autenticação do agente de segurança, edite o ficheiro config.

## <a name="change-authentication-method-after-deployment"></a>Alterar método de autenticação após a implantação

Ao implantar um agente de segurança com um script de instalação, é criado automaticamente um ficheiro de configuração.

Para alterar métodos de autenticação após a implementação, é necessária a edição manual do ficheiro de configuração.

### <a name="c-based-security-agent"></a>Agente de segurança baseado em C#

Editar _Authentication.config_ com os seguintes parâmetros:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>Agente de segurança baseado em C

Editar _LocalConfiguration.js_ com os seguintes parâmetros:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```
## <a name="next-steps"></a>Passos seguintes

- [Visão geral dos agentes de segurança](security-agent-architecture.md)
- [Implementar agente de segurança](how-to-deploy-agent.md)
- [Aceder a dados de segurança não processados](how-to-security-data-access.md)
