---
title: Executar um serviço azure service fabric sob uma conta gMSA
description: Aprenda a executar um serviço como uma Conta de Serviço Gerida por grupo (gMSA) num cluster autónomo do Service Fabric Windows.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988401"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Executar um serviço como uma Conta de Serviço Gerida de grupo

Num cluster autónomo do Windows Server, pode executar um serviço como uma conta de *serviço gerida* por grupo (gMSA) utilizando uma política de *RunAs.*  Por predefinição, as aplicações de `Fabric.exe` Tecido de Serviço são executadas sob a conta a que o processo é executado. Executar aplicações sob contas diferentes, mesmo num ambiente partilhado, torna-as mais seguras umas das outras. Ao utilizar um gMSA, não existe senha ou senha encriptada armazenada no manifesto da aplicação.  Também pode executar um serviço como [utilizador ou grupo de Directórioactivo.](service-fabric-run-service-as-ad-user-or-group.md)

O exemplo seguinte mostra como criar uma conta gMSA chamada *svc-Test$,* como implementar essa conta de serviço gerida para os nós do cluster, e como configurar o principal do utilizador.

> [!NOTE]
> A utilização de um gMSA com um cluster de tecido de serviço autónomo requer diretório ativo no seu domínio (em vez de Azure Ative Directory (Azure AD)).

Pré-requisitos:

- O domínio precisa de uma chave de raiz KDS.
- Deve haver pelo menos um Dc Do Windows Server 2012 (ou R2) no domínio.

1. Ter um administrador de domínio ative Directy criar `New-ADServiceAccount` uma conta de serviço `PrincipalsAllowedToRetrieveManagedPassword` gerida pelo grupo utilizando o cmdlet e garantir que todos os nós de cluster de tecido de serviço. `AccountName`E `DnsHostName` `ServicePrincipalName` deve ser único.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Em cada um dos nós de cluster `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`de tecido de serviço (por exemplo, instale e teste o gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configure o diretor do Utilizador `RunAsPolicy` e configure o de referência para o [Utilizador](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas).
    
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
> Se aplicar uma política de RunAs a um serviço e o manifesto de serviço declarar recursos de ponto final com o protocolo HTTP, deve especificar uma Política de **Acesso de Segurança**.  Para mais informações, consulte [Atribuir uma política de acesso à segurança para os pontos finais HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md).
>

Os seguintes artigos irão guiá-lo através dos próximos passos:

- [Compreender o modelo de aplicação](service-fabric-application-model.md)
- [Especificar recursos num manifesto de serviço](service-fabric-service-manifest-resources.md)
- [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
