---
title: Empacotar um aplicativo de Service Fabric do Azure
description: Saiba mais sobre como empacotar um aplicativo de Service Fabric do Azure e como se preparar para a implantação em um cluster.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 16bd48963040a8e30ff81f40c01134014eaccf48
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639688"
---
# <a name="package-an-application"></a>Criar pacote de uma aplicação

Este artigo descreve como empacotar um aplicativo Service Fabric e deixá-lo pronto para implantação.

## <a name="package-layout"></a>Layout do pacote

O manifesto do aplicativo, um ou mais manifestos do serviço e outros arquivos de pacote necessários devem ser organizados em um layout específico para implantação em um Cluster Service Fabric. Os manifestos de exemplo neste artigo precisariam ser organizados na seguinte estrutura de diretório:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

As pastas são nomeadas para corresponder aos atributos de **nome** de cada elemento correspondente. Por exemplo, se o manifesto do serviço contivesse dois pacotes de código com os nomes **Mycódigoa** e **MyCodeB**, duas pastas com os mesmos nomes conteriam os binários necessários para cada pacote de código.

## <a name="use-setupentrypoint"></a>Usar SetupEntryPoint

Cenários típicos para usar **SetupEntryPoint** são quando você precisa executar um executável antes do início do serviço ou você precisa executar uma operação com privilégios elevados. Por exemplo:

* Configurar e inicializar variáveis de ambiente que o executável de serviço precisa. Ele não está limitado a apenas executáveis escritos por meio dos modelos de programação de Service Fabric. Por exemplo, NPM. exe precisa de algumas variáveis de ambiente configuradas para implantar um aplicativo node. js.
* Configurando o controle de acesso instalando certificados de segurança.

Para obter mais informações sobre como configurar o **SetupEntryPoint**, consulte [Configurar a política para um ponto de entrada de instalação de serviço](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Configurar

### <a name="build-a-package-by-using-visual-studio"></a>Criar um pacote usando o Visual Studio

Se você usar o Visual Studio 2015 para criar seu aplicativo, poderá usar o comando Package para criar automaticamente um pacote que corresponda ao layout descrito acima.

Para criar um pacote, clique com o botão direito do mouse no projeto de aplicativo em Gerenciador de Soluções e escolha o comando de pacote, conforme mostrado abaixo:

![Empacotando um aplicativo com o Visual Studio][vs-package-command]

Quando o empacotamento for concluído, você poderá encontrar o local do pacote na janela de **saída** . A etapa de empacotamento ocorre automaticamente quando você implanta ou depura seu aplicativo no Visual Studio.

### <a name="build-a-package-by-command-line"></a>Compilar um pacote por linha de comando

Também é possível empacotar programaticamente seu aplicativo usando `msbuild.exe`. Nos bastidores, o Visual Studio está executando-o para que a saída seja a mesma.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Testar o pacote

Você pode verificar a estrutura do pacote localmente por meio do PowerShell usando o comando [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) .
Esse comando verifica se há problemas de análise de manifesto e verifica todas as referências. Esse comando só verifica a exatidão estrutural dos diretórios e arquivos no pacote.
Ele não verifica qualquer conteúdo do pacote de dados ou código além de verificar se todos os arquivos necessários estão presentes.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Esse erro mostra que o arquivo *mySetup. bat* referenciado no manifesto do serviço **SetupEntryPoint** está faltando no pacote de códigos. Depois que o arquivo ausente é adicionado, a verificação do aplicativo passa:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Se seu aplicativo tiver [parâmetros de aplicativo](service-fabric-manage-multiple-environment-app-configuration.md) definidos, você poderá passá-los em [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) para validação adequada.

Se você souber o cluster em que o aplicativo será implantado, é recomendável passar o parâmetro `ImageStoreConnectionString`. Nesse caso, o pacote também é validado em relação às versões anteriores do aplicativo que já estão em execução no cluster. Por exemplo, a validação pode detectar se um pacote com a mesma versão, mas conteúdo diferente, já foi implantado.  

Depois que o aplicativo for empacotado corretamente e passar na validação, considere compactar o pacote para operações de implantação mais rápidas.

## <a name="compress-a-package"></a>Compactar um pacote

Quando um pacote é grande ou tem muitos arquivos, você pode compactá-lo para uma implantação mais rápida. A compactação reduz o número de arquivos e o tamanho do pacote.
Para um pacote de aplicativos compactados, [carregar o pacote de aplicativos](service-fabric-deploy-remove-applications.md#upload-the-application-package) pode levar mais tempo em comparação com o carregamento do pacote descompactado, especialmente se a compactação for feita como parte da cópia. Com a compactação, o [registro](service-fabric-deploy-remove-applications.md#register-the-application-package) e o cancelamento [do registro do tipo de aplicativo](service-fabric-deploy-remove-applications.md#unregister-an-application-type) são mais rápidos.

O mecanismo de implantação é o mesmo para pacotes compactados e descompactados. Se o pacote for compactado, ele será armazenado como tal no repositório de imagens de cluster e será descompactado no nó antes da execução do aplicativo.
A compactação substitui o pacote de Service Fabric válido pela versão compactada. A pasta deve permitir permissões de gravação. A execução da compactação em um pacote já compactado não produz nenhuma alteração.

Você pode compactar um pacote executando o comando do PowerShell [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) com a opção `CompressPackage`. Você pode descompactar o pacote com o mesmo comando, usando `UncompressPackage` opção.

O comando a seguir compacta o pacote sem copiá-lo para o repositório de imagens. Você pode copiar um pacote compactado para um ou mais clusters Service Fabric, conforme necessário, usando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) sem o sinalizador `SkipCopy`.
O pacote agora inclui arquivos compactados para os pacotes `code`, `config`e `data`. O manifesto do aplicativo e os manifestos do serviço não são compactados, pois eles são necessários para muitas operações internas. Por exemplo, compartilhamento de pacote, extração de nome e versão de tipo de aplicativo para determinadas validações, todos precisam acessar os manifestos. O descompactar os manifestos tornaria essas operações ineficientes.

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Como alternativa, você pode compactar e copiar o pacote com [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) em uma única etapa.
Se o pacote for grande, forneça um tempo limite suficientemente alto para dar tempo tanto para a compactação do pacote quanto para o upload para o cluster.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Internamente, o Service Fabric computa somas de verificação para os pacotes de aplicativos para validação. Ao usar a compactação, as somas de verificação são computadas nas versões compactadas de cada pacote. A geração de um novo zip do mesmo pacote de aplicativos cria somas de verificação diferentes. Para evitar erros de validação, use o [provisionamento diff](service-fabric-application-upgrade-advanced.md). Com essa opção, não inclua os pacotes inalterados na nova versão. Em vez disso, referencie-os diretamente do novo manifesto do serviço.

Se o provisionamento de comparação não for uma opção e você precisar incluir os pacotes, gere novas versões para os pacotes `code`, `config`e `data` para evitar a incompatibilidade de soma de verificação. A geração de novas versões para pacotes inalterados é necessária quando um pacote compactado é usado, independentemente de a versão anterior usar compactação ou não.

O pacote agora está empacotado corretamente, validado e compactado (se necessário), portanto, está pronto para [implantação](service-fabric-deploy-remove-applications.md) em um ou mais clusters de Service Fabric.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Compactar pacotes ao implantar usando o Visual Studio

Você pode instruir o Visual Studio a compactar pacotes na implantação, adicionando o elemento `CopyPackageParameters` ao seu perfil de publicação e definir o atributo `CompressPackage` como `true`.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Criar um sfpkg

A partir da versão 6,1, Service Fabric permite o provisionamento de um repositório externo.
Com essa opção, o pacote de aplicativos não precisa ser copiado para o repositório de imagens. Em vez disso, você pode criar um `sfpkg` e carregá-lo em um repositório externo e, em seguida, fornecer o URI de download para Service Fabric ao provisionar. O mesmo pacote pode ser provisionado em vários clusters. O provisionamento do repositório externo salva o tempo necessário para copiar o pacote para cada cluster.

O arquivo de `sfpkg` é um zip que contém o pacote de aplicativo inicial e tem a extensão ". sfpkg".
Dentro do zip, o pacote de aplicativos pode ser compactado ou descompactado. A compactação do pacote de aplicativos dentro do zip é feita nos níveis de código, configuração e pacote de dados, conforme [mencionado anteriormente](service-fabric-package-apps.md#compress-a-package).

Para criar um `sfpkg`, comece com uma pasta que contém o pacote de aplicativo original, compactado ou não. Em seguida, use qualquer utilitário para compactar a pasta com a extensão ". sfpkg". Por exemplo, use [zipfile. CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

O `sfpkg` deve ser carregado para o repositório externo fora da banda, fora do Service Fabric. O repositório externo pode ser qualquer armazenamento que expõe um ponto de extremidade http ou HTTPS REST. Durante o provisionamento, Service Fabric executa uma operação GET para baixar o pacote de aplicativos `sfpkg`, de modo que o repositório deve permitir acesso de leitura para o pacote.

Para provisionar o pacote, use o provisionamento externo, que requer o URI de download e as informações de tipo de aplicativo.

>[!NOTE]
> O provisionamento baseado no caminho relativo do repositório de imagens atualmente não dá suporte a arquivos `sfpkg`. Portanto, o `sfpkg` não deve ser copiado para o repositório de imagens.

## <a name="next-steps"></a>Passos seguintes

[Implantar e remover aplicativos][10] descreve como usar o PowerShell para gerenciar instâncias de aplicativo

[Gerenciar parâmetros de aplicativo para vários ambientes][11] descreve como configurar parâmetros e variáveis de ambiente para diferentes instâncias de aplicativo.

[Configurar políticas de segurança para seu aplicativo][12] descreve como executar serviços em políticas de segurança para restringir o acesso.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md