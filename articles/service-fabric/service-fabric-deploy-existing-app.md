---
title: Implementar um tecido de serviço Azure existente
description: Saiba como embalar uma aplicação existente como um hóspede executável, para que possa ser implantada num cluster de Tecido de Serviço.
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 72fde75e16341164106bb952d0bb66b83be744e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86259265"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Pacote e implemente um executável existente para o tecido de serviço

Ao embalar um executável existente como [um hóspede executável,](service-fabric-guest-executables-introduction.md)pode escolher usar um modelo de projeto Visual Studio ou [criar manualmente o pacote de aplicações](#manually). Utilizando o Visual Studio, a estrutura do pacote de aplicações e os ficheiros manifestos são criados pelo novo modelo de projeto para si.

> [!TIP]
> A maneira mais fácil de embalar um Windows existente executável em um serviço é usar o Visual Studio e em Linux para usar Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Use o Visual Studio para embalar e implementar um executável existente

O Visual Studio fornece um modelo de serviço de serviço de Tecido de Serviço para ajudá-lo a implementar um hóspede executável para um cluster de Tecido de Serviço.

1. Escolha **o Arquivo** Novo  >  **Projeto** e crie uma aplicação de Tecido de Serviço.
2. Escolha **o Hóspede Executável** como o modelo de serviço.
3. Clique **em navegar** para selecionar a pasta com o seu executável e preencha os restantes parâmetros para criar o serviço.
   * *Código Pacote Comportamento*. Pode ser configurado para copiar todo o conteúdo da sua pasta para o Visual Studio Project, o que é útil se o executável não mudar. Se espera que o executável mude e queira que a capacidade de captar novas construções dinamicamente, pode optar por ligar-se à pasta. Pode utilizar pastas ligadas ao criar o projeto de aplicação no Visual Studio. Isto liga-se à localização de origem a partir do projeto, possibilitando a atualização do hóspede executável no seu destino de origem. Estas atualizações tornam-se parte do pacote de aplicações em construção.
   * *O programa* especifica o executável que deve ser executado para iniciar o serviço.
   * *Os argumentos* especificam os argumentos que devem ser transmitidos ao executável. Pode ser uma lista de parâmetros com argumentos.
   * *WorkingFolder* especifica o diretório de trabalho para o processo que vai ser iniciado. Pode especificar três valores:
     * `CodeBase` especifica que o diretório de trabalho será definido para o diretório de código no pacote de pedidos `Code` (diretório indicado na estrutura de ficheiros anterior).
     * `CodePackage` especifica que o diretório de trabalho será definido na raiz do pacote de aplicações `GuestService1Pkg` (indicado na estrutura de ficheiros anterior).
     * `Work` especifica que os ficheiros são colocados num subdiretório chamado trabalho.
4. Dê um nome ao serviço e clique em **OK**.
5. Se o seu serviço precisar de um ponto final de comunicação, pode agora adicionar o protocolo, porta e escrever ao ficheiro ServiceManifest.xml. Por exemplo: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Agora pode usar o pacote e publicar ação contra o seu cluster local depurando a solução no Visual Studio. Quando estiver pronto, pode publicar a aplicação num cluster remoto ou verificar a solução para o controlo de origem.
7. Leia [a sua aplicação de execução](#check-your-running-application) para ver como ver o seu serviço executável de hóspedes em execução no Service Fabric Explorer.

Para um exemplo de walkthrough, consulte [Criar a sua primeira aplicação executável de hóspedes utilizando o Visual Studio](quickstart-guest-app.md).

### <a name="packaging-multiple-executables-with-visual-studio"></a>Embalagem de vários executáveis com Visual Studio

Pode utilizar o Visual Studio para produzir um pacote de aplicações que contém vários executáveis de hóspedes. Depois de adicionar o primeiro hóspede executável, clique no projeto de aplicação e selecione o **serviço Add->New Service Fabric** para adicionar o segundo projeto executável do hóspede à solução.

> [!NOTE]
> Se optar por ligar a fonte no projeto Visual Studio, construindo a solução Visual Studio, certificar-se-á de que o seu pacote de candidaturas está atualizado com alterações na origem.

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Use Yeoman para embalar e implementar um executável existente no Linux

O procedimento para criar e implantar um hóspede executável no Linux é o mesmo que implementar uma aplicação de csharp ou java.

1. Num terminal, escreva `yo azuresfguest`.
2. Dê um nome à aplicação.
3. Nomeie o seu serviço e forneça os detalhes, incluindo o caminho do executável e os parâmetros com os quais deve ser invocado.

A Yeoman cria um pacote de aplicações com a aplicação adequada e ficheiros manifestos, juntamente com a instalação e desinstalação de scripts.

### <a name="packaging-multiple-executables-using-yeoman-on-linux"></a>Embalagem de vários executáveis usando Yeoman em Linux

Para adicionar outro serviço a uma aplicação já criada com o `yo`, execute os seguintes passos:

1. Altere o diretório para a raiz da aplicação existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é a aplicação criada por Yeoman.
2. Corra `yo azuresfguest:AddService` e forneça os detalhes necessários.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Pacote manual e implemente um executável existente

O processo de embalagem manual de um hóspede executável baseia-se nos seguintes passos gerais:

1. Crie a estrutura do diretório de pacotes.
2. Adicione os ficheiros de código e configuração da aplicação.
3. Edite o ficheiro manifesto de serviço.
4. Edite o ficheiro manifesto de aplicação.

### <a name="create-the-package-directory-structure"></a>Criar a estrutura do diretório de pacotes

Pode começar por criar a estrutura do diretório, conforme descrito no [Package a Azure Service Fabric App](./service-fabric-package-apps.md).

### <a name="add-the-applications-code-and-configuration-files"></a>Adicione os ficheiros de código e configuração da aplicação

Depois de ter criado a estrutura do diretório, pode adicionar os ficheiros de código e configuração da aplicação ao abrigo do código e configurar os diretórios. Também pode criar diretórios ou subdireções adicionais ao abrigo do código ou config diretórios.

O Service Fabric faz um `xcopy` dos conteúdos do diretório de raiz da aplicação, pelo que não existe uma estrutura predefinida para utilizar além da criação de dois diretórios de topo, código e configurações. (Pode escolher nomes diferentes se quiser. Mais detalhes estão na secção seguinte.)

> [!NOTE]
> Certifique-se de que inclui todos os ficheiros e dependências de que a aplicação necessita. O Service Fabric copia o conteúdo do pacote de aplicações em todos os nós do cluster onde os serviços da aplicação serão implantados. A embalagem deve conter todo o código que a aplicação precisa de executar. Não assuma que as dependências já estão instaladas.
>
>

### <a name="edit-the-service-manifest-file"></a>Editar o ficheiro manifesto de serviço

O próximo passo é editar o ficheiro manifesto de serviço para incluir as seguintes informações:

* O nome do tipo de serviço. Este é um ID que o Service Fabric usa para identificar um serviço.
* O comando a utilizar para lançar a aplicação (ExeHost).
* Qualquer script que precise de ser executado para configurar a aplicação (SetupEntrypoint).

Segue-se um exemplo de um `ServiceManifest.xml` ficheiro:

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

As seguintes secções reveem as diferentes partes do ficheiro que precisa de atualizar.

#### <a name="update-servicetypes"></a>AtualizarTipos de serviço

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Pode escolher o nome que `ServiceTypeName` quiser. O valor é usado no `ApplicationManifest.xml` ficheiro para identificar o serviço.
* Especifique `UseImplicitHost="true"`. Este atributo diz ao Service Fabric que o serviço se baseia numa aplicação independente, pelo que tudo o que o Service Fabric precisa de fazer é lançá-lo como um processo e monitorizar a sua saúde.

#### <a name="update-codepackage"></a>Atualização CodePackage
O elemento CodePackage especifica a localização (e versão) do código do serviço.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

O `Name` elemento é utilizado para especificar o nome do diretório no pacote de aplicações que contém o código do serviço. `CodePackage` também tem o `version` atributo. Isto pode ser usado para especificar a versão do código, e também pode ser usado potencialmente para atualizar o código do serviço usando a infraestrutura de gestão do ciclo de vida da aplicação em Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Opcional: Update SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
O elemento SetupEntryPoint é utilizado para especificar qualquer ficheiro executável ou de lote que deve ser executado antes do lançamento do código do serviço. É um passo opcional, pelo que não precisa de ser incluído se não for necessária uma inicialização. O SetupEntryPoint é executado sempre que o serviço é reiniciado.

Existe apenas um SetupEntryPoint, pelo que os scripts de configuração precisam de ser agrupados num único ficheiro de lote se a configuração da aplicação exigir vários scripts. O SetupEntryPoint pode executar qualquer tipo de ficheiro: ficheiros executáveis, ficheiros de lote e cmdlets PowerShell. Para mais detalhes, consulte [Configurar ConfigurarPoint](service-fabric-application-runas-security.md).

No exemplo anterior, o SetupEntryPoint executa um ficheiro de lote chamado `LaunchConfig.cmd` que está localizado no `scripts` subdiretor do diretório de códigos (assumindo que o elemento WorkingFolder está definido para CodeBase).

#### <a name="update-entrypoint"></a>Atualização Ponto de Entrada

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

O `ExeHost` elemento especifica o executável (e os argumentos) que devem ser usados para lançar o serviço. Pode opcionalmente adicionar o `IsExternalExecutable="true"` atributo para indicar que o programa é um `ExeHost` executável externo fora do pacote de código. Por exemplo, `<ExeHost IsExternalExecutable="true">`.

* `Program` especifica o nome do executável que deve iniciar o serviço.
* `Arguments` especifica os argumentos que devem ser passados para o executável. Pode ser uma lista de parâmetros com argumentos.
* `WorkingFolder` especifica o diretório de trabalho para o processo que vai ser iniciado. Pode especificar três valores:
  * `CodeBase` especifica que o diretório de trabalho será definido para o diretório de código no pacote de pedidos `Code` (diretório na estrutura de ficheiros anterior).
  * `CodePackage` especifica que o diretório de trabalho será definido como a raiz do pacote de aplicações `GuestService1Pkg` (na estrutura de ficheiros anterior).
    * `Work` especifica que os ficheiros são colocados num subdiretório chamado trabalho.

O WorkingFolder é útil para definir o diretório de trabalho correto para que os caminhos relativos possam ser utilizados quer pela aplicação quer pelos scripts de inicialização.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Atualizar Pontos finais e registar-se com o Serviço de Nomeação para comunicação

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```

No exemplo anterior, o `Endpoint` elemento especifica os pontos finais que a aplicação pode ouvir. Neste exemplo, a aplicação Node.js escuta em http no porto 3000.

Além disso, pode pedir à Service Fabric que publique este ponto final no Serviço de Nomeação para que outros serviços possam descobrir o endereço de ponto final para este serviço. Isto permite-lhe comunicar entre serviços que são executáveis de hóspedes.
O endereço de ponto final publicado é do formulário `UriScheme://IPAddressOrFQDN:Port/PathSuffix` . `UriScheme` e `PathSuffix` são atributos opcionais. `IPAddressOrFQDN` é o endereço IP ou o nome de domínio totalmente qualificado do nó que este executável é colocado, e é calculado para si.

No exemplo seguinte, uma vez que o serviço é implementado, no Service Fabric Explorer vê um ponto final semelhante ao `http://10.1.4.92:3000/myapp/` publicado para a instância de serviço. Ou se isto é uma máquina local, estás a `http://localhost:3000/myapp/` ver.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```

Pode utilizar estes endereços com [procuração inversa](service-fabric-reverseproxy.md) para comunicar entre serviços.

### <a name="edit-the-application-manifest-file"></a>Editar o ficheiro manifesto de aplicação

Uma vez configurado o `Servicemanifest.xml` ficheiro, tem de efec se alterar o `ApplicationManifest.xml` ficheiro para garantir que o tipo de serviço e o nome corretos são utilizados.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

No `ServiceManifestImport` elemento, pode especificar um ou mais serviços que pretende incluir na aplicação. Os serviços são referenciados com `ServiceManifestName` , que especifica o nome do diretório onde o ficheiro está `ServiceManifest.xml` localizado.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurar a exploração madeireira

Para os executáveis dos hóspedes, é útil ser capaz de ver os registos da consola para saber se os scripts de aplicação e configuração mostram erros.
A reorientação da consola pode ser configurada no `ServiceManifest.xml` ficheiro utilizando o `ConsoleRedirection` elemento.

> [!WARNING]
> Nunca utilize a política de redirecionamento da consola numa aplicação que seja implementada na produção porque isso pode afetar o failover da aplicação. *Utilize-o apenas* para fins de desenvolvimento local e depuragem.  
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

`ConsoleRedirection` pode ser usado para redirecionar a saída da consola (tanto em stdout como stderr) para um diretório de trabalho. Isto fornece a capacidade de verificar se não existem erros durante a configuração ou execução da aplicação no cluster de Tecido de Serviço.

`FileRetentionCount` determina quantos ficheiros são guardados no diretório de trabalho. Um valor de 5, por exemplo, significa que os ficheiros de registo das cinco execuções anteriores são armazenados no diretório de trabalho.

`FileMaxSizeInKb` especifica o tamanho máximo dos ficheiros de registo.

Os ficheiros de registo são guardados num dos diretórios de trabalho do serviço. Para determinar onde os ficheiros estão localizados, utilize o Service Fabric Explorer para determinar em que nó o serviço está em funcionamento e em que diretório de trabalho está a ser utilizado. Este processo é coberto mais tarde neste artigo.

## <a name="deployment"></a>Implementação

O último passo é implementar a [sua aplicação.](service-fabric-deploy-remove-applications.md) O seguinte script PowerShell mostra como implementar a sua aplicação no cluster de desenvolvimento local e iniciar um novo serviço de Tecido de Serviço.

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
> [Comprima a embalagem](service-fabric-package-apps.md#compress-a-package) antes de copiar para a loja de imagens se a embalagem for grande ou tiver muitos ficheiros. Leia mais [aqui](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Um serviço de Tecido de Serviço pode ser implantado em várias "configurações". Por exemplo, pode ser implantado como instâncias únicas ou múltiplas, ou pode ser implantado de forma a que haja uma instância do serviço em cada nó do cluster de Tecido de Serviço.

O `InstanceCount` parâmetro do `New-ServiceFabricService` cmdlet é utilizado para especificar quantas instâncias do serviço devem ser lançadas no cluster de Tecido de Serviço. Pode definir o `InstanceCount` valor, dependendo do tipo de aplicação que está a implementar. Os dois cenários mais comuns são:

* `InstanceCount = "1"`. Neste caso, apenas uma instância do serviço é implantada no cluster. O programador da Service Fabric determina em que nó o serviço vai ser implantado.
* `InstanceCount ="-1"`. Neste caso, um dos casos do serviço é implantado em todos os nós do cluster de Tecidos de Serviço. O resultado é ter uma (e apenas uma) instância do serviço para cada nó no cluster.

Esta é uma configuração útil para aplicações frontais (por exemplo, um ponto final REST), porque as aplicações do cliente precisam de "ligar" a qualquer um dos nós do cluster para usar o ponto final. Esta configuração também pode ser utilizada quando, por exemplo, todos os nós do cluster de Tecido de Serviço estão ligados a um equilibrador de carga. O tráfego do cliente pode então ser distribuído por todo o serviço que está a funcionar em todos os nós do cluster.

## <a name="check-your-running-application"></a>Verifique a sua aplicação de execução
No Service Fabric Explorer, identifique o nó onde o serviço está a funcionar. Neste exemplo, funciona no Node1:

![Nó onde o serviço está em execução](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Se navegar para o nó e navegar para a aplicação, consulte as informações essenciais do nó, incluindo a sua localização no disco.

![Localização no disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Se navegar no diretório utilizando o Server Explorer, pode encontrar o diretório de trabalho e a pasta de registo do serviço, como mostra a seguinte imagem:

![Localização do log](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a embalar um hóspede executável e implantá-lo para o Service Fabric. Consulte os seguintes artigos para obter informações e tarefas relacionadas.

* [Amostra para embalagem e implantação de um hóspede executável,](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)incluindo um link para o pré-relançamento da ferramenta de embalagem
* [Amostra de dois executáveis de hóspedes (C# e nodejs) comunicando através do serviço Naming usando REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Crie a sua primeira aplicação de Tecido de Serviço utilizando o Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
