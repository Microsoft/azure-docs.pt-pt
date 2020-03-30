---
title: Implemente uma aplicação Node.js que utilize o MongoDB
description: Walkthrough sobre como embalar vários executáveis de hóspedes para implantar para um cluster Azure Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4538efc8a2426fc20dd20d1a85edaf6f76bfc649
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614473"
---
# <a name="deploy-multiple-guest-executables"></a>Implementar vários executáveis convidados
Este artigo mostra como embalar e implementar vários executáveis de hóspedes para o Tecido de Serviço Azure. Para construir e implantar um único pacote de Tecido de Serviço, leia como [implantar um hóspede executável para o Tecido de Serviço](service-fabric-deploy-existing-app.md).

Enquanto este walkthrough mostra como implementar uma aplicação com uma extremidade frontal Node.js que usa o MongoDB como loja de dados, você pode aplicar os passos a qualquer aplicação que tenha dependências de outra aplicação.   

Pode utilizar o Visual Studio para produzir o pacote de aplicações que contém vários executáveis de hóspedes. Consulte [o Uso do Estúdio Visual para embalar uma aplicação existente](service-fabric-deploy-existing-app.md). Depois de ter adicionado o primeiro hóspede executável, clique no projeto de aplicação e selecione o **serviço Add->New Service Fabric** para adicionar o segundo projeto executável ao hóspede executável à solução. Nota: Se optar por ligar a fonte no projeto Do Estúdio Visual, construindo a solução Visual Studio, certificar-se-á de que o seu pacote de aplicações está atualizado com alterações na fonte. 

## <a name="samples"></a>Amostras
* [Amostra para embalagem e implantação de um hóspede executável](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Amostra de dois executáveis convidados (C# e nodejs) comunicando através do serviço naming usando REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Embalem manualmente a aplicação executável de vários hóspedes
Em alternativa, pode embalar manualmente o hóspede executável. Para mais detalhes, consulte [manualmente o pacote e implemente um executável existente](service-fabric-deploy-existing-app.md#manually-package-and-deploy-an-existing-executable).

### <a name="packaging-the-nodejs-application"></a>Embalar a aplicação Nó.js
Este artigo pressupõe que o Node.js não está instalado nos nós do cluster Service Fabric. Como consequência, você precisa adicionar Node.exe ao diretório raiz da sua aplicação de nó antes de embalar. A estrutura de diretório da aplicação Node.js (utilizando a estrutura web express e o motor de modelo de Jade) deve ser semelhante à abaixo:

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

Como próximo passo, cria-se um pacote de aplicação para a aplicação Node.js. O código abaixo cria um pacote de aplicação Service Fabric que contém a aplicação Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Abaixo está uma descrição dos parâmetros que estão sendo usados:

* **/fonte** aponta para o diretório da aplicação que deve ser embalada.
* **/alvo** define o diretório em que o pacote deve ser criado. Este diretório tem de ser diferente do diretório de origem.
* **/nome de aplicação** define o nome da aplicação da aplicação existente. É importante entender que isto se traduz para o nome de serviço no manifesto, e não para o nome da aplicação Service Fabric.
* **/exe** define o executível que o Tecido de `node.exe`Serviço deve lançar, neste caso .
* **/ma** define o argumento que está sendo usado para lançar o executável. Como o Node.js não está instalado, o Service Fabric precisa de `node.exe bin/www`lançar o servidor web Node.js executando .  `/ma:'bin/www'`indica a ferramenta `bin/www` de embalagem para usar como argumento para nó.exe.
* **/AppType** define o nome do tipo de aplicação Service Fabric.

Se você navegar para o diretório especificado no parâmetro /alvo, você pode ver que a ferramenta criou um pacote de Tecido de Serviço totalmente funcional, como mostrado abaixo:

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
O ServiceManifest.xml gerado tem agora uma secção que descreve como o servidor web Node.js deve ser lançado, como mostrado no código abaixo:

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
Nesta amostra, o servidor web Node.js ouve a porta 3000, pelo que necessita de atualizar as informações de ponto final no ficheiro ServiceManifest.xml, conforme mostrado abaixo.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Embalar a aplicação MongoDB
Agora que embalou a aplicação Node.js, pode ir em frente e embalar o MongoDB. Como mencionado anteriormente, os passos que passa agora não são específicos do Node.js e do MongoDB. Na verdade, aplicam-se a todas as aplicações que devem ser embaladas em conjunto como uma aplicação de Tecido de Serviço.  

Para embalar mongoDB, você deve ter certeza de embalar Mongod.exe e Mongo.exe. Ambos os binários `bin` estão localizados no diretório do seu diretório de instalação MongoDB. A estrutura do diretório é semelhante à que se segue.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
O Tecido de Serviço precisa de iniciar o MongoDB com um `/ma` comando semelhante ao abaixo, pelo que tem de utilizar o parâmetro ao embalar o MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Os dados não estão a ser preservados em caso de falha no nó se colocar o diretório de dados mongoDB no diretório local do nó. Deve utilizar armazenamento duradouro ou implementar uma réplica MongoDB definida de forma a evitar a perda de dados.  
>
>

Na PowerShell ou na concha de comando, executamos a ferramenta de embalagem com os seguintes parâmetros:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Para adicionar o MongoDB ao seu pacote de aplicações Service Fabric, é necessário certificar-se de que o parâmetro/alvo aponta para o mesmo diretório que já contém o manifesto de aplicação juntamente com a aplicação Node.js. Também precisa de se certificar de que está a usar o mesmo nome ApplicationType.

Vamos procurar no diretório e examinar o que a ferramenta criou.

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
Como pode ver, a ferramenta adicionou uma nova pasta, MongoDB, ao diretório que contém os binários MongoDB. Se abrir `ApplicationManifest.xml` o ficheiro, pode ver que o pacote contém agora tanto a aplicação Node.js como a MongoDB. O código abaixo mostra o conteúdo do manifesto de aplicação.

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
O último passo é publicar a aplicação para o cluster de Tecido de Serviço local utilizando os scripts PowerShell abaixo:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Uma vez publicada a aplicação com sucesso para o cluster local, pode aceder à aplicação Nó.js no porto que introduzimos\/no manifesto de serviço da aplicação Nó.js -- por exemplo http: /localhost:3000.

Neste tutorial, já viu como embalar facilmente duas aplicações existentes como uma aplicação de Tecido de Serviço. Também aprendeu a implantá-lo no Service Fabric para que possa beneficiar de algumas das funcionalidades do Tecido de Serviço, tais como alta disponibilidade e integração do sistema de saúde.

## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Adicionar mais executáveis de hóspedes a uma aplicação existente usando Yeoman em Linux

Para adicionar outro serviço a uma aplicação já criada com o `yo`, execute os seguintes passos: 
1. Altere o diretório para a raiz da aplicação existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é a aplicação criada por Yeoman.
2. Corra `yo azuresfguest:AddService` e forneça os detalhes necessários.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a implantação de contentores com tecido de [serviço e vistas panorâmicas](service-fabric-containers-overview.md) de contentores
* [Amostra para embalagem e implantação de um hóspede executável](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Amostra de dois executáveis convidados (C# e nodejs) comunicando através do serviço naming usando REST](https://github.com/Azure-Samples/service-fabric-containers)
