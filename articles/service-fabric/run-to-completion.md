---
title: Semântica RunToCompletion em Tecido de Serviço
description: Descreve a semântica RunToCompletion no Tecido de Serviço.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431296"
---
# <a name="runtocompletion"></a>RuntoCompletion

A partir da versão 7.1, o Tecido de Serviço suporta a semântica **RunToCompletion** para [contentores][containers-introduction-link] e aplicações [executáveis por hóspedes.][guest-executables-introduction-link] Estas semânticas permitem aplicações e serviços que completam uma tarefa e saída, em contraste com, sempre executando aplicações e serviços.

Antes de prosseguir com este artigo, recomendamos que se familiarize com o modelo de [aplicação Service Fabric][application-model-link] e o modelo de [hospedagem do Tecido de Serviço.][hosting-model-link]

> [!NOTE]
> Atualmente, a semântica RunToCompletion não é suportada para serviços escritos utilizando o modelo de programação [de Serviços Fiáveis.][reliable-services-link]
 
## <a name="runtocompletion-semantics-and-specification"></a>Semântica runtoconclusão e especificação
A semântica RunToCompletion pode ser especificada como uma Política de **Execução** ao [importar o ServiceManifest][application-and-service-manifests-link]. A política especificada é herdada por todos os CodePackages que compõem o ServiceManifest. O seguinte snippet ApplicationManifest.xml fornece um exemplo.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**A Política de Execução** permite os seguintes dois atributos:
* **Tipo:** **RunToCompletion** é atualmente o único valor permitido para este atributo.
* **Reiniciar:** Este atributo especifica a política de reinício que é aplicada aos CodePackages que compõem o ServicePackage, por falha. Considera-se que um Código Pacote que sai com um **código de saída não zero** falhou. Os valores permitidos para este atributo são **OnFailure** e **Nunca** com o **OnFailure** sendo o padrão.

Com a política de reinício definida para **o OnFailure**, se algum Código Pacote falhar (código de **saída não zero)**, é reiniciado, com recuos entre falhas repetidas. Com a política de reinício definida para **Nunca**, se algum CodePackage falhar, o estado de implementação do Pacote de Serviços Implantados é marcado como **Failed,** mas outros Códigos Pacotes são autorizados a continuar a execução. Se todos os Pacotes de Código que compõem o ServicePackage funcionarem até à conclusão com sucesso (código de **saída 0)**, o estado de implementação do Pacote de Serviços Implantados é marcado como **RanToCompletion**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Exemplo completo usando semântica RunToComplete

Vejamos um exemplo completo utilizando a semântica RunToComplete.

> [!IMPORTANT]
> O exemplo que se segue assume familiaridade com a criação de [aplicações de contentores Windows utilizando o Service Fabric e o Docker][containers-getting-started-link].
>
> Este exemplo refere-se mcr.microsoft.com/windows/nanoserver:1809. Os recipientes do Windows Server não são compatíveis em todas as versões de um SISTEMA de acolhimento. Para saber mais, consulte a Compatibilidade da [Versão do Recipiente do Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

O seguinte ServiceManifest.xml descreve um ServicePackage composto por dois Códigos Pacotes, que representam contentores. *RunToCompletionCodePackage1* basta registar uma mensagem para **sair** e sair. *RunToCompletionCodePackage2* pinga o endereço de backback por um tempo e, em seguida, sai com um código de saída de **0**, **1** ou **2**.

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

O seguinte ApplicationManifest.xml descreve uma aplicação baseada no ServiceManifest.xml discutido acima. Especifica a Política de **Execução** **RunToCompletion** para *windowsRunToCompletionServicePackage* com uma política de **reinício**do OnFailure . Após a ativação do *WindowsRunToCompletionServicePackage,* os seus Códigos Constituintes serão iniciados. *RunToCompletionCodePackage1* deve sair com sucesso na primeira ativação. No entanto, *o RunToCompletionCodePackage2* pode falhar (código de **saída não zero)**, caso em que será reiniciado uma vez que a política de reinício é **OnFailure**.

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
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Consulta do estado de implantação de um Pacote de Serviços Implantados
O estado de implementação de um Pacote de Serviços Implantados pode ser consultado a partir da PowerShell utilizando o [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] ou a partir de C# utilizando [o FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync (String, Uri, String)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Considerações ao utilizar semântica RunToCompletion

Os seguintes pontos devem ser assinalados para o suporte atual RunToCompletion.
* Estas semânticas são suportadas apenas para [contentores][containers-introduction-link] e aplicações [executáveis por hóspedes.][guest-executables-introduction-link]
* Não são permitidos cenários de upgrade para aplicações com semântica RunToCompletion. Os utilizadores devem eliminar e recriar tais aplicações, se necessário.
* Eventos de failover podem fazer com que os CodePackages reexecutem após a conclusão bem sucedida, no mesmo nó, ou outros nós do cluster. Exemplos de eventos de failover são, reinicia o nó e upgrades de tempo de execução de tecido de serviço em um nó.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter informações relacionadas.

* [Tecido de serviço e recipientes.][containers-introduction-link]
* [Tecido de Serviço e executáveis convidados.][guest-executables-introduction-link]

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
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync

