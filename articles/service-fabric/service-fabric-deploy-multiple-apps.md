---
title: Implementar uma aplicação de node. js que utiliza o MongoDB para o Azure Service Fabric | Documentos da Microsoft
description: Passo a passo sobre como empacotar vários executáveis convidados para implementar um cluster do Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4d9dfbcfc1dd00209a90386bf75f0c2515a28060
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57854471"
---
# <a name="deploy-multiple-guest-executables"></a>Implementar vários executáveis convidados
Este artigo mostra como empacotar e implementar vários executáveis convidados para o Azure Service Fabric. Para criar e implementar um único pacote de recursos de infraestrutura do serviço Leia como a [implementar um executável de convidado no Service Fabric](service-fabric-deploy-existing-app.md).

Embora estas instruções mostram como implementar uma aplicação com um node. js front-end que utiliza o MongoDB como arquivo de dados, pode aplicar os passos para qualquer aplicação que apresenta dependências em outro aplicativo.   

Pode utilizar o Visual Studio para produzir o pacote de aplicação que contém vários executáveis convidados. Ver [usando o Visual Studio para empacotar um aplicativo existente](service-fabric-deploy-existing-app.md). Depois de ter adicionado o primeiro executável de convidado, clique com o botão direito do rato no projeto de aplicação e selecione o **adicionar -> serviço novo do Service Fabric** para adicionar o segundo projeto executáveis de convidado para a solução. Nota: Se optar por ligar a origem no projeto do Visual Studio, compilar a solução do Visual Studio, serão Certifique-se de que o seu pacote de aplicação é atualizado com as alterações na origem. 

## <a name="samples"></a>Amostras
* [Exemplo para o empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemplo de dois convidado executáveis (c# e nodejs) comunicar através do serviço de nomenclatura com REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>O pacote manualmente vários aplicação executável convidada
Em alternativa pode empacotar manualmente o convidado executável. Para o empacotamento manual, este artigo utiliza a ferramenta de empacotamento do Service Fabric, que está disponível em [ http://aka.ms/servicefabricpacktool ](https://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Empacotar a aplicação node. js
Este artigo pressupõe que o node. js não está instalado em nós no cluster do Service Fabric. Como conseqüência, terá de adicionar Node.exe para o diretório de raiz da sua aplicação de nó antes de empacotamento. A estrutura do diretório da aplicação node. js (com a arquitetura de web Express e o mecanismo de modelo Jade) deve ter um aspeto semelhante à abaixo:

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

Como passo seguinte, crie um pacote de aplicação para a aplicação node. js. O código abaixo cria um pacote de aplicação do Service Fabric que contém a aplicação node. js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Segue-se uma descrição dos parâmetros que estão a ser utilizados:

* **/Source** aponta para o diretório do aplicativo que deve ser empacotado.
* **/ destino** define o diretório no qual o pacote deve ser criado. Este diretório tem de ser diferente do diretório de origem.
* **/appname** define o nome da aplicação da aplicação existente. É importante compreender que isto traduz-se para o nome do serviço no manifesto e não ao nome de aplicação do Service Fabric.
* **/exe** define o executável que o Service Fabric é suposto para iniciar, neste caso `node.exe`.
* **/Ma** define o argumento que está a ser utilizado para iniciar o executável. Como não está instalado o node. js, Service Fabric tem de iniciar o servidor de web de node. js executando `node.exe bin/www`.  `/ma:'bin/www'` informa a ferramenta de empacotamento para utilizar `bin/www` como o argumento node.exe.
* **/ AppType** define o nome de tipo de aplicação do Service Fabric.

Se navegar até o diretório que foi especificado no parâmetro /target, pode ver que a ferramenta criou um pacote de recursos de infraestrutura do serviço totalmente funcional conforme mostrado abaixo:

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
O servicemanifest. XML gerado agora tem uma secção que descreve a forma como o servidor de web de node. js deve ser inicializado, conforme mostrado no fragmento de código abaixo:

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
Neste exemplo, o servidor de web de node. js escuta a porta 3000, por isso terá de atualizar as informações de ponto final no ficheiro servicemanifest. XML, conforme mostrado abaixo.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Empacotar a aplicação do MongoDB
Agora que o ter compactado a aplicação node. js, pode ir em frente e pacotes MongoDB. Como mencionado anteriormente, os passos de que revê agora não são específicos de node. js e MongoDB. Na verdade, eles se aplicam a todas as aplicações que devem ser empacotados em conjunto como uma aplicação do Service Fabric.  

Para empacotar a MongoDB, quer certificar-se de que o pacote Mongod.exe e Mongo.exe. Ambos os binários são localizados no `bin` diretório do seu diretório de instalação do MongoDB. A estrutura do diretório é semelhante à abaixo.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Recursos de infraestrutura do serviço necessita para começar a MongoDB com um comando semelhante de abaixo, por isso terá de utilizar o `/ma` parâmetro quando o empacotamento do MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Os dados não é a ser mantidos no caso de uma falha de nó, se colocar o diretório de dados do MongoDB no diretório local do nó. Deve utilizar o armazenamento durável ou implementar uma réplica de MongoDB, definir a fim de evitar a perda de dados.  
>
>

No PowerShell ou a shell de comandos, podemos executar a ferramenta de empacotamento com os seguintes parâmetros:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Para adicionar o MongoDB ao seu pacote de aplicação do Service Fabric, terá de certificar-se de que os pontos de parâmetro /target no mesmo diretório que já contém a aplicação do manifesto, juntamente com a aplicação node. js. Terá também de certificar-se de que está a utilizar o mesmo nome de ApplicationType.

Vamos, navegue para o diretório e examinar o que criou a ferramenta.

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
Como pode ver, a ferramenta adicionada uma nova pasta, MongoDB, para o diretório que contém os binários do MongoDB. Se abrir o `ApplicationManifest.xml` arquivo, pode ver que o pacote contém agora a aplicação node. js e MongoDB. O código abaixo mostra o conteúdo do manifesto do aplicativo.

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

### <a name="publishing-the-application"></a>Publicação da aplicação
A última etapa é publicar a aplicação no cluster do Service Fabric local usando os scripts do PowerShell abaixo:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Assim que a aplicação é publicada com êxito para o cluster local, pode aceder a aplicação node. js na porta entraram no manifesto do serviço de aplicação node. js, por exemplo http://localhost:3000.

Neste tutorial, viu como empacotar facilmente dois aplicativos existentes como uma aplicação do Service Fabric. Também aprendeu como implementá-la para o Service Fabric, para que ele pode beneficiar de alguns dos recursos do Service Fabric, como elevada disponibilidade e a integração do sistema de estado de funcionamento.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Adicionar mais executáveis de convidado para uma aplicação existente com o Yeoman no Linux

Para adicionar outro serviço a uma aplicação já criada com o `yo`, execute os seguintes passos: 
1. Altere o diretório para a raiz da aplicação existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é a aplicação criada por Yeoman.
2. Executar `yo azuresfguest:AddService` e forneça os detalhes necessários.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre a implementação de contentores com [descrição geral do Service Fabric e contentores](service-fabric-containers-overview.md)
* [Exemplo para o empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemplo de dois convidado executáveis (c# e nodejs) comunicar através do serviço de nomenclatura com REST](https://github.com/Azure-Samples/service-fabric-containers)
