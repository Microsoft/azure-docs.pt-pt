---
title: Implantar um executável existente no Azure Service Fabric | Microsoft Docs
description: Saiba como empacotar um aplicativo existente como um executável convidado, para que ele possa ser implantado em um Cluster Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: atsenthi
ms.openlocfilehash: 575303cc2ec3e880187bac64da06d05721df14e6
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599661"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Empacotar e implantar um executável existente no Service Fabric
Ao empacotar um executável existente como um [executável convidado](service-fabric-guest-executables-introduction.md), você pode optar por usar um modelo de projeto do Visual Studio ou [criar o pacote de aplicativos manualmente](#manually). Usando o Visual Studio, a estrutura do pacote de aplicativos e os arquivos de manifesto são criados pelo novo modelo de projeto para você.

> [!TIP]
> A maneira mais fácil de empacotar um executável existente do Windows em um serviço é usar o Visual Studio e o Linux para usar o Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Usar o Visual Studio para empacotar e implantar um executável existente
O Visual Studio fornece um modelo de serviço Service Fabric para ajudá-lo a implantar um executável convidado em um Cluster Service Fabric.

1. Escolha **arquivo** > **novo projeto**e crie um Service Fabric aplicativo.
2. Escolha **executável convidado** como o modelo de serviço.
3. Clique em **procurar** para selecionar a pasta com seu executável e preencha o restante dos parâmetros para criar o serviço.
   * *Comportamento do pacote de códigos*. Pode ser definido para copiar todo o conteúdo da pasta para o projeto do Visual Studio, o que será útil se o executável não for alterado. Se você espera que o executável seja alterado e queira que a capacidade de obter novas compilações dinamicamente, você pode optar por vincular à pasta em vez disso. Você pode usar pastas vinculadas ao criar o projeto de aplicativo no Visual Studio. Isso é vinculado ao local de origem de dentro do projeto, possibilitando que você atualize o executável do convidado em seu destino de origem. Essas atualizações se tornam parte do pacote de aplicativos na compilação.
   * *Programa* especifica o executável que deve ser executado para iniciar o serviço.
   * *Argumentos* especifica os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
   * *WorkingFolder* especifica o diretório de trabalho para o processo que será iniciado. Você pode especificar três valores:
     * `CodeBase`Especifica que o diretório de trabalho será definido para o diretório de código no pacote de aplicativos (`Code` diretório mostrado na estrutura de arquivo anterior).
     * `CodePackage`Especifica que o diretório de trabalho será definido como a raiz do pacote de aplicativos (`GuestService1Pkg` mostrado na estrutura de arquivo anterior).
     * `Work`Especifica que os arquivos são colocados em um subdiretório chamado trabalho.
4. Dê um nome ao serviço e clique em **OK**.
5. Se o serviço precisar de um ponto de extremidade para comunicação, agora você poderá adicionar o protocolo, a porta e o tipo ao arquivo do serviço de manifesto. xml. Por exemplo: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Agora você pode usar o pacote e a ação de publicação em seu cluster local Depurando a solução no Visual Studio. Quando estiver pronto, você poderá publicar o aplicativo em um cluster remoto ou fazer check-in da solução para o controle do código-fonte.
7. Leia [verificar seu aplicativo em execução](#check-your-running-application) para ver como exibir seu serviço executável convidado em execução no Service Fabric Explorer.

Para obter um exemplo de instruções, consulte [criar seu primeiro aplicativo executável de convidado usando o Visual Studio](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Usar o Yeoman para empacotar e implantar um executável existente no Linux

O procedimento para criar e implantar um executável convidado no Linux é o mesmo que implantar um aplicativo Csharp ou Java.

1. Num terminal, escreva `yo azuresfguest`.
2. Dê um nome à aplicação.
3. Nomeie o serviço e forneça os detalhes, incluindo o caminho do executável e os parâmetros com os quais ele deve ser invocado.

O Yeoman cria um pacote de aplicativos com os arquivos de manifesto e de aplicativo apropriados, juntamente com os scripts de instalação e desinstalação.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Empacotar e implantar manualmente um executável existente
O processo de empacotamento manual de um executável convidado é baseado nas seguintes etapas gerais:

1. Crie a estrutura do diretório do pacote.
2. Adicione o código e os arquivos de configuração do aplicativo.
3. Edite o arquivo de manifesto do serviço.
4. Edite o arquivo de manifesto do aplicativo.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](https://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Criar a estrutura do diretório do pacote
Você pode começar criando a estrutura de diretório, conforme descrito em [empacotar um aplicativo de Service Fabric do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps).

### <a name="add-the-applications-code-and-configuration-files"></a>Adicionar o código e os arquivos de configuração do aplicativo
Depois de criar a estrutura de diretório, você pode adicionar o código do aplicativo e os arquivos de configuração nos diretórios de código e configuração. Você também pode criar diretórios ou subdiretórios adicionais nos diretórios de código ou configuração.

Service Fabric faz um `xcopy` do conteúdo do diretório raiz do aplicativo, portanto, não há nenhuma estrutura predefinida a ser usada além de criar dois diretórios principais, código e configurações. (Você pode escolher nomes diferentes, se desejar. Mais detalhes estão na próxima seção.)

> [!NOTE]
> Certifique-se de incluir todos os arquivos e dependências de que o aplicativo precisa. Service Fabric copia o conteúdo do pacote de aplicativos em todos os nós no cluster em que os serviços do aplicativo serão implantados. O pacote deve conter todo o código que o aplicativo precisa para ser executado. Não presuma que as dependências já estejam instaladas.
>
>

### <a name="edit-the-service-manifest-file"></a>Editar o arquivo de manifesto do serviço
A próxima etapa é editar o arquivo de manifesto do serviço para incluir as seguintes informações:

* O nome do tipo de serviço. Essa é uma ID que Service Fabric usa para identificar um serviço.
* O comando a ser usado para iniciar o aplicativo (ExeHost).
* Qualquer script que precise ser executado para configurar o aplicativo (SetupEntrypoint).

Veja a seguir um exemplo de um `ServiceManifest.xml` arquivo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

As seções a seguir passam pelas diferentes partes do arquivo que você precisa atualizar.

#### <a name="update-servicetypes"></a>Atualizar os tipos de atualizações
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Você pode escolher qualquer nome que desejar `ServiceTypeName`. O valor é usado no `ApplicationManifest.xml` arquivo para identificar o serviço.
* Especifique `UseImplicitHost="true"`. Esse atributo informa Service Fabric que o serviço é baseado em um aplicativo independente, de modo que todos os Service Fabric precisam fazê-lo para iniciá-lo como um processo e monitorar sua integridade.

#### <a name="update-codepackage"></a>Atualizar CodePackage
O elemento CodePackage especifica o local (e a versão) do código do serviço.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

O `Name` elemento é usado para especificar o nome do diretório no pacote de aplicativos que contém o código do serviço. `CodePackage`também tem o `version` atributo. Isso pode ser usado para especificar a versão do código e também pode ser usado para atualizar o código do serviço usando a infraestrutura de gerenciamento do ciclo de vida do aplicativo em Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Opcional: Atualizar SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
O elemento SetupEntryPoint é usado para especificar qualquer executável ou arquivo em lotes que deve ser executado antes do código do serviço ser iniciado. É uma etapa opcional, portanto, não precisa ser incluída se não houver nenhuma inicialização necessária. O SetupEntryPoint é executado toda vez que o serviço é reiniciado.

Há apenas um SetupEntryPoint, portanto, os scripts de instalação precisam ser agrupados em um único arquivo em lotes se a instalação do aplicativo exigir vários scripts. O SetupEntryPoint pode executar qualquer tipo de arquivo: arquivos executáveis, arquivos em lotes e cmdlets do PowerShell. Para obter mais detalhes, consulte [Configurar SetupEntryPoint](service-fabric-application-runas-security.md).

No exemplo anterior, o SetupEntryPoint executa um arquivo em lotes chamado `LaunchConfig.cmd` que está localizado `scripts` no subdiretório do diretório do código (supondo que o elemento WorkingFolder esteja definido como codebase).

#### <a name="update-entrypoint"></a>Atualizar ponto de entrada
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

O `EntryPoint` elemento no arquivo de manifesto do serviço é usado para especificar como iniciar o serviço.

O `ExeHost` elemento Especifica o executável (e os argumentos) que devem ser usados para iniciar o serviço. Opcionalmente, você pode adicionar `IsExternalExecutable="true"` o atributo `ExeHost` a para indicar que o programa é um executável externo fora do pacote de códigos. Por exemplo, `<ExeHost IsExternalExecutable="true">`.

* `Program`Especifica o nome do executável que deve iniciar o serviço.
* `Arguments`Especifica os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
* `WorkingFolder`Especifica o diretório de trabalho para o processo que será iniciado. Você pode especificar três valores:
  * `CodeBase`Especifica que o diretório de trabalho será definido para o diretório de código no pacote de aplicativos (`Code` diretório na estrutura de arquivos anterior).
  * `CodePackage`Especifica que o diretório de trabalho será definido como a raiz do pacote de aplicativos (`GuestService1Pkg` na estrutura de arquivo anterior).
    * `Work`Especifica que os arquivos são colocados em um subdiretório chamado trabalho.

O WorkingFolder é útil para definir o diretório de trabalho correto para que os caminhos relativos possam ser usados pelo aplicativo ou scripts de inicialização.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Atualizar pontos de extremidade e registrar com Serviço de Nomenclatura para comunicação
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
No exemplo anterior, o `Endpoint` elemento Especifica os pontos de extremidade que o aplicativo pode escutar. Neste exemplo, o aplicativo node. js escuta no http na porta 3000.

Além disso, você pode pedir Service Fabric para publicar esse ponto de extremidade no Serviço de Nomenclatura para que outros serviços possam descobrir o endereço do ponto de extremidade para esse serviço. Isso permite que você seja capaz de se comunicar entre os serviços que são executáveis convidados.
O endereço do ponto de extremidade publicado está `UriScheme://IPAddressOrFQDN:Port/PathSuffix`no formato. `UriScheme`e `PathSuffix` são atributos opcionais. `IPAddressOrFQDN`é o endereço IP ou o nome de domínio totalmente qualificado do nó no qual esse executável é colocado e é calculado para você.

No exemplo a seguir, depois que o serviço for implantado, em Service Fabric Explorer você verá um `http://10.1.4.92:3000/myapp/` ponto de extremidade semelhante ao publicado para a instância de serviço. Ou, se esse for um computador local, você `http://localhost:3000/myapp/`verá.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Você pode usar esses endereços com [proxy reverso](service-fabric-reverseproxy.md) para se comunicar entre os serviços.

### <a name="edit-the-application-manifest-file"></a>Editar o arquivo de manifesto do aplicativo
Depois de configurar o `Servicemanifest.xml` arquivo, você precisa fazer algumas alterações `ApplicationManifest.xml` no arquivo para garantir que o tipo de serviço e o nome corretos sejam usados.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
`ServiceManifestImport` No elemento, você pode especificar um ou mais serviços que deseja incluir no aplicativo. Os serviços são referenciados com `ServiceManifestName`, que especifica o nome do diretório onde o `ServiceManifest.xml` arquivo está localizado.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurar o registro em log
Para executáveis convidados, é útil ser capaz de ver os logs do console para descobrir se os scripts de configuração e de aplicativo mostram erros.
O redirecionamento de console pode ser configurado `ServiceManifest.xml` no arquivo usando `ConsoleRedirection` o elemento.

> [!WARNING]
> Nunca use a política de redirecionamento de console em um aplicativo implantado em produção porque isso pode afetar o failover do aplicativo. Use isso *apenas* para fins de depuração e desenvolvimento locais.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection`pode ser usado para redirecionar a saída do console (stdout e stderr) para um diretório de trabalho. Isso fornece a capacidade de verificar se não há erros durante a instalação ou execução do aplicativo no Cluster Service Fabric.

`FileRetentionCount`Determina quantos arquivos são salvos no diretório de trabalho. Um valor de 5, por exemplo, significa que os arquivos de log das cinco execuções anteriores são armazenados no diretório de trabalho.

`FileMaxSizeInKb`Especifica o tamanho máximo dos arquivos de log.

Os arquivos de log são salvos em um dos diretórios de trabalho do serviço. Para determinar onde os arquivos estão localizados, use Service Fabric Explorer para determinar em qual nó o serviço está sendo executado e qual diretório de trabalho está sendo usado. Esse processo é abordado posteriormente neste artigo.

## <a name="deployment"></a>Implementação
A última etapa é [implantar seu aplicativo](service-fabric-deploy-remove-applications.md). O script do PowerShell a seguir mostra como implantar seu aplicativo no cluster de desenvolvimento local e iniciar um novo serviço de Service Fabric.

```powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Compacte o pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o repositório de imagens se o pacote for grande ou tiver muitos arquivos. Leia mais [aqui](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Um serviço de Service Fabric pode ser implantado em várias "configurações". Por exemplo, ele pode ser implantado como uma ou várias instâncias ou pode ser implantado de forma que haja uma instância do serviço em cada nó do cluster de Service Fabric.

O `InstanceCount` parâmetro`New-ServiceFabricService` do cmdlet é usado para especificar quantas instâncias do serviço devem ser iniciadas no Cluster Service Fabric. Você pode definir o `InstanceCount` valor, dependendo do tipo de aplicativo que você está implantando. Os dois cenários mais comuns são:

* `InstanceCount = "1"`. Nesse caso, apenas uma instância do serviço é implantada no cluster. O Agendador de Service Fabric determina em qual nó o serviço será implantado.
* `InstanceCount ="-1"`. Nesse caso, uma instância do serviço é implantada em cada nó no Cluster Service Fabric. O resultado é ter uma (e apenas uma) instância do serviço para cada nó no cluster.

Essa é uma configuração útil para aplicativos de front-end (por exemplo, um ponto de extremidade REST), porque os aplicativos cliente precisam "se conectar" a qualquer um dos nós no cluster para usar o ponto de extremidade. Essa configuração também pode ser usada quando, por exemplo, todos os nós do cluster de Service Fabric estão conectados a um balanceador de carga. O tráfego do cliente pode então ser distribuído entre o serviço em execução em todos os nós no cluster.

## <a name="check-your-running-application"></a>Verificar o aplicativo em execução
Em Service Fabric Explorer, identifique o nó em que o serviço está em execução. Neste exemplo, ele é executado em Node1:

![Nó em que o serviço está em execução](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Se você navegar até o nó e navegar até o aplicativo, verá as informações essenciais do nó, incluindo seu local no disco.

![Local no disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Se você navegar até o diretório usando Gerenciador de Servidores, poderá encontrar o diretório de trabalho e a pasta de log do serviço, conforme mostrado na seguinte captura de tela: 

![Local do log](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu a empacotar um executável convidado e implantá-lo no Service Fabric. Consulte os artigos a seguir para obter informações e tarefas relacionadas.

* [Exemplo de empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), incluindo um link para o pré-lançamento da ferramenta de empacotamento
* [Exemplo de dois executáveis convidados (C# e NodeJS) se comunicando por meio do serviço de cadastramento usando REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Implementar vários executáveis convidados](service-fabric-deploy-multiple-apps.md)
* [Criar seu primeiro aplicativo Service Fabric usando o Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
