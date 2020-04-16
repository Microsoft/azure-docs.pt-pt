---
title: Código de inicializadorPacotes em Tecido de Serviço
description: Descreve pacotes de código de inicializador em tecido de serviço.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430633"
---
# <a name="initializer-codepackages"></a>Pacotes de código de inicializador

A partir da versão 7.1, o Tecido de Serviço suporta códigos de **inicializadorpara** [contentores][containers-introduction-link] e aplicações [executáveis por hóspedes.][guest-executables-introduction-link] Os Códigos de Inicializador proporcionam a oportunidade de realizar inicializações no âmbito servicePackage antes de outros Pacotes de Código iniciarem a execução. A sua relação com um ServicePackage é análoga ao que é um [SetupEntryPoint][setup-entry-point-link] para um CodePackage.

Antes de prosseguir com este artigo, recomendamos que se familiarize com o modelo de [aplicação Service Fabric][application-model-link] e o modelo de [hospedagem do Tecido de Serviço.][hosting-model-link]

> [!NOTE]
> Os Códigos de Inicializadornão são atualmente suportados para serviços escritos utilizando o modelo de programação [de Serviços Fiáveis.][reliable-services-link]
 
## <a name="semantics"></a>Semântica

Espera-se que um Código de Inicializador seja executado até à **conclusão com sucesso (código**de saída 0) . Um Código de Inicializador falhado é reiniciado até que esteja concluído com sucesso. São permitidos pacotes de códigode inicializadorm múltiplos e executados para **a conclusão bem sucedida,** **sequencialmente,** **numa ordem especificada** antes de outros Códigos Pacotes no Pacote de Serviço iniciarem a execução.

## <a name="specifying-initializer-codepackages"></a>Especificar pacotes de código de inicializador
Pode marcar um CodePackage como um Inicializador, definindo o atributo **inicializador** **verdadeiro** no ServiceManifest. Quando existem vários Códigos de Inicializador, a sua ordem de execução pode ser especificada através do atributo **ExecOrder.** **A ExecOrder** deve ser um inteiro não negativo e só é válida para pacotes de código de inicializador. Os Códigos de Inicializadorcom valores mais baixos de **ExecOrder** são executados primeiro. Se o **ExecOrder** não for especificado para um Código de Inicializador, é assumido um valor predefinido de 0. A ordem relativa de execução dos Códigos de Inicializadores Com o mesmo valor de **ExecOrder** não é especificada.

O seguinte snippet ServiceManifest descreve três CodePackages dois dos quais são marcados como Inicializadores. Quando este ServicePackage é ativado, o *InitCodePackage0* é executado primeiro, uma vez que tem o valor mais baixo de **ExecOrder**. Após a conclusão com sucesso (código de saída 0) do *InitCodePackage0*, *initCodePackage1* é executado. Finalmente, após a conclusão bem sucedida do *InitCodePackage1,* o Volume de *TrabalhoCódigoPackage* é executado.

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
## <a name="complete-example-using-initializer-codepackages"></a>Exemplo completo usando pacotes de código de inicializador

Vejamos um exemplo completo usando pacotes de código de inicializador.

> [!IMPORTANT]
> O exemplo que se segue assume familiaridade com a criação de [aplicações de contentores Windows utilizando o Service Fabric e o Docker][containers-getting-started-link].
>
> Este exemplo refere-se mcr.microsoft.com/windows/nanoserver:1809. Os recipientes do Windows Server não são compatíveis em todas as versões de um SISTEMA de acolhimento. Para saber mais, consulte a Compatibilidade da [Versão do Recipiente do Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

O seguinte ServiceManifest.xml baseia-se no snippet ServiceManifest descrito anteriormente. *InitCodePackage0*, *InitCodePackage1* e *WorkloadCodePackagesão* CodePackages que representam contentores. Após a ativação, *o InitCodePackage0* é executado primeiro. Regista uma mensagem num ficheiro e sai. Em seguida, *o InitCodePackage1* é executado, que também regista uma mensagem para um ficheiro e saídas. Finalmente, o *Pacote Código de Carga de Trabalho* começa a execução. Também regista uma mensagem num ficheiro, envia o conteúdo do ficheiro para **stdout** e, em seguida, pings para sempre.

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

O seguinte ApplicationManifest.xml descreve uma aplicação baseada no ServiceManifest.xml discutido acima. Note que especifica o **mesmo** volume de montagem para todos os recipientes, ou seja, **C:\WorkspaceOnHost** é montado em **C:\WorkspaceOnContainer** em todos os três recipientes. O efeito líquido é que todos os recipientes escrevem para o mesmo ficheiro de registo na ordem em que são ativados.

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
* [Tecido de Serviço e executáveis convidados.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

