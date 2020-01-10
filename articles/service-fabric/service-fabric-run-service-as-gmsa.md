---
title: Executar um serviço de Service Fabric do Azure em uma conta do gMSA
description: Saiba como executar um serviço como um gMSA em um Cluster Service Fabric Windows autônomo.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 99d8089bd12d05e46f91e55c933d58d50baa92f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464268"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Executar um serviço como uma Conta de Serviço Gerida de grupo
Em um cluster autônomo do Windows Server, você pode executar um serviço como uma conta de serviço gerenciado de grupo (gMSA) usando uma política RunAs.  Por padrão, Service Fabric aplicativos são executados sob a conta sob a qual o processo Fabric. exe é executado. Executar aplicativos em contas diferentes, mesmo em um ambiente hospedado compartilhado, os torna mais seguros uns dos outros. Observe que isso usa Active Directory local em seu domínio e não Azure Active Directory (AD do Azure). Usando um gMSA, não há senha ou senha criptografada armazenada no manifesto do aplicativo.  Você também pode executar um serviço como um [Active Directory usuário ou grupo](service-fabric-run-service-as-ad-user-or-group.md).

O exemplo a seguir mostra como criar uma conta do gMSA chamada *svc-Test $* ; como implantar essa conta de serviço gerenciado nos nós do cluster; e como configurar a entidade de usuário.

Pré-requisitos:
- O domínio precisa de uma chave raiz KDS.
- Deve haver pelo menos um DC do Windows Server 2012 (ou R2) no domínio.

1. Tenha um administrador de domínio Active Directory criar uma conta de serviço gerenciado de grupo usando o `New-ADServiceAccount` commandlet e verifique se o `PrincipalsAllowedToRetrieveManagedPassword` inclui todos os nós de cluster do Service Fabric. `AccountName`, `DnsHostName`e `ServicePrincipalName` devem ser exclusivos.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Em cada um dos nós de Cluster Service Fabric (por exemplo, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), instale e teste o gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configure a entidade de usuário e configure o RunAsPolicy para fazer referência ao usuário.
    
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
> Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, você deverá especificar um **SecurityAccessPolicy**.  Para obter mais informações, consulte [atribuir uma política de acesso de segurança para pontos de extremidade http e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Como uma próxima etapa, leia os seguintes artigos:
* [Entender o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
