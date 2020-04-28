---
title: Executar um serviço Azure Service Fabric como utilizador ou grupo de anúncios
description: Aprenda a executar um serviço como utilizador ou grupo de Directórioativo num cluster autónomo do Service Fabric Windows.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: d440aadb66562e32331c9725a9367c12440a315d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75464242"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Executar um serviço como utilizador ou grupo de Diretório Ativo
Num cluster autónomo do Windows Server, pode executar um serviço como utilizador ou grupo de Directórioactivo utilizando uma política de RunAs.  Por predefinição, as aplicações de Tecido de Serviço são executadas sob a conta que o processo Fabric.exe é executado. Executar aplicações sob contas diferentes, mesmo num ambiente partilhado, torna-as mais seguras umas das outras. Note que isto utiliza diretório ativo no local dentro do seu domínio e não o Azure Ative Directory (Azure AD).  Também pode executar um serviço como uma conta de serviço gerida por [grupo (gMSA)](service-fabric-run-service-as-gmsa.md).

Ao utilizar um utilizador ou grupo de domínio, pode então aceder a outros recursos no domínio (por exemplo, partilhas de ficheiros) que tenham sido concedidas permissões.

O exemplo seguinte mostra um utilizador do Diretório Ativo chamado *TestUser* com a sua palavra-passe de domínio encriptada utilizando um certificado chamado *MyCert*. Pode utilizar `Invoke-ServiceFabricEncryptText` o comando PowerShell para criar o texto secreto da cifra. Consulte [a Gestão de segredos nas aplicações de Tecido de Serviço](service-fabric-application-secret-management.md) para mais detalhes.

Deve implementar a chave privada do certificado para desencriptar a palavra-passe para a máquina local utilizando um método fora de banda (em Azure, isto é via Azure Resource Manager). Em seguida, quando o Service Fabric implanta o pacote de serviço para a máquina, é capaz de desencriptar o segredo e (juntamente com o nome de utilizador) autenticar com Ative Directory para executar sob essas credenciais.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Se aplicar uma política de RunAs a um serviço e o manifesto de serviço declarar recursos de ponto final com o protocolo HTTP, também deve especificar uma Política de **Acesso de Segurança**.  Para mais informações, consulte [Atribuir uma política de acesso à segurança para os pontos finais HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Como próximo passo, leia os seguintes artigos:
* [Compreender o modelo de aplicação](service-fabric-application-model.md)
* [Especificar recursos num manifesto de serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
