---
title: CodePackages de inicializador em tecido de serviço
description: Descreve o CodePackages inicializador no tecido de serviço.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 3be079b97c2660437344f88203fdda06cc6d6740
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86258983"
---
# <a name="initializer-codepackages"></a>CodePackages do Inicializador

A partir da versão 7.1, o Service Fabric suporta **codepackages inicializadores** para [contentores][containers-introduction-link] e aplicações [executáveis de hóspedes.][guest-executables-introduction-link] Os CodePackages inicializadores proporcionam a oportunidade de efetuar inicializações no âmbito ServicePackage antes que outros CodePackages comecem a ser executados. A sua relação com um ServicePackage é análoga ao que é um [SetupEntryPoint][setup-entry-point-link] para um CodePackage.

Antes de prosseguir com este artigo, recomendamos conhecer o modelo de [aplicação de Tecido de Serviço][application-model-link] e o modelo de [hospedagem do Tecido de Serviço.][hosting-model-link]

> [!NOTE]
> Os CodePackages inicializadores não são atualmente suportados para serviços escritos utilizando o modelo de programação [de Serviços Fidedigtos.][reliable-services-link]
 
## <a name="semantics"></a>Semântica

Espera-se que um CodePackage inicializado seja executado **com sucesso (código de saída 0)**. Um CodePackage inicializador falhado é reiniciado até completar com sucesso. Vários CodePackages inicializadores são permitidos e executados para **conclusão com sucesso,** **sequencialmente,** **numa ordem especificada** antes de outros CodePackages no ServicePackage começarem a ser executados.

## <a name="specifying-initializer-codepackages"></a>Especificar codepackages inicializadores
Pode marcar um CodePackage como um Initializer definindo o atributo **Initializer** para **verdadeiro** no ServiceManifest. Quando existem vários CodePackages inicializadores, a sua ordem de execução pode ser especificada através do atributo **ExecOrder.** **O ExecOrder** deve ser um número inteiro não negativo e só é válido para CodePackages inicializador. Os CodePackages de inicializador com valores mais baixos de **ExecOrder** são executados primeiro. Se **o ExecOrder** não for especificado para um CodePackage inicializador, assume-se um valor padrão de 0. Não é especificada a ordem de execução relativa de Initializer CodePackages com o mesmo valor de **ExecOrder.**

O seguinte snippet ServiceManifest descreve três CodePackages dois dos quais são marcados como Inicializadores. Quando este ServicePackage é ativado, *o InitCodePackage0* é executado primeiro, uma vez que tem o valor mais baixo de **ExecOrder**. Após a conclusão com sucesso (código de saída 0) do *InitCodePackage0,* o *InitCodePackage1* é executado. Finalmente, após a conclusão bem sucedida do *InitCodePackage1,* *o WorkloadCodePackage* é executado.

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Exemplo completo usando CodePackages inicializador

Vejamos um exemplo completo usando CodePackages inicializador.

> [!IMPORTANT]
> O exemplo a seguir pressupõe a familiaridade com a criação [de aplicações de contentores Windows utilizando o Service Fabric e o Docker.][containers-getting-started-link]
>
> Este exemplo refere-se mcr.microsoft.com/windows/nanoserver:1809. Os recipientes do Windows Server não são compatíveis em todas as versões de um sistema operativo anfitrião. Para saber mais, consulte a [compatibilidade da versão do recipiente do Windows](/virtualization/windowscontainers/deploy-containers/version-compatibility).

O seguinte ServiceManifest.xml baseia-se no snippet ServiceManifest descrito anteriormente. *InitCodePackage0*, *InitCodePackage1* e *WorkloadCodePackage* são CodePackages que representam contentores. Após a ativação, *o InitCodePackage0* é executado primeiro. Regista uma mensagem num ficheiro e sai. Em seguida, *o InitCodePackage1* é executado, que também regista uma mensagem num ficheiro e sai. Finalmente, a *WorkloadCodePackage* começa a ser executada. Também regista uma mensagem num ficheiro, coloca o conteúdo do ficheiro em **stdout** e, em seguida, pings para sempre.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

O ApplicationManifest.xml seguinte descreve uma aplicação com base no ServiceManifest.xml discutido acima. Note que especifica o mesmo suporte de **volume** para todos os recipientes, ou seja, **C:\WorkspaceOnHost** é montado em **C:\WorkspaceOnContainer** nos três recipientes. O efeito líquido é que todos os recipientes escrevem para o mesmo ficheiro de registo na ordem em que são ativados.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
Uma vez ativado com sucesso o ServicePackage, o conteúdo de **C:\WorkspaceOnHost\log.txt** deve ser o seguinte.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

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
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
