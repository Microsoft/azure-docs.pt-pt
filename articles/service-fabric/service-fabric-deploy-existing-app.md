---
title: Implementar um executável existente no Azure Service Fabric | Documentos da Microsoft
description: Saiba como empacotar uma aplicação existente como convidado executável, pelo que pode ser implementado num cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: aljo
ms.openlocfilehash: 2035ee27aecdf2ec1ab8bf1166f667b5595a7cbb
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439490"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Agrupar e implementar um executável existente para o Service Fabric
Quando empacotar um executável existente como um [executável convidado](service-fabric-guest-executables-introduction.md), pode escolher entre utilizar um modelo de projeto do Visual Studio ou a [criar manualmente o pacote de aplicação](#manually). Com o Visual Studio, a estrutura de pacotes de aplicações e ficheiros de manifesto são criados pelo novo modelo de projeto para.

> [!TIP]
> A maneira mais fácil para empacotar um executável num serviço de Windows existente é usar o Visual Studio e no Linux para utilizar o Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Utilizar o Visual Studio para empacotar e implementar um executável existente
O Visual Studio fornece um modelo de serviço do Service Fabric para o ajudar a implementar um executável de convidado num cluster do Service Fabric.

1. Escolher **arquivo** > **novo projeto**e crie uma aplicação do Service Fabric.
2. Escolher **executável convidado** como o modelo de serviço.
3. Clique em **procurar** para selecionar a pasta com o seu executável e preencha o resto dos parâmetros para criar o serviço.
   * *Comportamento do pacote de código*. Pode ser definido para copiar todo o conteúdo da sua pasta para o projeto do Visual Studio, que é útil se o executável não é alterada. Se pretender que o executável para alterar e quiser ter a capacidade de retirada novas compilações dinamicamente, pode optar por ligar para a pasta em vez disso. Pode utilizar pastas ligadas ao criar o projeto de aplicativo no Visual Studio. Esta contém links para a localização de origem a partir de dentro do projeto, tornando possível para que atualizar o executável de convidado na sua origem de destino. Essas atualizações tornam-se parte do pacote do aplicativo na compilação.
   * *Programa* Especifica o executável que deve ser executado para iniciar o serviço.
   * *Argumentos* Especifica os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
   * *WorkingFolder* Especifica o diretório de trabalho para o processo que vai ser iniciado. Pode especificar três valores:
     * `CodeBase` Especifica que o diretório de trabalho vai ser definido para o diretório de código no pacote de aplicação (`Code` directory mostrada a estrutura de arquivo anterior).
     * `CodePackage` Especifica que o diretório de trabalho vai ser definido para a raiz do pacote de aplicação (`GuestService1Pkg` mostra a estrutura de arquivo anterior).
     * `Work` Especifica que os ficheiros são colocados num subdiretório chamado de trabalho.
4. Dê um nome ao serviço e clique em **OK**.
5. Se o seu serviço precisa de um ponto final para comunicação, agora pode adicionar o protocolo, porta e tipo no ficheiro servicemanifest. XML. Por exemplo: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Agora pode utilizar o pacote e publique ação contra o seu cluster local ao depurar a solução no Visual Studio. Quando estiver pronto, pode publicar a aplicação num cluster remoto ou check-in a solução ao controle de origem.
7. Leia [verificar a sua aplicação em execução](#check-your-running-application) para saber como pode ver o seu serviço de executáveis de convidado em execução no Service Fabric Explorer.

Para uma exemplo passo a passo, consulte [criar a sua primeira aplicação executável de convidado com o Visual Studio](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Utilizar o Yeoman para o pacote e implementar um executável existente no Linux

O procedimento para criar e implementar um convidado executável no Linux é igual de uma aplicação csharp ou java.

1. Num terminal, escreva `yo azuresfguest`.
2. Dê um nome à aplicação.
3. Ao serviço o nome e forneça os detalhes, incluindo o caminho do executável e os parâmetros, que tem de ser invocado com.

Yeoman cria um pacote de aplicação com o aplicativo apropriado e arquivos de manifesto juntamente com instalar e desinstalar scripts.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Manualmente, empacotar e implementar um executável existente
O processo de empacotamento manualmente um executável convidado baseia-se os seguintes passos gerais:

1. Crie a estrutura de diretório do pacote.
2. Adicione ficheiros de código e a configuração do aplicativo.
3. Edite o ficheiro de manifesto do serviço.
4. Edite o ficheiro de manifesto do aplicativo.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](https://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Criar a estrutura de diretório do pacote
Pode começar ao criar a estrutura de diretório, conforme descrito em [empacotar uma aplicação do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps).

### <a name="add-the-applications-code-and-configuration-files"></a>Adicionar ficheiros de código e a configuração da aplicação
Depois de criar a estrutura de diretórios, pode adicionar o código do aplicativo e ficheiros de configuração sob os diretórios de configuração e de código. Também pode criar diretórios adicionais ou os subdiretórios abaixo os diretórios de configuração ou de código.

Service Fabric faz um `xcopy` do conteúdo de diretório de raiz da aplicação, portanto, não há nenhuma estrutura predefinida para utilizar senão criando dois diretórios principais, código e as definições. (Pode escolher nomes diferentes se desejar. Estão mais detalhes na próxima seção.)

> [!NOTE]
> Certifique-se de que inclui todos os arquivos e as dependências que a aplicação precisa. Service Fabric copia o conteúdo do pacote de aplicação em todos os nós do cluster em que serviços do aplicativo vai ser implementado. O pacote deve conter todo o código que o aplicativo precisa ser executado. Não partem do princípio de que as dependências já estão instaladas.
>
>

### <a name="edit-the-service-manifest-file"></a>Editar o ficheiro de manifesto do serviço
A próxima etapa é editar o ficheiro de manifesto de serviço para incluir as seguintes informações:

* O nome do tipo de serviço. Este é um ID de que o Service Fabric utiliza para identificar um serviço.
* O comando para utilizar para iniciar o aplicativo (ExeHost).
* Qualquer script que precisa ser executado para configurar a aplicação (SetupEntrypoint).

Segue-se um exemplo de um `ServiceManifest.xml` ficheiro:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

As secções seguintes abordar as diferentes partes do ficheiro que tem de atualizar.

#### <a name="update-servicetypes"></a>Atualizar ServiceTypes
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Pode escolher qualquer nome que pretende para `ServiceTypeName`. O valor é utilizado no `ApplicationManifest.xml` ficheiro para identificar o serviço.
* Especificar `UseImplicitHost="true"`. Este atributo indica ao Service Fabric que o serviço se baseia numa aplicação autónoma e contém, pelo que todas as necessidades de Service Fabric para o fazer é para iniciá-lo como um processo e monitorizar o respetivo estado de funcionamento.

#### <a name="update-codepackage"></a>Atualizar CodePackage
O elemento de CodePackage Especifica a localização (e versão) de código do serviço.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

O `Name` elemento é usado para especificar o nome do diretório no pacote de aplicação que contém o código do serviço. `CodePackage` Também tem o `version` atributo. Isso pode ser utilizado para especificar a versão do código e pode também ser usado para atualizar o código do serviço utilizando a infraestrutura de gestão do ciclo de vida de aplicações no Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Opcional: Atualizar SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
O elemento de SetupEntryPoint é utilizado para especificar qualquer ficheiro executável ou lote que deve ser executado antes do código do serviço é iniciado. É um passo opcional, para que ele não tem de ser incluída se não houver nenhuma necessária de inicialização. O SetupEntryPoint é executado sempre que o serviço for reiniciado.

Há apenas um SetupEntryPoint, para que os scripts de configuração tem de ser agrupados num arquivo em lote única, se a configuração do aplicativo requer vários scripts. O SetupEntryPoint pode executar qualquer tipo de ficheiro: ficheiros executáveis, ficheiros batch e cmdlets do PowerShell. Para obter mais detalhes, consulte [SetupEntryPoint configurar](service-fabric-application-runas-security.md).

No exemplo anterior, o SetupEntryPoint executa um arquivo em lotes chamado `LaunchConfig.cmd` que se encontra no `scripts` subdiretório do diretório de código (supondo que o elemento de WorkingFolder está definido para a base de código).

#### <a name="update-entrypoint"></a>Atualizar o ponto de entrada
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

O `EntryPoint` elemento no ficheiro de manifesto do serviço é utilizado para especificar como iniciar o serviço.

O `ExeHost` elemento Especifica o ficheiro executável (e argumentos) que deve ser usado para iniciar o serviço. Opcionalmente, pode adicionar os `IsExternalExecutable="true"` atributo para `ExeHost` para indicar que o programa é um executável externo fora do pacote de código. Por exemplo, `<ExeHost IsExternalExecutable="true">`.

* `Program` Especifica o nome do ficheiro executável que deve iniciar o serviço.
* `Arguments` Especifica os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
* `WorkingFolder` Especifica o diretório de trabalho para o processo que vai ser iniciado. Pode especificar três valores:
  * `CodeBase` Especifica que o diretório de trabalho vai ser definido para o diretório de código no pacote de aplicação (`Code` diretório na estrutura do arquivo anterior).
  * `CodePackage` Especifica que o diretório de trabalho vai ser definido para a raiz do pacote de aplicação (`GuestService1Pkg` na estrutura do arquivo anterior).
    * `Work` Especifica que os ficheiros são colocados num subdiretório chamado de trabalho.

O WorkingFolder é útil para definir o diretório de trabalho correto, de forma a que os caminhos relativos podem ser usados pelos scripts da aplicação ou a inicialização.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Atualizar pontos de extremidade e registar com o serviço de nomenclatura para comunicação
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
No exemplo anterior, o `Endpoint` elemento Especifica os pontos de extremidade que o aplicativo pode escutar. Neste exemplo, a aplicação node. js escuta de http na porta 3000.

Além disso pode pedir ao Service Fabric para publicar este ponto final do serviço de nomenclatura para que outros serviços podem detetar o endereço de ponto final para este serviço. Isto permite-lhe ser capaz de comunicar entre os serviços que são executáveis convidados.
O endereço de ponto de extremidade publicada é o formato `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` e `PathSuffix` são atributos opcionais. `IPAddressOrFQDN` é o endereço IP ou nome de domínio completamente qualificado do nó esse executável é colocado no, e esta é calculada para.

No exemplo a seguir, uma vez que o serviço é implementado, no Service Fabric Explorer vir um ponto final semelhante a `http://10.1.4.92:3000/myapp/` publicados para a instância de serviço. Ou se se tratar de um computador local, consulte `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Pode utilizar estes endereços com [proxy inverso](service-fabric-reverseproxy.md) para a comunicação entre serviços.

### <a name="edit-the-application-manifest-file"></a>Edite o ficheiro de manifesto de aplicação
Depois de ter configurado o `Servicemanifest.xml` arquivo, precisa fazer algumas alterações para o `ApplicationManifest.xml` ficheiro para garantir que o tipo de serviço correto e o nome são utilizadas.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
Na `ServiceManifestImport` elemento, pode especificar um ou mais serviços que pretende incluir na aplicação. Os serviços são referenciados com `ServiceManifestName`, que especifica o nome do diretório onde o `ServiceManifest.xml` está localizado o ficheiro.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurar o registo
Para executáveis de convidado, é útil conseguir ver os registos da consola para saber se os scripts de configuração de aplicação e mostram os eventuais erros.
Redirecionamento de Console pode ser configurado no `ServiceManifest.xml` de ficheiros utilizando o `ConsoleRedirection` elemento.

> [!WARNING]
> Nunca use a política de redirecionamento de consola num aplicativo que é implementado em produção, porque isso pode afetar o failover de aplicativos. *Apenas* usá-lo para o desenvolvimento local e fins de depuração.  
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

`ConsoleRedirection` pode ser utilizado para redirecionar a saída da consola (stdout e stderr) para um diretório de trabalho. Isso oferece a capacidade para verificar que não há nenhum erro durante a instalação ou execução do aplicativo no cluster do Service Fabric.

`FileRetentionCount` determina o número de ficheiros é guardado no diretório de trabalho. Um valor de 5, por exemplo, significa que os ficheiros de registo para as execuções de cinco anteriores são armazenados no diretório de trabalho.

`FileMaxSizeInKb` Especifica o tamanho máximo dos ficheiros de registo.

Ficheiros de registo são salvas em um dos diretórios de trabalho do serviço. Para determinar onde estão localizados os ficheiros, utilizar o Service Fabric Explorer para determinar qual nó a que o serviço está em execução no e está a ser utilizado o diretório de trabalho. Este processo é descrito neste artigo.

## <a name="deployment"></a>Implementação
A última etapa é [implementar a sua aplicação](service-fabric-deploy-remove-applications.md). O script do PowerShell seguinte mostra como implementar a sua aplicação para o cluster de desenvolvimento local e inicie um novo serviço do Service Fabric.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Comprimir pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o arquivo de imagem, se o pacote é grande ou se tiver muitos arquivos. Leia mais [aqui](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Um serviço do Service Fabric pode ser implementado em diversas "configurações". Por exemplo, pode ser implementado como instâncias únicas ou múltiplas, ou pode ser implementado de forma que existe uma instância do serviço em cada nó do cluster do Service Fabric.

O `InstanceCount` parâmetro do `New-ServiceFabricService` cmdlet é utilizado para especificar o número de instâncias do serviço deve ser iniciado no cluster do Service Fabric. Pode definir o `InstanceCount` valor, dependendo do tipo de aplicação que está a implementar. Os dois cenários mais comuns são:

* `InstanceCount = "1"`. Neste caso, apenas uma instância do serviço é implementada no cluster. O agendador do Service Fabric determina o nó que o serviço vai ser implementado em.
* `InstanceCount ="-1"`. Neste caso, uma instância do serviço é implementada em cada nó no cluster do Service Fabric. O resultado é ter uma instância de um (e apenas um) do serviço para cada nó no cluster.

Esta é uma configuração útil para aplicativos de front-end (por exemplo, um ponto final REST), uma vez que as aplicações cliente ter "ligar" para qualquer um de nós do cluster para utilizar o ponto final. Esta configuração também pode ser utilizada quando, por exemplo, todos os nós do cluster do Service Fabric estão ligados a um balanceador de carga. O tráfego de cliente, em seguida, pode ser distribuído em todo o serviço que está a ser executado em todos os nós do cluster.

## <a name="check-your-running-application"></a>Verifique a sua aplicação em execução
No Service Fabric Explorer, identifique o nó em que o serviço está em execução. Neste exemplo, ele é executado em Node1:

![Nó onde o serviço está a ser executada](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Se navegar para o nó e navegue para a aplicação, verá as informações de nó essenciais, incluindo a localização no disco.

![Localização no disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Se navegar para o diretório com o Explorador de servidores, pode encontrar o diretório de trabalho e a pasta de registo do serviço, conforme mostrado na captura de ecrã seguinte: 

![Localização do registo](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu como um executável de convidado do pacote e implementá-la para o Service Fabric. Veja os artigos seguintes para informações relacionadas e tarefas.

* [Exemplo para o empacotamento e implantação de um executável convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), incluindo uma ligação para a versão de pré-lançamento da ferramenta de empacotamento
* [Exemplo de dois convidado executáveis (c# e nodejs) comunicar através do serviço de nomenclatura com REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Implementar vários executáveis convidados](service-fabric-deploy-multiple-apps.md)
* [Criar a sua primeira aplicação do Service Fabric com o Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
