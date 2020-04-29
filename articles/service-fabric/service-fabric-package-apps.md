---
title: Pacote uma aplicação Azure Service Fabric
description: Saiba mais sobre a embalagem de uma aplicação Azure Service Fabric e como se preparar para a implantação para um cluster.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7c99eec28ac06ecf666d6dda1015f889841a5dbf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79258346"
---
# <a name="package-an-application"></a>Criar pacote de uma aplicação

Este artigo descreve como embalar uma aplicação de Tecido de Serviço e torná-la pronta para a implantação.

## <a name="package-layout"></a>Layout de pacote

O manifesto de aplicação, um ou mais manifestos de serviço e outros ficheiros de pacotes necessários devem ser organizados num layout específico para implantação num cluster de Tecido de Serviço. O exemplo manifesta-se neste artigo teria de ser organizado na seguinte estrutura de diretório:

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

As pastas são nomeadas para corresponder aos atributos de **nome** de cada elemento correspondente. Por exemplo, se o manifesto de serviço contivesse dois pacotes de código com os nomes **MyCodeA** e **MyCodeB,** então duas pastas com os mesmos nomes conteriam os binários necessários para cada pacote de código.

## <a name="use-setupentrypoint"></a>Use SetupEntryPoint

Cenários típicos para a utilização do **SetupEntryPoint** são quando precisa de executar um executável antes do início do serviço ou precisa de realizar uma operação com privilégios elevados. Por exemplo:

* Configuração e inicialização de variáveis ambientais que o serviço executável necessita. Não se limita apenas executáveis escritos através dos modelos de programação do Tecido de Serviço. Por exemplo, npm.exe precisa de algumas variáveis ambientais configuradas para implementar uma aplicação nó.js.
* Configurar o controlo de acesso instalando certificados de segurança.

Para obter mais informações sobre como configurar o **SetupEntryPoint,** consulte [Configurar a política para um ponto](service-fabric-application-runas-security.md) de entrada de configuração de serviço

<a id="Package-App"></a>

## <a name="configure"></a>Configurar

### <a name="build-a-package-by-using-visual-studio"></a>Construa um pacote usando o Estúdio Visual

Se usou o Visual Studio para criar a sua aplicação, pode utilizar o comando *Pacote* para criar automaticamente um pacote que corresponda ao layout acima descrito.

Para criar um pacote, clique no projeto de aplicação no *Solution Explorer* e escolha o comando **Pacote:**

![Embalar uma aplicação com o Estúdio Visual][vs-package-command]

Quando a embalagem estiver completa, pode encontrar a localização da embalagem na janela **Saída.** O passo de embalagem ocorre automaticamente quando implementa ou depura a sua aplicação no Estúdio Visual.

### <a name="build-a-package-by-command-line"></a>Construa um pacote por linha de comando

Também é possível embalar programáticamente `msbuild.exe`a sua aplicação utilizando . Sob o capot, o Estúdio Visual está a executá-lo para que a saída seja a mesma.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Testar o pacote

Pode verificar a estrutura do pacote localmente através da PowerShell utilizando o comando [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)
Este comando verifica questões de análise manifesta e verifica todas as referências. Este comando apenas verifica a correção estrutural dos diretórios e ficheiros no pacote.
Não verifica nenhum dos conteúdos do código ou pacote de dados para além de verificar se todos os ficheiros necessários estão presentes.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Este erro mostra que o ficheiro *MySetup.bat* referenciado no manifesto de serviço **ConfiguraçãoEntryPoint** está em falta no pacote de código. Após a adição do ficheiro em falta, a verificação da aplicação passa:

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

Se a sua aplicação tiver parâmetros de [aplicação definidos,](service-fabric-manage-multiple-environment-app-configuration.md) pode passá-los no [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) para validação adequada.

Se conhecer o cluster onde a aplicação será implantada, recomenda-se que passe no `ImageStoreConnectionString` parâmetro. Neste caso, o pacote também é validado em relação a versões anteriores da aplicação que já estão em execução no cluster. Por exemplo, a validação pode detetar se um pacote com a mesma versão, mas conteúdo diferente já foi implementado.  

Uma vez que a aplicação seja embalada corretamente e passe a validação, considere comprimir o pacote para operações de implantação mais rápidas.

## <a name="compress-a-package"></a>Comprima um pacote

Quando um pacote é grande ou tem muitos ficheiros, pode comprimir para uma implementação mais rápida. A compressão reduz o número de ficheiros e o tamanho do pacote.
Para um pacote de aplicação comprimido, o upload do pacote de [aplicação](service-fabric-deploy-remove-applications.md#upload-the-application-package) pode demorar mais tempo em comparação com o upload da embalagem não comprimida, especialmente se a compressão for feita como parte da cópia. Com compressão, [registo](service-fabric-deploy-remove-applications.md#register-the-application-package) e [desinscrição do tipo de aplicação](service-fabric-deploy-remove-applications.md#unregister-an-application-type) são mais rápidos.

O mecanismo de implantação é o mesmo para embalagens comprimidos e não comprimidos. Se a embalagem estiver comprimida, é armazenada como tal na loja de imagens do cluster e não é comprimida no nó antes da execução da aplicação.
A compressão substitui a embalagem de tecido de serviço válida pela versão comprimido. A pasta deve permitir permissões de escrita. A compressão de funcionamento num pacote já comprimido não produz alterações.

Pode comprimir uma embalagem executando o pacote de `CompressPackage` comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) com interruptor. Pode descomprimir a embalagem com `UncompressPackage` o mesmo comando, utilizando o interruptor.

O comando seguinte comprime a embalagem sem a copiar para a loja de imagens. Pode copiar uma embalagem comprimido para um ou mais clusters de Tecido de Serviço, conforme necessário, utilizando o [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) sem a `SkipCopy` bandeira.
O pacote agora inclui ficheiros `config`com `data` fecho para os `code`pacotes e pacotes. O manifesto de candidatura e os manifestos de serviço não estão fechados, porque são necessários para muitas operações internas. Por exemplo, a partilha de pacotes, o nome do tipo de aplicação e a extração de versão para determinadas validações, todas precisam de aceder aos manifestos. Fechar os manifestos tornaria estas operações ineficientes.

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

Em alternativa, pode comprimir e copiar a embalagem com [copy-serviceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) num só passo.
Se a embalagem for grande, proporcione um tempo suficiente para dar tempo tanto à compressão do pacote como ao upload para o cluster.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Internamente, o Service Fabric calcula verificações para os pacotes de aplicação para validação. Ao utilizar a compressão, os cheques são calculados nas versões zipped de cada embalagem. Gerar um novo zip a partir do mesmo pacote de aplicações cria diferentes verificações. Para evitar erros de validação, utilize [o fornecimento difuso](service-fabric-application-upgrade-advanced.md). Com esta opção, não inclua os pacotes inalterados na nova versão. Em vez disso, remecite-os diretamente do novo manifesto de serviço.

Se o fornecimento difuso não for uma opção e tiver `code`de `config`incluir `data` as embalagens, gerar novas versões para o , e pacotes para evitar a incompatibilidade de verificação. É necessário gerar novas versões para pacotes inalterados quando é utilizada uma embalagem comprimido, independentemente de a versão anterior utilizar ou não compressão.

A embalagem está agora embalada corretamente, validada e comprimida (se necessário), pelo que está pronta para [ser implantada](service-fabric-deploy-remove-applications.md) para um ou mais clusters de Tecido de Serviço.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Comprima pacotes ao implementar usando o Estúdio Visual

Pode instruir o Visual Studio a comprimir `CopyPackageParameters` pacotes na implementação, `CompressPackage` adicionando `true`o elemento ao seu perfil de publicação e definir o atributo para .

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Criar um sfpkg

Começando pela versão 6.1, o Service Fabric permite o fornecimento de uma loja externa.
Com esta opção, o pacote de aplicação não tem de ser copiado para a loja de imagens. Em vez disso, `sfpkg` pode criar um e carregá-lo para uma loja externa, em seguida, fornecer o download URI para Service Fabric ao fornecer. O mesmo pacote pode ser provisionado a vários clusters. O fornecimento da loja externa poupa o tempo necessário para copiar o pacote para cada cluster.

O `sfpkg` ficheiro é um fecho que contém o pacote inicial de aplicação e tem a extensão ".sfpkg".
No interior do fecho, o pacote de aplicação pode ser comprimido ou descomprimido. A compressão do pacote de aplicação no interior do fecho é feita aos níveis de código, config e pacote de dados, como [mencionado anteriormente.](service-fabric-package-apps.md#compress-a-package)

Para criar `sfpkg`um , comece com uma pasta que contenha o pacote de aplicação original, comprimido ou não. Em seguida, utilize qualquer utilidade para fechar a pasta com a extensão ".sfpkg". Por exemplo, utilize [zipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

O `sfpkg` deve ser enviado para a loja externa fora da banda, fora do Tecido de Serviço. A loja externa pode ser qualquer loja que exponha um REST http ou https endpoint. Durante o provisionamento, o Service Fabric `sfpkg` executa uma operação GET para descarregar o pacote de aplicações, pelo que a loja deve permitir o acesso da READ para a embalagem.

Para fornecer o pacote, utilize a provisão externa, que requer o download URI e as informações do tipo de aplicação.

>[!NOTE]
> O provisionamento com base no caminho relativo `sfpkg` da loja de imagens não suporta atualmente ficheiros. Portanto, o `sfpkg` não deve ser copiado para a loja de imagens.

## <a name="next-steps"></a>Passos seguintes

[Implementar e remover aplicações][10] descreve como usar o PowerShell para gerir instâncias de aplicação

[A gestão dos parâmetros de aplicação para vários ambientes][11] descreve como configurar parâmetros e variáveis ambientais para diferentes instâncias de aplicação.

[Configure as políticas de segurança para a sua aplicação][12] descreve como executar serviços sob políticas de segurança para restringir o acesso.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md