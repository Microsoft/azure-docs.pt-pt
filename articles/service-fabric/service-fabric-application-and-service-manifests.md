---
title: Descrevendo aplicativos e serviços de tecido de serviço Azure
description: Descreve como os manifestos são usados para descrever aplicações e serviços do Service Fabric.
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: fcf4c7611f0a6f52c28b234717b9244ac58ad2d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86248225"
---
# <a name="service-fabric-application-and-service-manifests"></a>Aplicação de tecido de serviço e manifestos de serviço
Este artigo descreve como as aplicações e serviços do Service Fabric são definidos e ver versão usando os ficheiros ApplicationManifest.xml e ServiceManifest.xml.  Para exemplos mais detalhados, consulte [exemplos de manifestos de aplicação e serviço.](service-fabric-manifest-examples.md)  O esquema XML para estes ficheiros manifestos está documentado na [documentação do esquema ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

> [!WARNING]
> O manifesto esquema de ficheiro XML impõe a ordem correta dos elementos infantis.  Como uma solução parcial, abra "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" em Visual Studio enquanto autoriza ou modifica qualquer um dos manifestos do Tecido de Serviço. Isto permitir-lhe-á verificar a encomenda de elementos infantis e fornecer um sentido intelli.

## <a name="describe-a-service-in-servicemanifestxml"></a>Descreva um serviço em ServiceManifest.xml
O manifesto de serviço define declarativamente o tipo de serviço e a versão. Especifica metadados de serviço como tipo de serviço, propriedades de saúde, métricas de equilíbrio de carga, binários de serviço e ficheiros de configuração.  Dito de outra forma, descreve o código, configuração e pacotes de dados que compõem um pacote de serviço para suportar um ou mais tipos de serviço. Um manifesto de serviço pode conter vários pacotes de código, configuração e dados, que podem ser versados de forma independente. Aqui está um manifesto de serviço para o ASP.NET Serviço frontal core web da [aplicação](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) da amostra de voto (e aqui estão [alguns exemplos mais detalhados):](service-fabric-manifest-examples.md)

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

**Os** atributos da versão são cordas não estruturadas e não são analisados pelo sistema. Os atributos da versão são usados para ver cada componente para upgrades.

**OsTipos de Serviço** declaram que tipos de serviço são suportados pelo **CodePackages** neste manifesto. Quando um serviço é instantâneo contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Espera-se que os processos resultantes registem os tipos de serviço suportados no tempo de execução. Os tipos de serviço são declarados ao nível manifesto e não ao nível do pacote de código. Assim, quando existem vários pacotes de código, todos eles são ativados sempre que o sistema procura qualquer um dos tipos de serviço declarados.

O executável especificado pelo **EntryPoint** é tipicamente o anfitrião de serviço de longa duração. **SetupEntryPoint** é um ponto de entrada privilegiado que funciona com as mesmas credenciais que o Service Fabric (normalmente a conta *LocalSystem)* antes de qualquer outro ponto de entrada.  A presença de um ponto de entrada de configuração separado evita ter de executar o hospedeiro de serviço com privilégios elevados por longos períodos de tempo. O executável especificado pelo **EntryPoint** é executado após **a saída de SetupEntryPoint** com sucesso. Se o processo alguma vez terminar ou falhar, o processo resultante é monitorizado e reiniciado (começando novamente com **o SetupEntryPoint).**  

Os cenários típicos para a utilização **do SetupEntryPoint** são quando executa uma execução executável antes do início do serviço ou executa uma operação com privilégios elevados. Por exemplo:

* Configuração e inicialização de variáveis ambientais que o serviço necessita. Isto não se limita apenas a executáveis escritos através dos modelos de programação do Service Fabric. Por exemplo, npm.exe precisa de algumas variáveis ambientais configuradas para implementar uma aplicação node.js.
* Criação de controlo de acesso através da instalação de certificados de segurança.

Para obter mais informações sobre como configurar o SetupEntryPoint, consulte [Configurar a política para um ponto](service-fabric-application-runas-security.md) de entrada de configuração de serviço

**EnvironmentVariables** (não definido no exemplo anterior) fornece uma lista de variáveis ambientais que estão definidas para este pacote de código. As variáveis ambientais podem ser ultrapassadas na `ApplicationManifest.xml` torção para fornecer valores diferentes para diferentes instâncias de serviço. 

**DataPackage** (não definido no exemplo anterior) declara uma pasta, nomeada pelo atributo **Name,** que contém dados estáticos arbitrários para serem consumidos pelo processo no tempo de execução.

**A ConfigPackage** declara uma pasta, denominada pelo atributo **Nome,** que contém um ficheiro *Settings.xml.* O ficheiro de definições contém secções de definições de pares de valor chave definidas pelo utilizador que o processo lê no tempo de execução. Durante uma atualização, se apenas a **versão** **ConfigPackage** tiver mudado, então o processo de execução não é reiniciado. Em vez disso, uma chamada notifica o processo que as definições de configuração mudaram para que possam ser recarregadas dinamicamente. Aqui está um exemplo *Settings.xml* ficheiro:

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Um Serviço de Serviço de Serviço **Endpoint** é um exemplo de um recurso de tecido de serviço. Um recurso de tecido de serviço pode ser declarado/alterado sem alterar o código compilado. O acesso aos Recursos de Tecido de Serviço especificados no manifesto de serviço pode ser controlado através do **SecurityGroup** no manifesto de aplicação. Quando um Recurso endpoint é definido no manifesto de serviço, o Service Fabric atribui portas da gama portuária de aplicações reservadas quando uma porta não é especificada explicitamente. Leia mais sobre [especificar ou sobreaferírlar recursos de ponto final](service-fabric-service-manifest-resources.md).

 
> [!WARNING]
> Por conceção, as portas estáticas não devem sobrepor-se à gama de portas de aplicação especificada no ClusterManifest. Se especificar uma porta estática, atribua-a fora da gama de portas de aplicação, caso contrário resultará em conflitos portuários. Com o lançamento 6.5CU2 emitiremos um **Aviso sanitário** quando detetarmos tal conflito, mas deixaremos que a implantação continue em sintonia com o comportamento enviado 6.5. No entanto, podemos impedir a implementação da aplicação das próximas grandes versões.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Descreva uma aplicação em ApplicationManifest.xml
O manifesto da aplicação descreve declarativamente o tipo e versão da aplicação. Especifica metadados de composição de serviço como nomes estáveis, esquema de partição, fator de contagem/replicação de casos, política de segurança/isolamento, restrições de colocação, substituições de configuração e tipos de serviço constituinte. Os domínios de equilíbrio de carga nos quais a aplicação é colocada também são descritos.

Assim, um manifesto de aplicação descreve elementos ao nível da aplicação e refere um ou mais manifestos de serviço para compor um tipo de aplicação. Aqui está o manifesto de candidatura para o [pedido de amostra de voto](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (e aqui estão [alguns exemplos mais detalhados):](service-fabric-manifest-examples.md)

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
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Tal como os manifestos de serviço, os atributos **de versão** são cordas não estruturadas e não são analisados pelo sistema. Os atributos da versão também são usados para ver cada componente para upgrades.

**Os parâmetros** definem os parâmetros utilizados ao longo do manifesto de aplicação. Os valores destes parâmetros podem ser fornecidos quando a aplicação é instantânea e pode substituir as definições de aplicação ou configuração de serviço.  O valor do parâmetro padrão é utilizado se o valor não for alterado durante a instantânea da aplicação. Para aprender a manter diferentes parâmetros de aplicação e serviço para ambientes individuais, consulte [a Gestão de parâmetros de aplicação para vários ambientes.](service-fabric-manage-multiple-environment-app-configuration.md)

**ServiceManifestImport** contém referências a manifestos de serviço que compõem este tipo de aplicação. Um manifesto de aplicação pode conter várias importações manifestas de serviço, cada uma pode ser versada de forma independente. Manifestos de serviço importados determinam que tipos de serviço são válidos dentro deste tipo de aplicação. Dentro do ServiceManifestImport, você sobrepõe os valores de configuração em variáveis Settings.xml e ambiente em ficheiros ServiceManifest.xml. **As políticas** (não definidas no exemplo anterior) para a ligação de ponto final, a segurança e o acesso, e a partilha de pacotes podem ser definidas em manifestos de serviço importados.  Para mais informações, consulte [as políticas de segurança configure para a sua aplicação.](service-fabric-application-runas-security.md)

**O DefaultServices** declara casos de serviço que são automaticamente criados sempre que uma aplicação é instantânea contra este tipo de aplicação. Os serviços predefinidos são apenas uma conveniência e comportam-se como serviços normais em todos os aspetos após a sua criação. São atualizados juntamente com quaisquer outros serviços na aplicação e podem ser removidos também. Um manifesto de aplicação pode conter vários serviços predefinidos.

**Os certificados** (não definidos no exemplo anterior) declaram os certificados utilizados para [configurar pontos finais HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) ou [encriptar segredos no manifesto de aplicação](service-fabric-application-secret-management.md).

**Restrições de colocação** são as declarações que definem onde os serviços devem ser executados. Estas declarações estão anexadas a serviços individuais que seleciona para uma ou mais propriedades de nó. Para obter mais informações, consulte [restrições de colocação e sintaxe de propriedade de nó](./service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-property-syntax)

**As políticas** (não definidas no exemplo anterior) descrevem a recolha de registos, as políticas de acesso por [defeito,](service-fabric-application-runas-security.md) [saúde](service-fabric-health-introduction.md#health-policies)e acesso à [segurança](service-fabric-application-runas-security.md) a definir ao nível da aplicação, incluindo se os serviços(s) têm acesso ao tempo de funcionamento do Tecido de Serviço.

> [!NOTE] 
> Por predefinição, as aplicações do Service Fabric têm acesso ao tempo de funcionamento do Tecido de Serviço, sob a forma de um ponto final que aceita pedidos específicos de aplicação, e variáveis ambientais que apontam para os caminhos de arquivo no hospedeiro que contém ficheiros de tecido e aplicações. Considere desativar este acesso quando a aplicação apresentar código falso (ou seja, código cuja proveniência é desconhecida, ou que o titular da aplicação sabe não ser seguro para executar). Para mais informações, consulte as [melhores práticas de segurança em Service Fabric.](service-fabric-best-practices-security.md#platform-isolation) 
>

**Os principais** (não definidos no exemplo anterior) descrevem os princípios de segurança (utilizadores ou grupos) necessários para [executar serviços e garantir recursos de serviços](service-fabric-application-runas-security.md).  Os principais são referenciados nas secções **políticas.**





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Passos seguintes
- [Embale uma aplicação](service-fabric-package-apps.md) e prepare-a para ser implantada.
- [Implementar e remover aplicações](service-fabric-deploy-remove-applications.md).
- [Configure parâmetros e variáveis ambientais para diferentes instâncias de aplicação](service-fabric-manage-multiple-environment-app-configuration.md).
- [Configure as políticas de segurança para a sua aplicação.](service-fabric-application-runas-security.md)
- [Configurar pontos finais HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Criptografe segredos no manifesto da aplicação](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
