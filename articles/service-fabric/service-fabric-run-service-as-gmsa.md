---
title: Executar um serviço de Service Fabric do Azure em uma conta do gMSA
description: Aprenda a executar um serviço como uma Conta de Serviço Gerida por grupo (gMSA) num cluster autónomo do Service Fabric Windows.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988401"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Executar um serviço como uma Conta de Serviço Gerida de grupo

Num cluster autónomo do Windows Server, pode executar um serviço como uma conta de *serviço gerida* por grupo (gMSA) utilizando uma política de *RunAs.*  Por predefinição, as aplicações de Tecido de Serviço são executadas sob a conta que o processo `Fabric.exe` é executado. Executar aplicativos em contas diferentes, mesmo em um ambiente hospedado compartilhado, os torna mais seguros uns dos outros. Usando um gMSA, não há senha ou senha criptografada armazenada no manifesto do aplicativo.  Você também pode executar um serviço como um [Active Directory usuário ou grupo](service-fabric-run-service-as-ad-user-or-group.md).

O exemplo seguinte mostra como criar uma conta gMSA chamada *svc-Test$,* como implementar essa conta de serviço gerida para os nós do cluster, e como configurar o principal do utilizador.

> [!NOTE]
> A utilização de um gMSA com um cluster de tecido de serviço autónomo requer diretório ativo no seu domínio (em vez de Azure Ative Directory (Azure AD)).

Pré-requisitos:

- O domínio precisa de uma chave raiz KDS.
- Deve haver pelo menos um DC do Windows Server 2012 (ou R2) no domínio.

1. Ter um administrador de domínio ative Directy criar uma conta de serviço gerida pelo grupo utilizando o `New-ADServiceAccount` cmdlet e garantir que o `PrincipalsAllowedToRetrieveManagedPassword` inclui todos os nós de cluster de tecido de serviço. `AccountName`, `DnsHostName`e `ServicePrincipalName` devem ser exclusivos.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Em cada um dos nós de Cluster Service Fabric (por exemplo, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), instale e teste o gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configure o diretor do Utilizador e configure o `RunAsPolicy` para fazer referência ao [Utilizador](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas).
    
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

Os seguintes artigos irão guiá-lo através dos próximos passos:

- [Entender o modelo de aplicativo](service-fabric-application-model.md)
- [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
- [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
