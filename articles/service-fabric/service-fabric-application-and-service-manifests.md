---
title: Descrevendo aplicativos e serviços do Azure Service Fabric | Microsoft Docs
description: Descreve como os manifestos são usados para descrever Service Fabric aplicativos e serviços.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/12/2019
ms.author: atsenthi
ms.openlocfilehash: a5e452bf3dc9f35c345a5f27af829904b4839ece
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977115"
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric manifestos de aplicativo e serviço
Este artigo descreve como Service Fabric aplicativos e serviços são definidos e com controle de versão usando os arquivos ApplicationManifest. xml e manifest. xml.  Para obter exemplos mais detalhados, consulte [exemplos de manifesto de aplicativo e serviço](service-fabric-manifest-examples.md).  O esquema XML para esses arquivos de manifesto está documentado na [documentação do esquema ServiceFabricServiceModel. xsd](service-fabric-service-model-schema.md).

> [!WARNING]
> O esquema de arquivo XML do manifesto impõe a ordenação correta dos elementos filho.  Como uma solução alternativa parcial, abra "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" no Visual Studio durante a criação ou modificação de qualquer um dos manifestos Service Fabric. Isso permitirá que você verifique a ordem dos elementos filho e fornecerá o IntelliType.

## <a name="describe-a-service-in-servicemanifestxml"></a>Descrever um serviço no Service manifest. xml
O manifesto do serviço declarativamente define o tipo de serviço e a versão. Ele especifica os metadados do serviço, como o tipo de serviço, as propriedades de integridade, as métricas de balanceamento de carga, os binários de serviço e os arquivos de configuração.  Em outras palavras, ele descreve o código, a configuração e os pacotes de dados que compõem um pacote de serviço para dar suporte a um ou mais tipos de serviço. Um manifesto de serviço pode conter vários pacotes de código, configuração e dados, que podem ter sua versão independente. Aqui está um manifesto de serviço para o serviço de front-end da Web ASP.NET Core do [aplicativo de exemplo de votação](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (e aqui estão alguns [exemplos mais detalhados](service-fabric-manifest-examples.md)):

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

Os atributos de **versão** são cadeias de caracteres não estruturadas e não analisados pelo sistema. Os atributos de versão são usados para a versão de cada componente para atualizações.

Os ServiceName declara quais tipos de serviço são suportados pelo **CodePackages** neste manifesto. Quando um serviço é instanciado em relação a um desses tipos de serviço, todos os pacotes de código declarados nesse manifesto são ativados executando seus pontos de entrada. Os processos resultantes são esperados para registrar os tipos de serviço com suporte em tempo de execução. Os tipos de serviço são declarados no nível do manifesto e não no nível do pacote de código. Assim, quando houver vários pacotes de código, eles serão todos ativados sempre que o sistema procurar por qualquer um dos tipos de serviço declarados.

O executável especificado pelo **EntryPoint** normalmente é o host de serviço de longa execução. **SetupEntryPoint** é um ponto de entrada privilegiado que é executado com as mesmas credenciais que Service Fabric (normalmente a conta *LocalSystem* ) antes de qualquer outro ponto de entrada.  A presença de um ponto de entrada de instalação separado evita a execução do host de serviço com altos privilégios por longos períodos de tempo. O executável especificado pelo **EntryPoint** é executado depois que **SetupEntryPoint** é encerrado com êxito. Se o processo for encerrado ou falhar, o processo resultante será monitorado e reiniciado (começando novamente com **SetupEntryPoint**).  

Cenários típicos para usar **SetupEntryPoint** são quando você executa um executável antes do início do serviço ou realiza uma operação com privilégios elevados. Por exemplo:

* Configurar e inicializar variáveis de ambiente que o executável de serviço precisa. Isso não se limita apenas a executáveis escritos por meio dos modelos de programação de Service Fabric. Por exemplo, NPM. exe precisa de algumas variáveis de ambiente configuradas para implantar um aplicativo node. js.
* Configurando o controle de acesso instalando certificados de segurança.

Para obter mais informações sobre como configurar o SetupEntryPoint, consulte [Configurar a política para um ponto de entrada de instalação de serviço](service-fabric-application-runas-security.md)

**EnvironmentVariables** (não definido no exemplo anterior) fornece uma lista de variáveis de ambiente que são definidas para este pacote de códigos. As `ApplicationManifest.xml` variáveis de ambiente podem ser substituídas no para fornecer valores diferentes para diferentes instâncias de serviço. 

**DataPackage** (não definido no exemplo anterior) declara uma pasta, nomeada pelo atributo **Name** , que contém dados estáticos arbitrários a serem consumidos pelo processo em tempo de execução.

**ConfigPackage** declara uma pasta, nomeada pelo atributo **Name** , que contém um arquivo *Settings. xml* . O arquivo de configurações contém seções de configurações de par chave-valor definidas pelo usuário que o processo lê de volta no tempo de execução. Durante uma atualização, se apenas a **versão** do ConfigPackage tiver sido alterada, o processo em execução não será reiniciado. Em vez disso, um retorno de chamada notifica o processo de que as definições de configuração foram alteradas para que possam ser recarregadas dinamicamente. Aqui está um exemplo de arquivo *Settings. xml* :

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Um ponto de **extremidade** de serviço Service Fabric é um exemplo de um recurso de Service Fabric. Um recurso de Service Fabric pode ser declarado/alterado sem alterar o código compilado. O acesso aos recursos de Service Fabric que são especificados no manifesto do serviço pode ser controlado por meio do The de **segurança** no manifesto do aplicativo. Quando um recurso de ponto de extremidade é definido no manifesto do serviço, Service Fabric atribui portas do intervalo de portas do aplicativo reservado quando uma porta não é especificada explicitamente. Leia mais sobre como [especificar ou substituir recursos de ponto de extremidade](service-fabric-service-manifest-resources.md).

 
> [!WARNING]
> As portas estáticas de design não devem se sobrepor ao intervalo de portas de aplicativo especificado em ClusterManifest. Se você especificar uma porta estática, atribua-a fora do intervalo de portas do aplicativo, caso contrário, isso resultará em conflitos de porta. Com a versão 6.5 CU2, emitiremos um **aviso de integridade** quando detectarmos esse conflito, mas permitirá que a implantação continue em sincronia com o comportamento 6,5 enviado. No entanto, poderemos impedir a implantação do aplicativo nas próximas versões principais.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Descrever um aplicativo em ApplicationManifest. xml
O manifesto do aplicativo descreve declarativamente o tipo e a versão do aplicativo. Ele especifica metadados de composição de serviço, como nomes estáveis, esquema de particionamento, fator de replicação/contagem de instâncias, política de segurança/isolamento, restrições de posicionamento, substituições de configuração e tipos de serviço constituintes. Os domínios de balanceamento de carga nos quais o aplicativo é colocado também são descritos.

Assim, um manifesto de aplicativo descreve os elementos no nível do aplicativo e faz referência a um ou mais manifestos do serviço para compor um tipo de aplicativo. Aqui está o manifesto do aplicativo para o [aplicativo de exemplo de votação](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (e aqui estão alguns [exemplos mais detalhados](service-fabric-manifest-examples.md)):

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

Assim como os manifestos de serviço, os atributos de **versão** são cadeias de caracteres não estruturados e não são analisados pelo sistema. Os atributos de versão também são usados para a versão de cada componente para atualizações.

**Parâmetros** define os parâmetros usados em todo o manifesto do aplicativo. Os valores desses parâmetros podem ser fornecidos quando o aplicativo é instanciado e pode substituir as definições de configuração de aplicativo ou serviço.  O valor do parâmetro padrão será usado se o valor não for alterado durante a instanciação do aplicativo. Para saber como manter diferentes parâmetros de aplicativo e de serviço para ambientes individuais, consulte [Gerenciando parâmetros de aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** contém referências a manifestos de serviço que compõem esse tipo de aplicativo. Um manifesto de aplicativo pode conter várias importações de manifesto de serviço, cada uma delas pode ter sua versão independente. Os manifestos do serviço importados determinam quais tipos de serviço são válidos nesse tipo de aplicativo. Dentro do ServiceManifestImport, você substitui os valores de configuração em Settings. xml e variáveis de ambiente nos arquivos do arquivo manifest. xml. **Políticas** do (não definido no exemplo anterior) para associação de ponto de extremidade, segurança e acesso e compartilhamento de pacotes podem ser definidos em manifestos de serviço importados.  Para obter mais informações, consulte [Configurar políticas de segurança para seu aplicativo](service-fabric-application-runas-security.md).

**DefaultServices** declara as instâncias de serviço que são criadas automaticamente sempre que um aplicativo é instanciado em relação a esse tipo de aplicativo. Os serviços padrão são apenas uma conveniência e se comportam como serviços normais em todos os aspectos depois de terem sido criados. Eles são atualizados juntamente com quaisquer outros serviços na instância do aplicativo e também podem ser removidos. Um manifesto de aplicativo pode conter vários serviços padrão.

**Certificados** do (não definido no exemplo anterior) declara os certificados usados para [Configurar pontos de extremidade https](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) ou criptografar [segredos no manifesto do aplicativo](service-fabric-application-secret-management.md).

As **restrições de posicionamento** são as instruções que definem onde os serviços devem ser executados. Essas instruções são anexadas a serviços individuais que você seleciona para uma ou mais propriedades de nó. Para obter mais informações, consulte [restrição de posicionamento e sintaxe de propriedade de nó](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#placement-constraints-and-node-property-syntax)

**Políticas** do (não definido no exemplo anterior) descreve a coleta de log, [execução padrão](service-fabric-application-runas-security.md), [integridade](service-fabric-health-introduction.md#health-policies)e políticas de [acesso de segurança](service-fabric-application-runas-security.md) para definir no nível do aplicativo, incluindo se os serviços têm acesso ao tempo de execução de Service Fabric.

> [!NOTE] 
> Por padrão, os aplicativos Service Fabric têm acesso ao tempo de execução do Service Fabric, na forma de um ponto de extremidade que aceita solicitações específicas do aplicativo e variáveis de ambiente apontando para caminhos de arquivo no host que contém arquivos específicos de aplicativo e de malha . Considere desabilitar esse acesso quando o aplicativo hospedar código não confiável (ou seja, código cuja comprovação é desconhecida ou que o proprietário do aplicativo sabe que não deve ser seguro de ser executado). Para obter mais informações, consulte [práticas recomendadas de segurança em Service Fabric](service-fabric-best-practices-security.md#platform-isolation). 
>

**Entidades de segurança** (não definido no exemplo anterior) descreva as entidades de segurança (usuários ou grupos) necessárias para [executar serviços e proteger os recursos de serviço](service-fabric-application-runas-security.md).  As entidades de segurança são referenciadas nas seções de **políticas** .





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Passos seguintes
- [Empacotar um aplicativo](service-fabric-package-apps.md) e deixá-lo pronto para ser implantado.
- [Implantar e remover aplicativos](service-fabric-deploy-remove-applications.md).
- [Configure parâmetros e variáveis de ambiente para diferentes instâncias de aplicativo](service-fabric-manage-multiple-environment-app-configuration.md).
- [Configure as políticas de segurança para seu aplicativo](service-fabric-application-runas-security.md).
- [Configurar pontos de extremidade https](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Criptografar segredos no manifesto do aplicativo](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



