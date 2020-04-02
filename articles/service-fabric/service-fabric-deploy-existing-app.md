---
title: Implementar um tecido de serviço existente executável para o Tecido de Serviço Azure
description: Aprenda a embalar uma aplicação existente como um hóspede executável, para que possa ser implantado para um cluster de Tecido de Serviço.
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: c6c6bc0369593c177b74261da1fd8c15dd73fcb3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520492"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Pacote e implementação de um executável existente para o Tecido de Serviço

Ao embalar um executável existente como [hóspede executável,](service-fabric-guest-executables-introduction.md)pode optar por utilizar um modelo de projeto do Estúdio Visual ou [criar manualmente o pacote](#manually)de aplicações . Utilizando o Visual Studio, a estrutura do pacote de aplicações e os ficheiros manifestos são criados pelo novo modelo de projeto para si.

> [!TIP]
> A maneira mais fácil de embalar um Windows existente executável em um serviço é usar o Visual Studio e em Linux para usar Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Use o Estúdio Visual para embalar e implementar um executável existente

O Visual Studio fornece um modelo de serviço service Fabric para ajudá-lo a implementar um hóspede executável para um cluster de Tecido de Serviço.

1. Escolha **o Arquivo** > **Novo Projeto**e crie uma aplicação de Tecido de Serviço.
2. Escolha o **Hóspede Executável** como modelo de serviço.
3. Clique em **Navegar** para selecionar a pasta com o seu executável e preencher o resto dos parâmetros para criar o serviço.
   * *Comportamento do pacote de código.* Pode ser configurado para copiar todo o conteúdo da sua pasta para o Visual Studio Project, o que é útil se o executável não mudar. Se espera que o executável mude e pretenda que a capacidade de captar novas construções de forma dinâmica, pode optar por ligar-se à pasta. Pode utilizar pastas ligadas ao criar o projeto de aplicação no Visual Studio. Isto liga-se à localização de origem a partir do projeto, possibilitando-lhe atualizar o hóspede executável no seu destino de origem. Estas atualizações tornam-se parte do pacote de aplicações em construção.
   * *O programa* especifica a executável que deve ser executada para iniciar o serviço.
   * *Os argumentos* especificam os argumentos que devem ser transmitidos ao executável. Pode ser uma lista de parâmetros com argumentos.
   * *WorkingFolder* especifica o diretório de trabalho para o processo que vai ser iniciado. Pode especificar três valores:
     * `CodeBase`especifica que o diretório de trabalho será definido para o diretório de código no pacote de aplicações (diretório`Code` mostrado na estrutura de ficheiros anterior).
     * `CodePackage`especifica que o diretório de trabalho será definido na raiz`GuestService1Pkg` do pacote de aplicações (mostrado na estrutura de ficheiros anterior).
     * `Work`especifica que os ficheiros são colocados num subdiretório chamado trabalho.
4. Dê um nome ao serviço e clique em **OK**.
5. Se o seu serviço necessitar de um ponto final de comunicação, pode agora adicionar o protocolo, porta e tipo ao ficheiro ServiceManifest.xml. Por exemplo: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Agora pode usar o pacote e publicar ações contra o seu cluster local, depurando a solução no Estúdio Visual. Quando estiver pronto, pode publicar a aplicação num cluster remoto ou verificar a solução para o controlo de origem.
7. Leia [a sua aplicação](#check-your-running-application) de execução para ver como ver o seu serviço executável de hóspedes a funcionar no Service Fabric Explorer.

Por exemplo, consulte [Criar a sua primeira aplicação executável de hóspedes usando o Visual Studio](quickstart-guest-app.md).

### <a name="packaging-multiple-executables-with-visual-studio"></a>Embalagem múltiplas executáveis com Estúdio Visual

Você pode usar o Visual Studio para produzir um pacote de aplicação que contém vários executáveis de hóspedes. Depois de adicionar o primeiro hóspede executável, clique no projeto de aplicação e selecione o **serviço Add->New Service Fabric** para adicionar o segundo projeto executável ao hóspede executável à solução.

> [!NOTE]
> Se optar por ligar a fonte no projeto Do Estúdio Visual, construindo a solução Visual Studio, certificar-se-á de que o seu pacote de aplicações está atualizado com alterações na fonte.

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Use Yeoman para embalar e implementar um executável existente no Linux

O procedimento para criar e implantar um hóspede executável no Linux é o mesmo que implementar uma aplicação csharp ou java.

1. Num terminal, escreva `yo azuresfguest`.
2. Dê um nome à aplicação.
3. Nomeie o seu serviço e forneça os detalhes, incluindo o caminho do executável e os parâmetros com os quais deve ser invocado.

Yeoman cria um pacote de aplicação com a aplicação apropriada e ficheiros manifestos juntamente com scripts de instalação e desinstalação.

### <a name="packaging-multiple-executables-using-yeoman-on-linux"></a>Embalagem múltiplas executáveis usando Yeoman em Linux

Para adicionar outro serviço a uma aplicação já criada com o `yo`, execute os seguintes passos:

1. Altere o diretório para a raiz da aplicação existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é a aplicação criada por Yeoman.
2. Corra `yo azuresfguest:AddService` e forneça os detalhes necessários.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Embalar manualmente e implementar um executável existente

O processo de embalagem manual de um hóspede executável baseia-se nos seguintes passos gerais:

1. Crie a estrutura de diretório de pacotes.
2. Adicione o código e os ficheiros de configuração da aplicação.
3. Editar o ficheiro manifesto de serviço.
4. Editar o ficheiro manifesto da aplicação.

### <a name="create-the-package-directory-structure"></a>Criar a estrutura de diretório de pacotes

Pode começar por criar a estrutura do diretório, como descrito no Pacote uma App de [Tecido de Serviço Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps).

### <a name="add-the-applications-code-and-configuration-files"></a>Adicione o código e os ficheiros de configuração da aplicação

Depois de ter criado a estrutura do diretório, pode adicionar o código e os ficheiros de configuração da aplicação sob os diretórios de código e config. Também pode criar diretórios ou subdiretórios adicionais sob o código ou diretórios de config.

O Service `xcopy` Fabric faz um dos conteúdos do diretório raiz da aplicação, pelo que não existe uma estrutura predefinida para utilizar além de criar dois diretórios, código e configurações de topo. (Pode escolher nomes diferentes se quiser. Mais detalhes estão na secção seguinte.)

> [!NOTE]
> Certifique-se de que inclui todos os ficheiros e dependências de que a aplicação necessita. Service Fabric copia o conteúdo do pacote de aplicação em todos os nós do cluster onde os serviços da aplicação vão ser implantados. O pacote deve conter todo o código que a aplicação precisa de executar. Não assuma que as dependências já estão instaladas.
>
>

### <a name="edit-the-service-manifest-file"></a>Editar o ficheiro manifesto de serviço

O próximo passo é editar o ficheiro manifesto de serviço para incluir as seguintes informações:

* O nome do tipo de serviço. Este é um ID que o Service Fabric usa para identificar um serviço.
* O comando a utilizar para lançar a aplicação (ExeHost).
* Qualquer script que precise de ser executado para configurar a aplicação (SetupEntrypoint).

Segue-se um exemplo `ServiceManifest.xml` de um ficheiro:

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

As seguintes secções passam pelas diferentes partes do ficheiro que precisa de atualizar.

#### <a name="update-servicetypes"></a>Atualizar Tipos de Serviços

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Pode escolher o nome que `ServiceTypeName`quiser. O valor é `ApplicationManifest.xml` usado no ficheiro para identificar o serviço.
* Especifique `UseImplicitHost="true"`. Este atributo diz ao Service Fabric que o serviço é baseado numa aplicação independente, por isso tudo o que o Service Fabric precisa fazer é lançá-lo como um processo e monitorizar a sua saúde.

#### <a name="update-codepackage"></a>Atualizar códigopacote
O elemento CodePackage especifica a localização (e versão) do código do serviço.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

O `Name` elemento é utilizado para especificar o nome do diretório no pacote de aplicações que contém o código do serviço. `CodePackage`também tem `version` o atributo. Isto pode ser usado para especificar a versão do código, e também pode ser potencialmente usado para atualizar o código do serviço utilizando a infraestrutura de gestão do ciclo de vida da aplicação em Tecido de Serviço.

#### <a name="optional-update-setupentrypoint"></a>Opcional: Ponto de configuração de atualização

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
O elemento ConfiguraçãoEntryPoint é utilizado para especificar qualquer ficheiro executável ou de lote que deve ser executado antes do código do serviço ser lançado. É um passo opcional, pelo que não precisa de ser incluído se não for necessária uma inicialização. O SetupEntryPoint é executado sempre que o serviço é reiniciado.

Existe apenas um SetupEntryPoint, pelo que os scripts de configuração precisam de ser agrupados num único ficheiro de lote se a configuração da aplicação necessitar de vários scripts. O SetupEntryPoint pode executar qualquer tipo de ficheiro: ficheiros executáveis, ficheiros de lote e cmdlets PowerShell. Para mais detalhes, consulte [Configurar Configurar](service-fabric-application-runas-security.md)O Ponto de Entrada .

No exemplo anterior, o SetupEntryPoint executa `LaunchConfig.cmd` um ficheiro `scripts` de lote chamado que está localizado no subdiretório do diretório de código (assumindo que o elemento WorkingFolder está definido para CodeBase).

#### <a name="update-entrypoint"></a>Ponto de entrada atualizado

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

O `EntryPoint` elemento no ficheiro manifesto de serviço é utilizado para especificar como lançar o serviço.

O `ExeHost` elemento especifica o executável (e os argumentos) que devem ser utilizados para lançar o serviço. Pode opcionalmente adicionar `IsExternalExecutable="true"` o `ExeHost` atributo para indicar que o programa é um executável externo fora do pacote de código. Por exemplo, `<ExeHost IsExternalExecutable="true">`.

* `Program`especifica o nome do executável que deve iniciar o serviço.
* `Arguments`especifica os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
* `WorkingFolder`especifica o diretório de trabalho para o processo que vai ser iniciado. Pode especificar três valores:
  * `CodeBase`especifica que o diretório de trabalho será definido para o diretório de código no pacote de aplicações (diretório`Code` na estrutura de ficheiros anterior).
  * `CodePackage`especifica que o diretório de trabalho será definido na raiz`GuestService1Pkg` do pacote de aplicações (na estrutura de ficheiros anterior).
    * `Work`especifica que os ficheiros são colocados num subdiretório chamado trabalho.

A WorkingFolder é útil para definir o diretório de trabalho correto para que os caminhos relativos possam ser utilizados pelas scripts de aplicação ou inicialização.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Atualizar pontos finais e registar-se com o Serviço de Nomeação para comunicação

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```

No exemplo anterior, `Endpoint` o elemento especifica os pontos finais que a aplicação pode ouvir. Neste exemplo, a aplicação Node.js ouve em http no porto 3000.

Além disso, pode pedir ao Service Fabric que publique este ponto final no Serviço de Nomeação para que outros serviços possam descobrir o endereço final deste serviço. Isto permite-lhe comunicar entre serviços que são executáveis por hóspedes.
O endereço final publicado é `UriScheme://IPAddressOrFQDN:Port/PathSuffix`do formulário . `UriScheme`e `PathSuffix` são atributos opcionais. `IPAddressOrFQDN`é o endereço IP ou o nome de domínio totalmente qualificado do nó em que este executável é colocado, e é calculado para si.

No exemplo seguinte, uma vez implementado o serviço, no Service Fabric `http://10.1.4.92:3000/myapp/` Explorer vê-se um ponto final semelhante ao publicado para a instância de serviço. Ou se isto é uma `http://localhost:3000/myapp/`máquina local.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```

Pode utilizar estes endereços com [procuração inversa](service-fabric-reverseproxy.md) para comunicar entre serviços.

### <a name="edit-the-application-manifest-file"></a>Editar o ficheiro manifesto da aplicação

Depois de configurar `Servicemanifest.xml` o ficheiro, tem de `ApplicationManifest.xml` fazer algumas alterações no ficheiro para garantir que o tipo e o nome corretos são utilizados.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

No `ServiceManifestImport` elemento, pode especificar um ou mais serviços que pretende incluir na aplicação. Os serviços `ServiceManifestName`são referenciados, o que especifica `ServiceManifest.xml` o nome do diretório onde o ficheiro está localizado.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurar a exploração madeireira

Para executáveis de hóspedes, é útil poder ver registos de consolas para saber se os scripts de aplicação e configuração mostram algum erro.
A reorientação da consola `ServiceManifest.xml` pode `ConsoleRedirection` ser configurada no ficheiro utilizando o elemento.

> [!WARNING]
> Nunca utilize a política de reorientação da consola numa aplicação que é implantada na produção porque isso pode afetar o failover da aplicação. *Utilize-o apenas* para fins de desenvolvimento local e depuração.  
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

`ConsoleRedirection`pode ser usado para redirecionar a saída da consola (tanto stdout como stderr) para um diretório de trabalho. Isto fornece a capacidade de verificar se não existem erros durante a configuração ou execução da aplicação no cluster Service Fabric.

`FileRetentionCount`determina quantos ficheiros são guardados no diretório de trabalho. Um valor de 5, por exemplo, significa que os ficheiros de registo das cinco execuções anteriores são armazenados no diretório de trabalho.

`FileMaxSizeInKb`especifica o tamanho máximo dos ficheiros de registo.

Os ficheiros de registo são guardados numa das diretórios de trabalho do serviço. Para determinar onde os ficheiros estão localizados, utilize o Service Fabric Explorer para determinar em que nó o serviço está a funcionar e qual o diretório de trabalho que está a ser utilizado. Este processo é abordado mais tarde neste artigo.

## <a name="deployment"></a>Implementação

O último passo é implementar a [sua aplicação.](service-fabric-deploy-remove-applications.md) O seguinte script PowerShell mostra como implementar a sua aplicação para o cluster de desenvolvimento local, e iniciar um novo serviço de Tecido de Serviço.

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
> [Comprima o pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para a loja de imagens se o pacote for grande ou tiver muitos ficheiros. Leia mais [aqui.](service-fabric-deploy-remove-applications.md#upload-the-application-package)
>

Um serviço de tecido de serviço pode ser implantado em várias "configurações". Por exemplo, pode ser implantado como instâncias individuais ou múltiplas, ou pode ser implantado de modo a que haja um caso do serviço em cada nó do cluster Service Fabric.

O `InstanceCount` parâmetro do `New-ServiceFabricService` cmdlet é utilizado para especificar quantas instâncias do serviço devem ser lançadas no cluster Service Fabric. Pode definir `InstanceCount` o valor, dependendo do tipo de aplicação que está a implementar. Os dois cenários mais comuns são:

* `InstanceCount = "1"`. Neste caso, apenas um caso do serviço é implantado no cluster. O programador do Service Fabric determina em que nó o serviço vai ser implantado.
* `InstanceCount ="-1"`. Neste caso, uma instância do serviço é implantada em cada nó do cluster Service Fabric. O resultado é ter uma (e única) instância do serviço para cada nó no cluster.

Esta é uma configuração útil para aplicações front-end (por exemplo, um ponto final REST), porque as aplicações do cliente precisam de "ligar" a qualquer um dos nós do cluster para usar o ponto final. Esta configuração também pode ser usada quando, por exemplo, todos os nós do cluster Service Fabric estão ligados a um equilibrador de carga. O tráfego do cliente pode então ser distribuído pelo serviço que está em execução em todos os nós do cluster.

## <a name="check-your-running-application"></a>Verifique a sua aplicação de execução
No Service Fabric Explorer, identifique o nó onde o serviço está em funcionamento. Neste exemplo, funciona no Nó1:

![Nó onde o serviço está funcionando](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Se navegar até ao nó e navegar para a aplicação, verá as informações essenciais do nó, incluindo a sua localização no disco.

![Localização no disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Se navegar no diretório utilizando o Server Explorer, pode encontrar o diretório de trabalho e a pasta de registo do serviço, como mostra a seguinte imagem:

![Localização do registo](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a embalar um hóspede executável e a implantá-lo para o Service Fabric. Consulte os seguintes artigos para obter informações e tarefas relacionadas.

* [Amostra para embalagem e implantação de um hóspede executável,](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)incluindo uma ligação ao pré-lançamento da ferramenta de embalagem
* [Amostra de dois executáveis convidados (C# e nodejs) comunicando através do serviço naming usando REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Crie a sua primeira aplicação de Tecido de Serviço usando o Estúdio Visual](service-fabric-tutorial-create-dotnet-app.md)
