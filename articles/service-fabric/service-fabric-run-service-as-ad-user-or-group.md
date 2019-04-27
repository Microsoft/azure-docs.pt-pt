---
title: Executar um serviço de Azure Service Fabric como um utilizador ou grupo | Documentos da Microsoft
description: Saiba como executar um serviço como um utilizador do Active Directory ou o grupo num cluster autónomo do Windows do Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 3e0bb62609f13430bd2beab2332a31983874eb8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837730"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Executar um serviço como um utilizador do Active Directory ou o grupo
Num cluster autónomo do Windows Server, pode executar um serviço como um utilizador do Active Directory ou o grupo através de uma política de RunAs.  Por predefinição, as aplicações do Service Fabric executam sob a conta que o processo de Fabric.exe é executado em. Execução de aplicativos sob diferentes contas, mesmo num ambiente de hospedagem compartilhado, torna mais seguro umas das outras. Tenha em atenção que isto utiliza o Active Directory no local dentro de seu domínio e não do Azure Active Directory (Azure AD).  Também pode executar um serviço como um [conta de serviço gerida (gMSA) de grupo](service-fabric-run-service-as-gmsa.md).

Ao utilizar um utilizador de domínio ou grupo, em seguida, pode aceder a outros recursos no domínio (por exemplo, as partilhas de ficheiros) que tenham sido concedidos permissões.

O exemplo seguinte mostra um utilizador do Active Directory chamado *TestUser* com o seu domínio chamado encriptado através de um certificado de palavra-passe *MyCert*. Pode utilizar o `Invoke-ServiceFabricEncryptText` comando do PowerShell para criar o texto de cifra secreta. Ver [gerir segredos em aplicações do Service Fabric](service-fabric-application-secret-management.md) para obter detalhes.

Tem de implementar a chave privada do certificado para desencriptar a palavra-passe para o computador local utilizando um método de fora de banda (no Azure, este é através do Azure Resource Manager). Em seguida, quando o Service Fabric implementa o pacote de serviço para a máquina, é capaz de desencriptar o segredo e (juntamente com o nome de utilizador) autenticar com o Active Directory para ser executado sob essas credenciais.

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
> Se aplicar uma política de RunAs para um serviço e o manifesto do serviço declara os recursos de ponto final com o protocolo HTTP, também tem de especificar um **SecurityAccessPolicy**.  Para obter mais informações, consulte [atribuir uma política de acesso de segurança para pontos finais HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Como passo seguinte, leia os artigos seguintes:
* [Compreender o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos num manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
