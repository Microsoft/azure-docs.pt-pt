---
title: Executar um serviço de Service Fabric do Azure como um usuário ou grupo do AD
description: Saiba como executar um serviço como um Active Directory usuário ou grupo em um Cluster Service Fabric Windows autônomo.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: d440aadb66562e32331c9725a9367c12440a315d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464242"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Executar um serviço como um Active Directory usuário ou grupo
Em um cluster autônomo do Windows Server, você pode executar um serviço como um Active Directory usuário ou grupo usando uma política RunAs.  Por padrão, Service Fabric aplicativos são executados sob a conta sob a qual o processo Fabric. exe é executado. Executar aplicativos em contas diferentes, mesmo em um ambiente hospedado compartilhado, os torna mais seguros uns dos outros. Observe que isso usa Active Directory local em seu domínio e não Azure Active Directory (AD do Azure).  Você também pode executar um serviço como uma [conta de serviço gerenciado de grupo (gMSA)](service-fabric-run-service-as-gmsa.md).

Usando um usuário ou grupo de domínio, você pode acessar outros recursos no domínio (por exemplo, compartilhamentos de arquivos) que receberam permissões.

O exemplo a seguir mostra um usuário Active Directory chamado *testuser* com sua senha de domínio criptografada usando um certificado chamado *MyCert*. Você pode usar o comando `Invoke-ServiceFabricEncryptText` PowerShell para criar o texto cifrado secreto. Consulte [Gerenciando segredos em Service Fabric aplicativos](service-fabric-application-secret-management.md) para obter detalhes.

Você deve implantar a chave privada do certificado para descriptografar a senha para o computador local usando um método fora de banda (no Azure, isso ocorre por meio de Azure Resource Manager). Em seguida, quando Service Fabric implanta o pacote de serviço no computador, ele é capaz de descriptografar o segredo e (junto com o nome de usuário) autenticar com Active Directory para ser executado sob essas credenciais.

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
> Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, você também deverá especificar um **SecurityAccessPolicy**.  Para obter mais informações, consulte [atribuir uma política de acesso de segurança para pontos de extremidade http e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Como uma próxima etapa, leia os seguintes artigos:
* [Entender o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
