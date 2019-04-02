---
title: Empacotar um serviço do Azure dos recursos de infraestrutura aplicação | Documentos da Microsoft
description: Como empacotar uma aplicação do Service Fabric antes de implementar um cluster.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: b8e66a9d5bba0c48f15b1ccd3f2d47e5405db792
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58791597"
---
# <a name="package-an-application"></a>Criar pacote de uma aplicação

Este artigo descreve como empacotar uma aplicação do Service Fabric e torná-lo pronto para implantação.

## <a name="package-layout"></a>Layout do pacote

O manifesto do aplicativo, um ou mais manifestos de serviço e outros ficheiros de pacote necessário devem ser organizados num layout específico para a implantação num cluster do Service Fabric. Os manifestos de exemplo neste artigo precisaria ser organizados na seguinte estrutura de diretório:

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

As pastas são nomeadas de acordo com o **nome** atributos de cada elemento correspondente. Por exemplo, se o manifesto do serviço continha dois pacotes de código com os nomes **MyCodeA** e **MyCodeB**, em seguida, duas pastas com os mesmos nomes iria conter os binários necessários para cada pacote do código.

## <a name="use-setupentrypoint"></a>Utilizar SetupEntryPoint

Cenários típicos para usar **SetupEntryPoint** quando tiver de executar um executável antes de iniciar o serviço ou tiver de realizar uma operação com privilégios elevados. Por exemplo:

* Como configurar e inicializar variáveis de ambiente que tem do executável do serviço. Não se limita a apenas os executáveis gravados via os modelos de programação do Service Fabric. Por exemplo, npm.exe tem algumas variáveis de ambiente configurados para implementar uma aplicação node. js.
* Configuração do controlo de acesso através da instalação de certificados de segurança.

Para obter mais informações sobre como configurar o **SetupEntryPoint**, consulte [configurar a política para um ponto de entrada de configuração de serviço](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Configurar

### <a name="build-a-package-by-using-visual-studio"></a>Criar um pacote usando o Visual Studio

Se utilizar o Visual Studio 2015 para criar a sua aplicação, pode utilizar o comando de pacote para criar automaticamente um pacote que corresponda ao esquema descrito acima.

Para criar um pacote, com o botão direito do projeto de aplicativo no Explorador de soluções e escolha o comando de pacote, conforme mostrado abaixo:

![Empacotar um aplicativo com o Visual Studio][vs-package-command]

Quando o empacotamento estiver concluído, pode encontrar a localização do pacote no **saída** janela. A etapa de empacotamento ocorre automaticamente quando implementar ou depurar a sua aplicação no Visual Studio.

### <a name="build-a-package-by-command-line"></a>Criar um pacote através da linha de comandos

Também é possível por meio de programação empacotar a sua aplicação utilizando `msbuild.exe`. Nos bastidores, Visual Studio é executá-lo para que a saída é a mesma.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Testar o pacote

Pode verificar a estrutura do pacote localmente através do PowerShell com o [teste ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) comando.
Este comando verifica a existência de manifesto da análise de problemas e verifique se todas as referências. Este comando só verifica se a correção estrutural dos diretórios e ficheiros no pacote.
Ele não verifica a qualquer um do conteúdo do pacote de código ou dados para além de verificar se todos os arquivos necessários estão presentes.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Este erro mostra que o *MySetup.bat* referenciado no manifesto do serviço de arquivo **SetupEntryPoint** está em falta no pacote do código. Depois do ficheiro em falta é adicionado, passa a verificação de aplicação:

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

Se seu aplicativo tiver [parâmetros de aplicação](service-fabric-manage-multiple-environment-app-configuration.md) definida, poderá passá-los na [teste ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) para validação adequada.

Se sabe o cluster onde o aplicativo será implantado, recomenda-se passar o `ImageStoreConnectionString` parâmetro. Neste caso, o pacote também é validado em relação a versões anteriores da aplicação que já estão em execução no cluster. Por exemplo, a validação pode detetar se um pacote com a mesma versão, mas conteúdo diferente já foi implementado.  

Depois do aplicativo é empacotado corretamente e passa na validação, considere a compressão do pacote para as operações de implementação mais rápidos.

## <a name="compress-a-package"></a>Comprimir um pacote

Quando um pacote é grande ou tem muitos ficheiros, é possível compactar para implantação mais rápida. Compressão reduz o número de ficheiros e o tamanho do pacote.
Para um pacote de aplicação comprimido [carregar o pacote de aplicação](service-fabric-deploy-remove-applications.md#upload-the-application-package) podem demorar mais tempo em comparação com a carregar o pacote não comprimido, especialmente se a compressão é feita como parte da cópia. Com a compressão, [registar](service-fabric-deploy-remove-applications.md#register-the-application-package) e [anular o registo do tipo de aplicação](service-fabric-deploy-remove-applications.md#unregister-an-application-type) são mais rápidos.

O mecanismo de implantação é a mesmo para pacotes comprimidos e descomprimidos. Se o pacote é compactado, ele é armazenado como tal no armazenamento de imagens do cluster e descomprimido no nó antes do aplicativo é executado.
A compressão substitui o pacote válido do Service Fabric com a versão compactada. A pasta tem de permitir permissões de escrita. Em execução a compressão num pacote já comprimido produz sem alterações.

É possível compactar um pacote ao executar o comando do Powershell [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) com `CompressPackage` mudar. Pode descomprimir o pacote com o mesmo comando com `UncompressPackage` mudar.

O seguinte comando compacta o pacote sem copiá-lo para o arquivo de imagem. Pode copiar um pacote comprimido para uma ou mais clusters do Service Fabric, conforme necessário, usando [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) sem o `SkipCopy` sinalizador.
Agora, o pacote inclui arquivos compactados para o `code`, `config`, e `data` pacotes. O manifesto da aplicação e manifestos de serviço não são comprimidos, porque eles são necessários para várias operações internas. Por exemplo, a partilha de pacote, extração de nome e a versão do tipo de aplicação para determinados validações todos precisam de aceder os manifestos. Comprimir os manifestos seria tornar essas operações ineficiente.

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

Em alternativa, pode comprimir e copiar o pacote com [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) num único passo.
Se o pacote é grande, fornece um tempo limite alto o suficiente para dar tempo para a compressão do pacote e o carregamento para o cluster.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Internamente, o Service Fabric calcula as somas de verificação para os pacotes de aplicativos para a validação. Quando utilizar compressão, as somas de verificação são calculadas nas versões de cada pacote zipadas. Gerar um novo zip a partir do mesmo pacote de aplicação cria as somas de verificação diferentes. Para evitar erros de validação, utilize [diff aprovisionamento](service-fabric-application-upgrade-advanced.md). Com esta opção, não inclua os pacotes sem alterações na nova versão. Em vez disso, referenciá-los diretamente a partir do manifesto do serviço novo.

Se diff aprovisionamento não é uma opção e tem de incluir os pacotes, gerar novas versões para o `code`, `config`, e `data` pacotes para evitar o erro de correspondência de soma de verificação. A gerar novas versões de pacotes inalterados é necessária quando é utilizado um pacote comprimido, independentemente se a versão anterior utiliza compressão ou não.

O pacote é agora empacotado corretamente, validado e comprimido (se necessário), para que fique pronta para [implementação](service-fabric-deploy-remove-applications.md) para um ou mais clusters do Service Fabric.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Comprimir pacotes durante a implantação usando o Visual Studio

Pode instruir o Visual Studio para comprimir os pacotes de implantação, adicionando a `CopyPackageParameters` elemento para o seu perfil de publicação e o conjunto a `CompressPackage` atributo para `true`.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Criar um sfpkg

A partir da versão 6.1, Service Fabric permite o aprovisionamento de um arquivo externo.
Com esta opção, o pacote de aplicação não tem de ser copiados para o armazenamento de imagens. Em vez disso, pode criar um `sfpkg` e carregá-lo para um armazenamento externo, em seguida, forneça o URI de transferência para o Service Fabric quando são aprovisionados. O mesmo pacote pode ser aprovisionado para vários clusters. Aprovisionamento a partir da loja externa economiza o tempo necessário para copiar o pacote para cada cluster.

O `sfpkg` ficheiro é um zip que contém o pacote de aplicação inicial e tem a extensão ". sfpkg".
Dentro do zip, o pacote de aplicação pode ser compactado ou descomprimido. A compressão do pacote de aplicação no interior do zip é feita no código, configuração e níveis de pacote de dados, como [mencionado anteriormente](service-fabric-package-apps.md#compress-a-package).

Para criar um `sfpkg`, começar com uma pasta que contém o pacote de aplicação original, ou não comprimido. Em seguida, utilize qualquer utilitário zip na pasta com a extensão ". sfpkg". Por exemplo, usar [ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

O `sfpkg` tem de ser carregado para o arquivo externo fora de banda, fora do Service Fabric. O arquivo externo pode ser qualquer armazenamento que expõe um ponto final de http ou https REST. Durante o aprovisionamento, o Service Fabric executa uma operação de obtenção de transferir o `sfpkg` pacote de aplicação, para que o arquivo tem de permitir o acesso de leitura para o pacote.

Para aprovisionar o pacote, utilize aprovisionar externo, o que requer o URI de transferência e as informações de tipo de aplicação.

>[!NOTE]
> Atualmente não suporta o aprovisionamento com base no caminho relativo do arquivo de imagem `sfpkg` ficheiros. Por conseguinte, o `sfpkg` não deve ser copiado para o armazenamento de imagens.

## <a name="next-steps"></a>Passos Seguintes

[Implantar e remover aplicativos] [ 10] descreve como utilizar o PowerShell para gerenciar as instâncias da aplicação

[Gerir parâmetros da aplicação para vários ambientes] [ 11] descreve como configurar parâmetros e variáveis de ambiente para diferentes instâncias da aplicação.

[Configurar políticas de segurança para o seu aplicativo] [ 12] descreve como executar serviços nas políticas de segurança para restringir o acesso.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md