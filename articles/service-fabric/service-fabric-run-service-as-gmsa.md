---
title: Executar um serviço de tecido de serviço Azure sob uma conta gMSA
description: Saiba como executar um serviço como uma conta de serviço gerida por grupo (gMSA) num cluster autónomo do Service Fabric Windows.
ms.topic: how-to
ms.date: 03/29/2018
ms.openlocfilehash: 9750042764306c5df7a391429cc6926704db05ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91838913"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Executar um serviço como uma Conta de Serviço Gerida de grupo

Num cluster autónomo do Windows Server, pode executar um serviço como uma *conta de serviço gerida por grupo* (gMSA) utilizando uma política *RunAs.*  Por predefinição, as aplicações do Service Fabric são executadas sob a conta que o `Fabric.exe` processo executa. Executar aplicações em diferentes contas, mesmo num ambiente partilhado, torna-as mais seguras umas das outras. Ao utilizar um gMSA, não existe senha ou senha encriptada armazenada no manifesto da aplicação.  Também pode executar um serviço como [utilizador ou grupo ative diretório.](service-fabric-run-service-as-ad-user-or-group.md)

O exemplo a seguir mostra como criar uma conta gMSA chamada *svc-Test$*, como implementar essa conta de serviço gerida para os nós do cluster, e como configurar o principal utilizador.

> [!NOTE]
> A utilização de um gMSA com um cluster de tecido de serviço autónomo requer diretório ativo no local dentro do seu domínio (em vez de Azure Ative Directory (Azure AD)).

Pré-requisitos:

- O domínio precisa de uma chave de raiz KDS.
- Deve haver pelo menos um Windows Server 2012 (ou R2) DC no domínio.

1. Tenha um administrador de domínio ative Directory criar uma conta de serviço gerida pelo grupo utilizando o `New-ADServiceAccount` cmdlet e certifique-se de que `PrincipalsAllowedToRetrieveManagedPassword` inclui todos os nós de cluster de Tecido de Serviço. `AccountName`E `DnsHostName` `ServicePrincipalName` deve ser único.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Em cada um dos nós de cluster de tecido de serviço (por exemplo, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$` ), instale e teste o gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configure o Diretor do Utilizador e configuure o `RunAsPolicy` para fazer referência ao [Utilizador](./service-fabric-cluster-fabric-settings.md#runas).
    
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
> Se aplicar uma política runAs a um serviço e o manifesto de serviço declarar recursos de ponto final com o protocolo HTTP, deve especificar uma **SegurançaAccessPolicy**.  Para obter mais informações, consulte [atribuir uma política de acesso à segurança para pontos finais HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md).
>

Os seguintes artigos irão guiá-lo através dos próximos passos:

- [Compreender o modelo de aplicação](service-fabric-application-model.md)
- [Especificar recursos num manifesto de serviço](service-fabric-service-manifest-resources.md)
- [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
