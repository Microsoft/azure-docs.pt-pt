---
title: Descrição de aplicações do Azure Service Fabric e serviços | Documentos da Microsoft
description: Descreve como os manifestos são utilizados para descrever serviços e aplicações do Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/19/2018
ms.author: ryanwi
ms.openlocfilehash: 0cd8131a0e5168ba8cfb6045c3be238162668995
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864580"
---
# <a name="service-fabric-application-and-service-manifests"></a>Aplicação do Service Fabric e manifestos de serviço
Este artigo descreve como os serviços e aplicações do Service Fabric são definidos e com a versão usando os arquivos applicationmanifest. XML e servicemanifest. XML.  Para obter exemplos mais detalhados, consulte [exemplos de manifesto de aplicação e serviço](service-fabric-manifest-examples.md).  O esquema XML para esses arquivos de manifesto está documentado no [documentação de esquema ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

> [!WARNING]
> O esquema do ficheiro de manifesto XML impõe ordem correta dos elementos filho.  Como solução parcial, abra "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" no Visual Studio ao mesmo tempo de criação ou modificação de qualquer um dos manifestos de Service Fabric. Isso permitirá que verifique a ordenação dos elementos subordinados e fornece intelli sentido.

## <a name="describe-a-service-in-servicemanifestxml"></a>Descrever um serviço no servicemanifest. XML
Manifesto do serviço declarativamente define o tipo de serviço e a versão. Especifica os metadados de serviço, como o tipo de serviço, propriedades de estado de funcionamento, métricas de balanceamento de carga, os binários do serviço e ficheiros de configuração.  Em outras palavras, ele descreve os pacotes de código, configuração e dados que compõem um pacote de serviço para dar suporte a um ou mais tipos de serviço. Um manifesto de serviço pode conter vários código, configuração e pacotes de dados, o que podem ser atualizados de forma independente. Aqui está um manifesto de serviço para serviço de front-end da web ASP.NET Core do [exemplo de aplicação de voto](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (e aqui estão alguns [obter exemplos mais detalhados](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**Versão** atributos são cadeias de caracteres não estruturadas e não analisar pelo sistema. Atributos de versão são utilizados para versão de cada componente para atualizações.

**ServiceTypes** declara que tipos de serviço são suportados pelo **CodePackages** nesse manifesto. Quando um serviço é instanciado em relação a um dos seguintes tipos de serviço, todos os pacotes do código declarados nesse manifesto são ativados através da execução de seus pontos de entrada. Os processos resultantes devem registrar os tipos de serviço com suporte em tempo de execução. Tipos de serviço são declarados em nível de manifesto e não no nível de pacote de código. Então, quando há vários pacotes de código, eles são todas ativados sempre que o sistema procura por qualquer um dos tipos de serviço declarado.

O executável especificado por **EntryPoint** é, normalmente, o anfitrião do serviço de execução longa. **SetupEntryPoint** é um ponto de entrada com privilégios que é executado com as mesmas credenciais do Service Fabric (normalmente, o *LocalSystem* conta) antes de qualquer outro ponto de entrada.  A presença de um ponto de entrada de configuração individual evita ter de executar o host de serviço com privilégios elevados por longos períodos de tempo. O executável especificado por **EntryPoint** é executada **SetupEntryPoint** será encerrada com êxito. Se o processo nunca termina ou falha, o processo resultante é monitorizado e reiniciado (a partir do novamente **SetupEntryPoint**).  

Cenários típicos para usar **SetupEntryPoint** são quando executar um executável antes de é iniciado o serviço ou efetuar uma operação com privilégios elevados. Por exemplo:

* Como configurar e inicializar variáveis de ambiente que tem do executável do serviço. Isso não está limitado a apenas os executáveis gravados via os modelos de programação do Service Fabric. Por exemplo, npm.exe tem algumas variáveis de ambiente configurados para implementar uma aplicação node. js.
* Configuração do controlo de acesso através da instalação de certificados de segurança.

Para obter mais informações sobre como configurar o SetupEntryPoint, consulte [configurar a política para um ponto de entrada de configuração de serviço](service-fabric-application-runas-security.md)

**EnvironmentVariables** (não definido no exemplo anterior) fornece uma lista de variáveis de ambiente que estão definidas para este pacote de código. Variáveis de ambiente podem ser substituídas no `ApplicationManifest.xml` para fornecer valores diferentes para instâncias de serviço diferentes. 

**DataPackage** (não definido no exemplo anterior) declara uma pasta, com o nome do **nome** atributo, que contém dados arbitrários de estáticos para ser consumida pelo processo de tempo de execução.

**ConfigPackage** declara uma pasta, com o nome do **Name** atributo, que contém um *Settings* ficheiro. O arquivo de configurações contém seções de definições de par chave-valor, de definidas pelo utilizador que o processo lê voltar no tempo de execução. Durante uma atualização, se apenas a **ConfigPackage** **versão** tiver sido alterado, em seguida, o processo em execução não for reiniciado. Em vez disso, um retorno de chamada notifica o processo que as definições de configuração foram alterados para que possa ser recarregadas dinamicamente. Eis um exemplo *Settings* ficheiro:

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Um serviço do Service Fabric **ponto final** é um exemplo de um recurso de infraestrutura do serviço; Um recurso de infraestrutura do serviço pode ser declarado/alterar sem alterar o código compilado. O acesso para os recursos de infraestrutura do serviço que são especificados no manifesto do serviço pode ser controlado através da **SecurityGroup** no manifesto do aplicativo. Quando um recurso de ponto final é definido no manifesto do serviço, o Service Fabric atribui portas o intervalo de portas reservado do aplicativo quando uma porta não for especificada explicitamente. Leia mais sobre [especificando ou a substituição de recursos de ponto final](service-fabric-service-manifest-resources.md).


<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Descrever um aplicativo em applicationmanifest. XML
O manifesto do aplicativo declarativamente descreve o tipo de aplicação e a versão. Especifica os metadados de composição de serviço, tais como nomes de estáveis, criação de partições de esquema, fator de replicação/contagem de instância, política de segurança/isolamento, restrições de posicionamento, substituições de configuração e tipos de serviço que constituem. Os domínios de balanceamento de carga no qual o aplicativo é colocado também estão descritos.

Assim, um manifesto de aplicativo descreve elementos ao nível da aplicação e faz referência a um ou mais manifestos de serviço para compor um tipo de aplicação. Aqui está o manifesto do aplicativo para o [exemplo de aplicação de voto](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (e aqui estão alguns [obter exemplos mais detalhados](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Como os manifestos de serviço, **versão** atributos são cadeias de caracteres não estruturadas e não são analisados pelo sistema. Atributos de versão também são utilizadas para versão de cada componente para atualizações.

**Parâmetros** define os parâmetros utilizados em todo o manifesto do aplicativo. Os valores destes parâmetros podem ser fornecidos quando o aplicativo é instanciado e pode substituir a aplicação ou definições de configuração do serviço.  O valor predefinido do parâmetro é utilizado se o valor não é alterado durante a instanciação do aplicativo. Para saber como manter o aplicativo diferente e parâmetros de serviço para ambientes individuais, veja [gerir parâmetros da aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** contém referências a manifestos de serviço que compõem este tipo de aplicação. Um manifesto de aplicação pode conter várias importações de manifesto de serviço, cada um deles pode ser atualizado de forma independente. Manifestos de serviço importado determinam que tipos de serviço são válidos dentro deste tipo de aplicação. Dentro de ServiceManifestImport, substituir valores de configuração em variáveis de ambiente e Settings nos ficheiros de servicemanifest. XML. **Políticas** (não definido no exemplo anterior) para a ligação de ponto de extremidade, segurança e acesso e pacote partilha pode ser definida em manifestos de serviço importado.  Para obter mais informações, consulte [configurar políticas de segurança para a sua aplicação](service-fabric-application-runas-security.md).

**DefaultServices** declara as instâncias de serviço que são criadas automaticamente sempre que um aplicativo é instanciado em relação a este tipo de aplicação. Serviços padrão são apenas uma conveniência e comportam-se como serviços normais em todos os aspectos depois de terem sido criados. Eles são atualizados juntamente com quaisquer outros serviços na instância da aplicação e podem ser removidos também. Um manifesto de aplicação pode conter vários serviços de predefinição.

**Certificados** (não definido no exemplo anterior) declara os certificados utilizados para [configurar pontos finais HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) ou [encriptar os segredos no manifesto do aplicativo](service-fabric-application-secret-management.md).

**Políticas** (não definido no exemplo anterior) descreve a recolha de registos [predefinição Run](service-fabric-application-runas-security.md), [estado de funcionamento](service-fabric-health-introduction.md#health-policies), e [acesso de segurança](service-fabric-application-runas-security.md) políticas para definir na nível de aplicativo.

**Principais** (não definido no exemplo anterior) descrever os princípios de segurança (utilizadores ou grupos) necessário para [execução de serviços e recursos de serviço seguro](service-fabric-application-runas-security.md).  Principais são referenciados na **políticas** secções.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Passos Seguintes
- [Empacotar um aplicativo](service-fabric-package-apps.md) e torná-lo pronto para implementar.
- [Implementar e remover aplicações](service-fabric-deploy-remove-applications.md).
- [Configurar parâmetros e variáveis de ambiente para diferentes instâncias da aplicação](service-fabric-manage-multiple-environment-app-configuration.md).
- [Configurar políticas de segurança para a sua aplicação](service-fabric-application-runas-security.md).
- [Configurar pontos finais HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Encriptar os segredos no manifesto do aplicativo](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



