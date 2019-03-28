---
title: Métodos de autenticação para o ASC para pré-visualização do IoT | Documentos da Microsoft
description: Saiba mais sobre os métodos de autenticação diferentes disponíveis ao utilizar o ASC para o serviço de IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 23bc4d0df1c8124ec225ac31239c7acb3f1ab546
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541816"
---
# <a name="security-agent-authentication-methods"></a>Métodos de autenticação do agente de segurança 

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica os métodos de autenticação diferentes que pode utilizar com o agente de AzureIoTSecurity para autenticar com o IoT Hub.

Para carregar cada dispositivo ao ASC para IoT no IoT Hub, um módulo de segurança é necessário. Para autenticar o dispositivo, o ASC para IoT pode utilizar um dos dois métodos. Escolha o método mais adequado para a sua solução de IoT existente. 

> [!div class="checklist"]
> * Opção de módulo de segurança
> * Opção de dispositivo

## <a name="authentication-methods"></a>Métodos de autenticação

Os dois métodos para o agente de AzureIoTSecurity efetuar a autenticação:

 - **Módulo** modo de autenticação<br>
   O módulo é autenticado independentemente do dispositivo duplo.
   As informações necessárias para este tipo de autenticação está definido pelo arquivo config para C# e o LocalConfiguration.json para C.
        
 - **Dispositivo** modo de autenticação<br>
    Nesse método, o agente de segurança autentica pela primeira vez no dispositivo. Depois da autenticação inicial, faz o ASC para o agente do IoT **Rest** chamar para o IoT Hub com a API Rest com os dados de autenticação do dispositivo. O ASC para o agente de IoT pedidos, em seguida, o método de autenticação do módulo de segurança e os dados do IoT Hub. No passo final, o ASC para IoT agente executa uma autenticação contra o ASC para o módulo de IoT.    

Ver [parâmetros de instalação do agente de segurança](#security-agent-installation-parameters) para aprender a configurar.
                                
## <a name="authentication-methods-known-limitations"></a>Limitações conhecidas de métodos de autenticação

- **Módulo** modo de autenticação só suporta a autenticação de chave simétrica.
- Certificado assinado de AC não é suportado pelo **dispositivo** modo de autenticação.  

## <a name="security-agent-installation-parameters"></a>Parâmetros de instalação do agente de segurança

Quando [implementar um agente de segurança](select-deploy-agent.md), detalhes de autenticação devem ser fornecidos como argumentos.
Estes argumentos estão documentados na tabela seguinte.


|Parâmetro|Descrição|Opções|
|---------|---------------|---------------|
|**identity**|Modo de autenticação| **Módulo** ou **dispositivo**|
|**tipo**|Tipo de autenticação|**SymmetricKey** ou **SelfSignedCertificate**|
|**filePath**|Caminho completo do absoluto para o ficheiro que contém o certificado ou a chave simétrica| |
|**gatewayHostname**|FQDN do IoT Hub|Exemplo: ContosoIotHub.azure-devices.net|
|**deviceId**|ID do dispositivo|Exemplo: MyDevice1|
|**certificateLocationKind**|Localização de armazenamento de certificado|**LocalFile** ou **Store**|


Ao utilizar o script de agente de segurança de instalação, a seguinte configuração é executada automaticamente.

Para editar manualmente a autenticação do agente de segurança, edite o ficheiro de configuração. 

## <a name="change-authentication-method-after-deployment"></a>Alterar método de autenticação após a implementação

Ao implementar um agente de segurança com um script de instalação, é automaticamente criado um ficheiro de configuração.

Para alterar os métodos de autenticação após a implementação, a edição manual do ficheiro de configuração, é necessário.


### <a name="c-based-security-agent"></a>C#-com base em agente de segurança

Editar _config_ com os seguintes parâmetros:

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

### <a name="c-based-security-agent"></a>Agente de segurança baseada em C

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
- [Descrição geral de agentes de segurança](security-agent-architecture.md)
- [Implementar o agente de segurança](select-deploy-agent.md)
- [Aceder a dados não processados de segurança](how-to-security-data-access.md)
