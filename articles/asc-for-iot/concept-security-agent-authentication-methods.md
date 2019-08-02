---
title: Métodos de autenticação para a central de segurança do Azure para IoT | Microsoft Docs
description: Saiba mais sobre os diferentes métodos de autenticação disponíveis ao usar a central de segurança do Azure para o serviço de IoT.
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
ms.openlocfilehash: 16f7f91e02d118d9f9a295ebb79a6cd0187dd9fd
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596463"
---
# <a name="security-agent-authentication-methods"></a>Métodos de autenticação do agente de segurança 

Este artigo explica os diferentes métodos de autenticação que você pode usar com o agente AzureIoTSecurity para autenticar com o Hub IoT.

Para cada dispositivo integrado à central de segurança do Azure para IoT no Hub IoT, é necessário um módulo de segurança. Para autenticar o dispositivo, a central de segurança do Azure para IoT pode usar um dos dois métodos. Escolha o método que funciona melhor para sua solução de IoT existente. 

> [!div class="checklist"]
> * Opção SecurityModule
> * Opção de dispositivo

## <a name="authentication-methods"></a>Métodos de autenticação

Os dois métodos para o agente AzureIoTSecurity realizar a autenticação:

 - Modo de autenticação **SecurityModule**<br>
   O agente é autenticado usando a identidade do módulo de segurança independentemente da identidade do dispositivo.
   Use esse tipo de autenticação se desejar que o agente de segurança Use um método de autenticação dedicado por meio do módulo de segurança (somente chave simétrica).
        
 - Modo de autenticação do **dispositivo**<br>
    Nesse método, o agente de segurança primeiro é autenticado com a identidade do dispositivo. Após a autenticação inicial, a central de segurança do Azure para o agente de IoT executa uma chamada **REST** para o Hub IOT usando a API REST com os dados de autenticação do dispositivo. A central de segurança do Azure para agente de IoT solicita o método de autenticação e os dados do módulo de segurança do Hub IoT. Na etapa final, a central de segurança do Azure para o agente de IoT executa uma autenticação em relação à central de segurança do Azure para o módulo IoT.
    
    Use esse tipo de autenticação se desejar que o agente de segurança reutilize um método de autenticação de dispositivo existente (certificado autoassinado ou chave simétrica). 

Consulte [parâmetros de instalação do agente de segurança](#security-agent-installation-parameters) para saber como configurar o.
                                
## <a name="authentication-methods-known-limitations"></a>Limitações conhecidas dos métodos de autenticação

- O modo de autenticação **SecurityModule** dá suporte apenas à autenticação de chave simétrica.
- Não há suporte para o certificado assinado por uma autoridade de certificação no modo de autenticação do **dispositivo** .  

## <a name="security-agent-installation-parameters"></a>Parâmetros de instalação do agente de segurança

Ao [implantar um agente de segurança](how-to-deploy-agent.md), os detalhes de autenticação devem ser fornecidos como argumentos.
Esses argumentos são documentados na tabela a seguir.


|Nome do parâmetro do Linux | Nome do parâmetro do Windows | Parâmetro abreviado |Descrição|Opções|
|---------------------|---------------|---------|---------------|---------------|
|autenticação-identidade|AuthenticationIdentity|aui|Identidade de autenticação| **SecurityModule** ou **dispositivo**|
|método de autenticação|AuthenticationMethod|aum|Método de autenticação|**SymmetricKey** ou **SelfSignedCertificate**|
|caminho do arquivo|FilePath|fixo|Caminho completo absoluto para o arquivo que contém o certificado ou a chave simétrica| |
|nome do host|nome de anfitrião|HN|FQDN do Hub IoT|Exemplo: ContosoIotHub.azure-devices.net|
|ID do dispositivo|DeviceId|di|ID do Dispositivo|Exemplo: MyDevice1|
|tipo de local de certificado|CertificateLocationKind|l|Local de armazenamento do certificado|**Local** ou **repositório**|
|


Ao usar o script de instalação do agente de segurança, a configuração a seguir é executada automaticamente. Para editar a autenticação do agente de segurança manualmente, edite o arquivo de configuração. 

## <a name="change-authentication-method-after-deployment"></a>Alterar o método de autenticação após a implantação

Ao implantar um agente de segurança com um script de instalação, um arquivo de configuração é criado automaticamente.

Para alterar os métodos de autenticação após a implantação, a edição manual do arquivo de configuração é necessária.


### <a name="c-based-security-agent"></a>C#Agente de segurança baseado em

Edite o _Authentication. config_ com os seguintes parâmetros:

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

Edite _LocalConfiguration. JSON_ com os seguintes parâmetros:

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
- [Implantar agente de segurança](how-to-deploy-agent.md)
- [Acessar dados brutos de segurança](how-to-security-data-access.md)
