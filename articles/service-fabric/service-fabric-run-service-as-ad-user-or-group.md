---
title: Executar um serviço de tecido de serviço Azure como utilizador ou grupo de AD
description: Saiba como executar um serviço como utilizador ou grupo ative directory num cluster autónomo do Service Fabric Windows.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: d4a7afc2ddb0f39014a7cf0fd006d7fe23673a95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840732"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Executar um serviço como utilizador ou grupo de Diretório Ativo
Num cluster autónomo do Windows Server, pode executar um serviço como utilizador ou grupo de Diretório Ativo utilizando uma política RunAs.  Por predefinição, as aplicações do Service Fabric são executadas sob a conta que o processo de Fabric.exe é executado. Executar aplicações em diferentes contas, mesmo num ambiente partilhado, torna-as mais seguras umas das outras. Note que isto utiliza o Ative Directory no local dentro do seu domínio e não o Azure Ative Directory (Azure AD).  Também pode executar um serviço como conta de [serviço gerido do grupo (gMSA)](service-fabric-run-service-as-gmsa.md).

Ao utilizar um utilizador ou grupo de domínio, pode então aceder a outros recursos no domínio (por exemplo, partilhas de ficheiros) que tenham sido concedidas permissões.

O exemplo a seguir mostra um utilizador do Ative Directory chamado *TestUser* com a sua senha de domínio encriptada através de um certificado chamado *MyCert*. Pode utilizar o `Invoke-ServiceFabricEncryptText` comando PowerShell para criar o texto secreto da cifra. Consulte [as aplicações Managing Secrets in Service Fabric](service-fabric-application-secret-management.md) para obter mais detalhes.

Tem de implantar a chave privada do certificado para desencriptar a palavra-passe para a máquina local utilizando um método fora de banda (em Azure, isto é através do Azure Resource Manager). Em seguida, quando o Service Fabric implanta o pacote de serviço para a máquina, é capaz de desencriptar o segredo e (juntamente com o nome de utilizador) autenticar com o Ative Directory para executar sob essas credenciais.

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
> Se aplicar uma política runAs a um serviço e o manifesto de serviço declarar recursos de ponto final com o protocolo HTTP, também deve especificar uma **SecurityAccessPolicy**.  Para obter mais informações, consulte [atribuir uma política de acesso à segurança para pontos finais HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Como passo seguinte, leia os seguintes artigos:
* [Compreender o modelo de aplicação](service-fabric-application-model.md)
* [Especificar recursos num manifesto de serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
