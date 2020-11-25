---
title: Semântica RunToCompletion em Tecido de Serviço
description: Descreve a semântica RunToCompletion em Tecido de Serviço.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000271"
---
# <a name="runtocompletion"></a>RunToCompletion

Começando pela versão 7.1, o Service Fabric suporta a semântica **RunToCompletion** para [contentores][containers-introduction-link] e aplicações [executáveis de hóspedes.][guest-executables-introduction-link] Estas semântica permitem aplicações e serviços que completam uma tarefa e saída, ao contrário de, sempre executando aplicações e serviços.

Antes de prosseguir com este artigo, recomendamos conhecer o modelo de [aplicação de Tecido de Serviço][application-model-link] e o modelo de [hospedagem do Tecido de Serviço.][hosting-model-link]

> [!NOTE]
> Atualmente, a semântica RunToCompletion não é suportada para serviços escritos utilizando o modelo de programação [dos Serviços Fiáveis.][reliable-services-link]
 
## <a name="runtocompletion-semantics-and-specification"></a>Semântica runToCompletion e especificação
A semântica RunToCompletion pode ser especificada como uma Política de **Execução** ao [importar o ServiceManifest][application-and-service-manifests-link]. A política especificada é herdada por todos os CodePackages que compõem o ServiceManifest. O seguinte ApplicationManifest.xml snippet fornece um exemplo.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**Execução A Política** permite os seguintes dois atributos:
* **Tipo:** **RunToCompletion** é atualmente o único valor permitido para este atributo.
* **Reiniciar:** Este atributo especifica a política de reinício que é aplicada aos CodePackages que compõem o ServicePackage, por falha. Considera-se que um CodePackage que sai com um **código de saída não zero** falhou. Os valores permitidos para este atributo são **OnFailure** e **Never** with **OnFailure** sendo o padrão.

Com a política de reinício definida para **OnFailure**, se algum CodePackage falhar **(código de saída não zero)** é reiniciado, com recuos entre falhas repetidas. Com a política de reinício definida para **Nunca**, se algum CodePackage falhar, o estado de implantação do Pacote de Serviços Implantados é marcado como **Falhado,** mas outros CodePackages podem continuar a executar. Se todos os CodePackages que compõem o ServicePackage funcionarem com sucesso **(código de saída 0)**, o estado de implantação do Serviço de Serviço implantado é marcado como **RanToCompletion**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Exemplo completo usando semântica RunToCompletion

Vejamos um exemplo completo usando a semântica RunToCompletion.

> [!IMPORTANT]
> O exemplo a seguir pressupõe a familiaridade com a criação [de aplicações de contentores Windows utilizando o Service Fabric e o Docker.][containers-getting-started-link]
>
> Este exemplo refere-se mcr.microsoft.com/windows/nanoserver:1809. Os recipientes do Windows Server não são compatíveis em todas as versões de um sistema operativo anfitrião. Para saber mais, consulte a [compatibilidade da versão do recipiente do Windows](/virtualization/windowscontainers/deploy-containers/version-compatibility).

O seguinte ServiceManifest.xml descreve um ServicePackage composto por dois CodePackages, que representam contentores. *RunToCompletionCodePackage1* apenas regista uma mensagem para **stdout** e saídas. *RunToCompletionCodePackage2* pings o endereço loopback por um tempo e, em seguida, sai com um código de saída de **0**, **1** ou **2**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

O ApplicationManifest.xml seguinte descreve uma aplicação com base no ServiceManifest.xml discutido acima. Especifica **runToCompletion** **ExecutionPolicy** for *WindowsRunToCompletionServicePackage* com uma política de reinício do **OnFailure**. Após a ativação do *WindowsRunToCompletionServicePackage,* será iniciado o seu CodePackages constituinte. *RunToCompletionCodePackage1* deve sair com sucesso na primeira ativação. No entanto, *o RunToCompletionCodePackage2* pode falhar **(código de saída não zero)**, caso em que será reiniciado uma vez que a política de reinício é **OnFailure**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Consulta do estado de implantação de um Pacote de Serviços Implantado
O estado de implantação de um Serviço de Serviço implementado pode ser consultado a partir de PowerShell utilizando [o Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] ou a partir de C# utilizando [o FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync (String, Uri, String)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Considerações ao utilizar a semântica RunToCompletion

Os seguintes pontos devem ser anotados para o atual suporte RunToCompletion.
* Estas semânticas são suportadas apenas para [contentores][containers-introduction-link] e aplicações [executáveis de hóspedes.][guest-executables-introduction-link]
* Não são permitidos cenários de atualização para aplicações com semântica RunToCompletion. Os utilizadores devem eliminar e recriar tais aplicações, se necessário.
* Os eventos de failover podem fazer com que o CodePackages re execute após a conclusão com sucesso, no mesmo nó, ou outros nós do cluster. Exemplos de eventos de failover são, o recomâneo do nó e atualizações de tempo de execução do Tecido de Serviço num nó.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter informações relacionadas.

* [Tecido de serviço e recipientes.][containers-introduction-link]
* [Tecido de serviço e executáveis de hóspedes.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync
