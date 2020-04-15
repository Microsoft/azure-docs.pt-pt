---
title: Métodos de autenticação de agentes de segurança
description: Conheça os diferentes métodos de autenticação disponíveis ao utilizar o Centro de Segurança Azure para o serviço IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d9d51292c3cae9634af917819b558cdfd2fa04b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311521"
---
# <a name="security-agent-authentication-methods"></a>Métodos de autenticação de agentes de segurança

Este artigo explica os diferentes métodos de autenticação que pode utilizar com o agente AzureIoTSecurity para autenticar com o Hub IoT.

Para cada dispositivo a bordo do Azure Security Center for IoT no IoT Hub, é necessário um módulo de segurança. Para autenticar o dispositivo, o Azure Security Center for IoT pode utilizar um de dois métodos. Escolha o método que funciona melhor para a sua solução IoT existente.

> [!div class="checklist"]
> * Opção SecurityModule
> * Opção do dispositivo

## <a name="authentication-methods"></a>Métodos de autenticação

Os dois métodos para o agente AzureIoTSecurity realizar a autenticação:

- Modo de autenticação **SecurityModule**<br>
O agente é autenticado utilizando a identidade do módulo de segurança independentemente da identidade do dispositivo.
Utilize este tipo de autenticação se quiser que o agente de segurança utilize um método de autenticação dedicado através de um módulo de segurança (apenas chave simétrica).

- **Modo** de autenticação do dispositivo<br>
Neste método, o agente de segurança autentica-se primeiro com a identidade do dispositivo. Após a autenticação inicial, o Centro de Segurança Azure para o agente IoT realiza uma chamada **REST** para o Hub IoT utilizando a API REST com os dados de autenticação do dispositivo. O Centro de Segurança Azure para o agente IoT solicita então o método de autenticação do módulo de segurança e os dados do IoT Hub. No passo final, o Azure Security Center for IoT agent realiza uma autenticação contra o Azure Security Center para módulo IoT.

Utilize este tipo de autenticação se quiser que o agente de segurança reutilize um método de autenticação do dispositivo existente (certificado auto-assinado ou chave simétrica).

Consulte [os parâmetros](#security-agent-installation-parameters) de instalação do agente de segurança para aprender a configurar.

## <a name="authentication-methods-known-limitations"></a>Métodos de autenticação conhecidos limitações

- O modo de autenticação **SecurityModule** suporta apenas a autenticação simétrica da chave.
- O certificado CA-Signed não é suportado pelo modo de autenticação do **Dispositivo.**

## <a name="security-agent-installation-parameters"></a>Parâmetros de instalação de agentes de segurança

Ao implantar um agente de [segurança,](how-to-deploy-agent.md)os detalhes da autenticação devem ser fornecidos como argumentos.
Estes argumentos estão documentados na tabela seguinte.

|Nome do parâmetro linux | Nome do parâmetro do windows | Parâmetro de mão curta |Descrição|Opções|
|---------------------|---------------|---------|---------------|---------------|
|autenticação-identidade|Identidade de Autenticação|aui|Identidade de autenticação| **Módulo** de Segurança ou **Dispositivo**|
|método de autenticação|Método de Autenticação|aum|Método de autenticação|**Certificado simétrico chave** ou **auto-assinado**|
|arquivo-caminho|Caminho de Arquivo|f|Caminho completo absoluto para o ficheiro que contém o certificado ou a chave simétrica| |
|nome anfitrião|NomedeAnfitrião|hn|FQDN do IoT Hub|Exemplo: ContosoIotHub.azure-devices.net|
|dispositivo-id|DeviceId|di|ID do dispositivo|Exemplo: MyDevice1|
|certificado-localização tipo|CertificadoS|cl|Local de armazenamento de certificado|**Arquivo Local** ou **Loja**|
|

Ao utilizar o script do agente de segurança de instalação, a seguinte configuração é executada automaticamente. Para editar a autenticação manual do agente de segurança, edite o ficheiro config.

## <a name="change-authentication-method-after-deployment"></a>Alterar o método de autenticação após a implantação

Ao implantar um agente de segurança com um script de instalação, é criado automaticamente um ficheiro de configuração.

Para alterar os métodos de autenticação após a implementação, é necessária a edição manual do ficheiro de configuração.

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

Editar _LocalConfiguration.json_ com os seguintes parâmetros:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Consulte também

- [Visão geral dos agentes de segurança](security-agent-architecture.md)
- [Implementar agente de segurança](how-to-deploy-agent.md)
- [Aceder a dados de segurança não processados](how-to-security-data-access.md)
