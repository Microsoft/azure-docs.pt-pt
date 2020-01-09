---
title: Implantar um aplicativo node. js que usa o MongoDB
description: Orientações sobre como empacotar vários executáveis convidados para implantar em um cluster de Service Fabric do Azure
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4538efc8a2426fc20dd20d1a85edaf6f76bfc649
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614473"
---
# <a name="deploy-multiple-guest-executables"></a>Implementar várias aplicações executáveis convidadas
Este artigo mostra como empacotar e implantar vários executáveis convidados no Azure Service Fabric. Para criar e implantar um único pacote de Service Fabric leia como [implantar um executável convidado para Service Fabric](service-fabric-deploy-existing-app.md).

Embora este passo a passos mostre como implantar um aplicativo com um front-end node. js que usa o MongoDB como o armazenamento de dados, você pode aplicar as etapas a qualquer aplicativo que tenha dependências em outro aplicativo.   

Você pode usar o Visual Studio para produzir o pacote de aplicativos que contém vários executáveis convidados. Consulte [usando o Visual Studio para empacotar um aplicativo existente](service-fabric-deploy-existing-app.md). Depois de adicionar o primeiro executável convidado, clique com o botão direito do mouse no projeto de aplicativo e selecione o **novo serviço de Service Fabric de >** para adicionar o segundo projeto executável convidado à solução. Observação: se você optar por vincular a origem no projeto do Visual Studio, criar a solução do Visual Studio, o garantirá que o pacote de aplicativos esteja atualizado com as alterações na origem. 

## <a name="samples"></a>Exemplos
* [Exemplo de empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemplo de dois executáveis convidados (C# e NodeJS) se comunicando por meio do serviço de cadastramento usando REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Empacotar manualmente o aplicativo executável de vários convidados
Como alternativa, você pode empacotar manualmente o executável convidado. Para obter detalhes, consulte [empacotar manualmente e implantar um executável existente](service-fabric-deploy-existing-app.md#manually-package-and-deploy-an-existing-executable).

### <a name="packaging-the-nodejs-application"></a>Empacotando o aplicativo node. js
Este artigo pressupõe que node. js não está instalado nos nós no Cluster Service Fabric. Como consequência, você precisa adicionar o Node. exe ao diretório raiz do seu aplicativo de nó antes do empacotamento. A estrutura de diretório do aplicativo node. js (usando a estrutura da Web expresso e o mecanismo de modelo Jade) deve ser semelhante à seguinte:

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Como uma próxima etapa, você cria um pacote de aplicativos para o aplicativo node. js. O código a seguir cria um pacote de aplicativo Service Fabric que contém o aplicativo node. js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Abaixo está uma descrição dos parâmetros que estão sendo usados:

* **/Source** aponta para o diretório do aplicativo que deve ser empacotado.
* **/target** define o diretório no qual o pacote deve ser criado. Esse diretório deve ser diferente do diretório de origem.
* **/AppName** define o nome do aplicativo do aplicativo existente. É importante entender que isso se traduz no nome do serviço no manifesto, e não no nome do aplicativo Service Fabric.
* **/exe** define o executável que Service Fabric deve iniciar, nesse caso `node.exe`.
* **/ma** define o argumento que está sendo usado para iniciar o executável. Como o Node. js não está instalado, Service Fabric precisa iniciar o servidor Web node. js executando `node.exe bin/www`.  `/ma:'bin/www'` informa a ferramenta de empacotamento para usar `bin/www` como argumento para node. exe.
* **/AppType** define o nome do tipo de aplicativo Service Fabric.

Se você navegar até o diretório que foi especificado no parâmetro/Target, poderá ver que a ferramenta criou um pacote de Service Fabric totalmente funcional, como mostrado abaixo:

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
O canmanifest. XML gerado agora tem uma seção que descreve como o servidor Web node. js deve ser iniciado, conforme mostrado no trecho de código abaixo:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
Neste exemplo, o servidor Web node. js escuta a porta 3000 e, portanto, você precisa atualizar as informações do ponto de extremidade no arquivo manifest. xml, conforme mostrado abaixo.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Empacotando o aplicativo MongoDB
Agora que você pacoteu o aplicativo node. js, pode prosseguir e empacotar o MongoDB. Como mencionado anteriormente, as etapas que você passa agora não são específicas para node. js e MongoDB. Na verdade, eles se aplicam a todos os aplicativos que devem ser empacotados em conjunto como um aplicativo Service Fabric.  

Para empacotar o MongoDB, você deseja se certificar de que você empacota o mongod. exe e o Mongo. exe. Ambos os binários estão localizados no diretório `bin` do diretório de instalação do MongoDB. A estrutura do diretório é semelhante à mostrada abaixo.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric precisa iniciar o MongoDB com um comando semelhante ao mostrado abaixo, portanto, você precisa usar o parâmetro `/ma` ao empacotar o MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Os dados não serão preservados no caso de uma falha de nó se você colocar o diretório de dados do MongoDB no diretório local do nó. Você deve usar o armazenamento durável ou implementar um conjunto de réplicas do MongoDB para evitar a perda de dados.  
>
>

No PowerShell ou no Shell de comando, executamos a ferramenta de empacotamento com os seguintes parâmetros:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Para adicionar o MongoDB ao seu pacote de aplicativo Service Fabric, você precisa certificar-se de que o parâmetro/Target aponta para o mesmo diretório que já contém o manifesto do aplicativo junto com o aplicativo node. js. Você também precisa se certificar de que está usando o mesmo nome de ApplicationType.

Vamos procurar o diretório e examinar o que a ferramenta criou.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Como você pode ver, a ferramenta adicionou uma nova pasta, MongoDB, ao diretório que contém os binários do MongoDB. Se você abrir o arquivo `ApplicationManifest.xml`, poderá ver que o pacote agora contém o aplicativo node. js e o MongoDB. O código a seguir mostra o conteúdo do manifesto do aplicativo.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Publicando o aplicativo
A última etapa é publicar o aplicativo no cluster de Service Fabric local usando os scripts do PowerShell abaixo:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Depois que o aplicativo for publicado com êxito no cluster local, você poderá acessar o aplicativo node. js na porta que inserimos no manifesto do serviço do aplicativo node. js, por exemplo, http:\//localhost: 3000.

Neste tutorial, você viu como empacotar facilmente dois aplicativos existentes como um aplicativo Service Fabric. Você também aprendeu como implantá-lo no Service Fabric para que ele possa se beneficiar de alguns dos recursos de Service Fabric, como a alta disponibilidade e a integração do sistema de integridade.

## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Adicionando mais executáveis convidados a um aplicativo existente usando o Yeoman no Linux

Para adicionar outro serviço a uma aplicação já criada com o `yo`, execute os seguintes passos: 
1. Altere o diretório para a raiz da aplicação existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é a aplicação criada por Yeoman.
2. Execute `yo azuresfguest:AddService` e forneça os detalhes necessários.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como implantar contêineres com [Service Fabric e contêineres visão geral](service-fabric-containers-overview.md)
* [Exemplo de empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemplo de dois executáveis convidados (C# e NodeJS) se comunicando por meio do serviço de cadastramento usando REST](https://github.com/Azure-Samples/service-fabric-containers)
