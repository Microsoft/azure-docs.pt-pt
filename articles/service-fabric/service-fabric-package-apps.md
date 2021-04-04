---
title: Embale uma aplicação Azure Service Fabric
description: Saiba mais sobre a embalagem de uma aplicação Azure Service Fabric e como se preparar para a implementação de um cluster.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 168e6d6dc7ab5bfeccc4e1dabc7bd50efcbe8f34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789707"
---
# <a name="package-an-application"></a>Criar pacote de uma aplicação

Este artigo descreve como embalar uma aplicação de Tecido de Serviço e torná-la pronta para a sua implantação.

## <a name="package-layout"></a>Layout do pacote

O manifesto de aplicação, um ou mais manifestos de serviço, e outros ficheiros de pacote necessários devem ser organizados num layout específico para implantação num cluster de Tecido de Serviço. O exemplo manifesta-se neste artigo na seguinte estrutura de diretório:

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

As pastas são nomeadas para corresponder aos atributos **Nome** de cada elemento correspondente. Por exemplo, se o manifesto de serviço contiver dois pacotes de código com os nomes **MyCodeA** e **MyCodeB,** então duas pastas com os mesmos nomes conteriam os binários necessários para cada pacote de código.

## <a name="use-setupentrypoint"></a>Use SetupEntryPoint

Os cenários típicos para a utilização **do SetupEntryPoint** são quando precisa de executar um executável antes do início do serviço ou é necessário executar uma operação com privilégios elevados. Por exemplo:

* Configuração e inicialização de variáveis ambientais que o serviço necessita. Não se limita apenas a executáveis escritos através dos modelos de programação do Service Fabric. Por exemplo, npm.exe precisa de algumas variáveis ambientais configuradas para implementar uma aplicação node.js.
* Criação de controlo de acesso através da instalação de certificados de segurança.

Para obter mais informações sobre como configurar o **SetupEntryPoint,** consulte [Configurar a política para um ponto](service-fabric-application-runas-security.md) de entrada de configuração de serviço

<a id="Package-App"></a>

## <a name="configure"></a>Configurar

### <a name="build-a-package-by-using-visual-studio"></a>Construa um pacote usando o Visual Studio

Se utilizar o Visual Studio para criar a sua aplicação, pode utilizar o comando *Pacote* para criar automaticamente um pacote que corresponda ao layout acima descrito.

Para criar um pacote, clique com o botão direito no projeto de aplicação no *Solution Explorer* e escolha o comando **Pacote:**

![Embalar uma aplicação com Visual Studio][vs-package-command]

Quando a embalagem estiver completa, pode encontrar a localização da embalagem na janela **de saída.** O passo de embalagem ocorre automaticamente quando implementa ou depura a sua aplicação no Visual Studio.

### <a name="build-a-package-by-command-line"></a>Construir um pacote por linha de comando

Também é possível embalar programáticamente a sua aplicação utilizando `msbuild.exe` . Sob o capot, o Visual Studio está a executá-lo para que a saída seja a mesma.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Teste o pacote

Pode verificar a estrutura do pacote localmente através do PowerShell utilizando o comando [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage)
Este comando verifica se há problemas de análise manifesto e verifica todas as referências. Este comando apenas verifica a correção estrutural dos diretórios e ficheiros no pacote.
Não verifica nenhum dos conteúdos do código ou do pacote de dados para além de verificar se todos os ficheiros necessários estão presentes.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Este erro mostra que o ficheiro *MySetup.bat* referenciado no manifesto de serviço **SetupEntryPoint** está em falta no pacote de código. Após a adição do ficheiro em falta, a verificação da aplicação passa:

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

Se a sua aplicação tiver parâmetros de [aplicação definidos,](service-fabric-manage-multiple-environment-app-configuration.md) pode passá-los em [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) para validação adequada.

Se conhecer o cluster onde a aplicação será implantada, recomenda-se que passe no `ImageStoreConnectionString` parâmetro. Neste caso, o pacote também é validado em comparação com versões anteriores da aplicação que já estão em execução no cluster. Por exemplo, a validação pode detetar se um pacote com a mesma versão, mas já foi implementado um conteúdo diferente.  

Uma vez que a aplicação seja embalada corretamente e passe a validação, considere comprimir a embalagem para operações de implantação mais rápidas.

## <a name="compress-a-package"></a>Comprimir um pacote

Quando um pacote é grande ou tem muitos ficheiros, pode comprimi-lo para uma implementação mais rápida. A compressão reduz o número de ficheiros e o tamanho do pacote.
Para um pacote de aplicações comprimidos, [o upload do pacote de aplicações](service-fabric-deploy-remove-applications.md#upload-the-application-package) pode demorar mais tempo em comparação com o upload do pacote não comprimido, especialmente se a compressão for feita como parte da cópia. Com a compressão, [o registo](service-fabric-deploy-remove-applications.md#register-the-application-package) e [a des-registo do tipo de aplicação](service-fabric-deploy-remove-applications.md#unregister-an-application-type) são mais rápidos.

O mecanismo de implantação é o mesmo para os pacotes comprimidos e descomprimidos. Se a embalagem for comprimida, é armazenada como tal na loja de imagens do cluster e não é compacta no nó antes da aplicação ser executada.
A compressão substitui o pacote de tecido de serviço válido pela versão comprimido. A pasta deve permitir a escrita de permissões. A compressão em execução num pacote já comprimido não produz alterações.

Pode comprimir um pacote executando o [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) com `CompressPackage` o interruptor. Pode descomprimir a embalagem com o mesmo comando, utilizando o `UncompressPackage` interruptor.

O comando a seguir comprime a embalagem sem a copiar para a loja de imagens. Pode copiar um pacote comprimido para um ou mais clusters de Tecido de Serviço, conforme necessário, utilizando [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) sem a `SkipCopy` bandeira.
O pacote agora inclui ficheiros com fecho para o `code` `config` , e `data` pacotes. O manifesto de candidatura e os manifestos de serviço não são fechados, pois são necessários para muitas operações internas. Por exemplo, a partilha de pacotes, o nome do tipo de aplicação e a extração de versão para determinadas validações, todos precisam de aceder aos manifestos. Fechar os manifestos tornaria estas operações ineficientes.

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

Em alternativa, pode comprimir e copiar o pacote com [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) num só passo.
Se a embalagem for grande, forneça um tempo suficientemente alto para permitir tempo tanto para a compressão da embalagem como para o upload para o cluster.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Internamente, o Service Fabric calcula as datas de verificação dos pacotes de aplicação para validação. Ao utilizar a compressão, as as caixas de verificação são calculadas nas versões com fecho de cada embalagem. Gerar um novo zip a partir do mesmo pacote de aplicações cria diferentes despesas de verificação. Para evitar erros de validação, utilize [o fornecimento de difusão](service-fabric-application-upgrade-advanced.md). Com esta opção, não inclua os pacotes inalterados na nova versão. Em vez disso, refira-os diretamente do novo manifesto de serviço.

Se o fornecimento de difusão não for uma opção e tiver de incluir as embalagens, gere novas versões para o `code` `config` , e `data` pacotes para evitar incompatibilidades de checksum. Gerar novas versões para pacotes inalterados é necessário quando um pacote comprimido é usado, independentemente de a versão anterior utilizar ou não compressão.

A embalagem está agora embalada corretamente, validada e comprimida (se necessário), pelo que está pronta para [ser implantada](service-fabric-deploy-remove-applications.md) num ou mais clusters de Tecido de Serviço.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Compre pacotes de comprimimento ao implementar usando o Visual Studio

Pode instruir o Visual Studio a comprimir os pacotes na implementação, adicionando o `CopyPackageParameters` elemento ao seu perfil de publicação e definindo o atributo para `CompressPackage` `true` .

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Criar um sfpkg

A partir da versão 6.1, o Service Fabric permite o fornecimento a partir de uma loja externa.
Com esta opção, o pacote de aplicações não tem de ser copiado para a loja de imagens. Em vez disso, pode criar um `sfpkg` e carregá-lo para uma loja externa, em seguida, fornecer o download URI para Service Fabric ao fornecer. O mesmo pacote pode ser a provisionado a vários clusters. O fornecimento da loja externa poupa o tempo necessário para copiar a embalagem a cada cluster.

O `sfpkg` ficheiro é um zip que contém o pacote inicial de aplicação e tem a extensão ".sfpkg".
Dentro do fecho, a embalagem de aplicação pode ser comprimida ou descomprimida. A compressão do pacote de aplicações no interior do fecho é feita a níveis de código, config e pacote de dados, como [mencionado anteriormente](service-fabric-package-apps.md#compress-a-package).

Para criar uma `sfpkg` pasta que contenha o pacote de aplicações original, comprimido ou não. Em seguida, utilize qualquer utilidade para fechar a pasta com a extensão ".sfpkg". Por exemplo, utilize [ZipFile.CreateFromDirect .](/dotnet/api/system.io.compression.zipfile.createfromdirectory#System_IO_Compression_ZipFile_CreateFromDirectory_System_String_System_String_System_IO_Compression_CompressionLevel_System_Boolean_)

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

O `sfpkg` deve ser enviado para a loja externa fora da banda, fora da Service Fabric. A loja externa pode ser qualquer loja que exponha um ponto final REST http ou https. Durante o fornecimento, a Service Fabric executa uma operação GET para descarregar o `sfpkg` pacote de aplicações, pelo que a loja deve permitir o acesso ao READ para o pacote.

Para o fornecimento do pacote, utilize a disposição externa, que requer a informação de descarregamento URI e o tipo de aplicação.

>[!NOTE]
> O provisionamento baseado no caminho relativo da loja de imagens não suporta `sfpkg` ficheiros atualmente. Portanto, o `sfpkg` não deve ser copiado para a loja de imagens.

## <a name="next-steps"></a>Passos seguintes

[Implementar e remover aplicações][10] descreve como usar o PowerShell para gerir instâncias de aplicação

[Gerir parâmetros de aplicação para vários ambientes][11] descreve como configurar parâmetros e variáveis ambientais para diferentes instâncias de aplicação.

[Configure as políticas de segurança para a sua aplicação][12] descreve como executar serviços ao abrigo de políticas de segurança para restringir o acesso.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
