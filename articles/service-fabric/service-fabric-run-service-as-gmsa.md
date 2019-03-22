---
title: Executar um serviço de Azure Service Fabric numa conta gMSA | Documentos da Microsoft
description: Saiba como executar um serviço como uma gMSA num cluster autónomo do Windows do Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 8d14d9191e74cb59c6696568ead425fca61d6f7c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57873908"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Executar um serviço como uma Conta de Serviço Gerida de grupo
Num cluster autónomo do Windows Server, pode executar um serviço como um grupo de conta de serviço gerida (gMSA) através de uma política de RunAs.  Por predefinição, as aplicações do Service Fabric executam sob a conta que o processo de Fabric.exe é executado em. Execução de aplicativos sob diferentes contas, mesmo num ambiente de hospedagem compartilhado, torna mais seguro umas das outras. Tenha em atenção que isto utiliza o Active Directory no local dentro de seu domínio e não do Azure Active Directory (Azure AD). Ao utilizar uma gMSA, não existe nenhuma palavra-passe ou a palavra-passe encriptada armazenado no manifesto do aplicativo.  Também pode executar um serviço como um [utilizador ou grupo do Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

O exemplo seguinte mostra como criar uma conta gMSA denominada *svc e teste$*; como implementar essa conta de serviço gerido para os nós do cluster; e como configurar o principal de utilizador.

Pré-requisitos:
- O domínio tem uma chave de raiz KDS.
- O domínio tem de estar num Windows Server 2012 ou posterior a nível funcional.

1. Peça a um administrador de domínio do Active Directory, criar um serviço de geridas de grupo conta através da `New-ADServiceAccount` commandlet e certifique-se de que o `PrincipalsAllowedToRetrieveManagedPassword` inclui todos os nós de cluster do service fabric. `AccountName`, `DnsHostName`, e `ServicePrincipalName` tem de ser exclusivo.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Em cada um dos recursos de infraestrutura do serviço de nós de cluster (por exemplo, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), instalar e testar a gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configurar o principal de utilizador e configure o RunAsPolicy para fazer referência ao utilizador.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE] 
> Se aplicar uma política de RunAs para um serviço e o manifesto do serviço declara os recursos de ponto final com o protocolo HTTP, tem de especificar um **SecurityAccessPolicy**.  Para obter mais informações, consulte [atribuir uma política de acesso de segurança para pontos finais HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Como passo seguinte, leia os artigos seguintes:
* [Compreender o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos num manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
