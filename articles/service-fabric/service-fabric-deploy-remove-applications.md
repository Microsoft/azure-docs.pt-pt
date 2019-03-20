---
title: Implementação de aplicação do Service Fabric do Azure | Documentos da Microsoft
description: Como implementar e remover aplicações no Service Fabric com o PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: dbfbbd38a24d4f82ef7fd09ce57b87fb5e5327dc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880811"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Implementar e remover aplicações com o PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [APIs FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Uma vez um [empacotou do tipo de aplicação][10], ele está pronto para a implantação num cluster do Azure Service Fabric. Implantação envolve os seguintes três passos:

1. Carregar o pacote de aplicação para o armazenamento de imagens.
2. Registe o tipo de aplicação com o caminho relativo do arquivo de imagem.
3. Crie a instância da aplicação.

Depois da aplicação implementada já não é necessária, pode eliminar a instância da aplicação e o respetivo tipo de aplicação. Para remover completamente uma aplicação do cluster envolve os seguintes passos:

1. Remover (ou eliminar) o executado instância da aplicação.
2. Anular o registo do tipo de aplicação se já não precisam dele.
3. Remova o pacote de aplicação do arquivo de imagem.

Se utilizar o Visual Studio para a implantação e depuração de aplicativos no seu cluster de desenvolvimento local, todos os passos anteriores são processados automaticamente por meio de um script do PowerShell.  Este script está localizado na *Scripts* pasta do projeto de aplicativo. Este artigo fornece em segundo plano sobre o que esse script está fazendo, de modo a que possa efetuar as mesmas operações fora do Visual Studio. 

Outra forma de implementar uma aplicação é usar a aprovisionar externo. O pacote de aplicação pode ser [empacotados como `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) e carregados para um armazenamento externo. Neste caso, o carregamento para o arquivo de imagem não é necessária. Implementação tem os seguintes passos:

1. Carregar o `sfpkg` um armazenamento externo. O arquivo externo pode ser qualquer armazenamento que expõe um ponto final de http ou https REST.
2. Registe o tipo de aplicação com o URI de transferência externo e as informações de tipo de aplicação.
2. Crie a instância da aplicação.

Para a limpeza, remova as instâncias da aplicação e anular o registo do tipo de aplicação. Uma vez que o pacote não foi copiado para o armazenamento de imagens, não existe uma localização temporária para a limpeza. Aprovisionamento a partir do arquivo externo está disponível a partir de versão 6.1 do Service Fabric.

>[!NOTE]
> Visual Studio não suporta atualmente aprovisionar externo.

 
## <a name="connect-to-the-cluster"></a>Ligar ao cluster
Antes de executar qualquer comando do PowerShell neste artigo, sempre comece usando [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) para ligar ao cluster do Service Fabric. Para ligar ao cluster de desenvolvimento local, execute o seguinte:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Para obter exemplos de ligar a um cluster remoto ou o cluster protegido com o Azure Active Directory, X509 certificados, ou consulte do Active Directory do Windows [ligar a um cluster seguro](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Carregar o pacote de aplicação
Carregar o pacote de aplicação, coloca-o numa localização que seja acessível aos componentes internos do Service Fabric.
Se pretender verificar o pacote de aplicação localmente, utilize o [teste ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet.

O [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) comando carrega o pacote de aplicação para o armazenamento de imagens do cluster.

Suponha que criar e empacotar um aplicativo chamado *MyApplication* no Visual Studio 2015. Por predefinição, o nome do tipo de aplicativo listado no applicationmanifest. xml é "MyApplicationType".  O pacote de aplicação, que contém o manifesto do aplicativo necessário, manifestos de serviço e os pacotes de código/configuração/dados, está localizado em *C:\Users\<nome de utilizador\>\Documents\Visual 2015\Projects\ do Studio MyApplication\MyApplication\pkg\Debug*. 

O comando seguinte lista o conteúdo do pacote de aplicação:

```powershell
PS C:\> $path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Se o pacote de aplicação é grande e/ou tenha muitos ficheiros, pode [comprimi-los](service-fabric-package-apps.md#compress-a-package). A compressão reduz o tamanho e o número de ficheiros.
O efeito é que o registo e anular o registo do tipo de aplicação são mais rápidos. Tempo de carregamento poderá ser mais lento atualmente, especialmente se incluir o tempo para comprimir o pacote. 

Para compactar um pacote, utilize o mesmo [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) comando. Compressão pode ser feita separado desde o carregamento, ao utilizar o `SkipCopy` sinalizador, ou em conjunto com a operação de carregamento. Aplicação da compactação num pacote comprimido é sem operações.
Para descomprimir um pacote comprimido, utilize o mesmo [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) comando com o `UncompressPackage` mudar.

O seguinte cmdlet compacta o pacote sem copiá-lo para o arquivo de imagem. Agora, o pacote inclui arquivos compactados para o `Code` e `Config` pacotes. A aplicação e manifestos de serviço não são comprimidos, porque eles são necessários para várias operações internas (como a partilha de nome e versão extração do tipo para determinados validações do pacote). Comprimir os manifestos seria tornar essas operações ineficiente.

```
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

Para pacotes de aplicações grande, a compressão demorada. Para obter melhores resultados, utilize uma unidade SSD rápida. As horas de compactação e o tamanho do pacote comprimido também diferem com base no conteúdo do pacote.
Por exemplo, Eis as estatísticas de compressão de alguns pacotes, o que mostram inicial e o tamanho do pacote comprimida, com a hora de compressão.

|Tamanho inicial (MB)|Contagem de ficheiros|Tempo de compressão|Tamanho do pacote comprimida (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Depois de um pacote é compactado, ele pode ser carregado para um ou vários clusters do Service Fabric conforme necessário. O mecanismo de implantação é a mesmo para pacotes comprimidos e descomprimidos. Pacotes compactados são armazenados como tal no armazenamento de imagens do cluster. Os pacotes são descomprimidos no nó, antes do aplicativo é executado.


O exemplo seguinte carrega o pacote para o arquivo de imagens, para uma pasta com o nome "MyApplicationV1":

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Se não especificar a *- ApplicationPackagePathInImageStore* parâmetro, o pacote de aplicação é copiado para a pasta "Debug" no arquivo de imagem.

>[!NOTE]
>**Cópia ServiceFabricApplicationPackage** irá detetar automaticamente a cadeia de ligação do arquivo de imagem apropriada se a sessão do PowerShell está ligada a um cluster do Service Fabric. Para versões de Service Fabric com mais de 5.6, o **- ImageStoreConnectionString** argumento deve ser fornecido explicitamente.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>O **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet, o que faz parte do módulo do PowerShell do Service Fabric SDK, é utilizado para obter a cadeia de ligação do arquivo de imagem.  Para importar o módulo SDK, execute:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Ver [entender a cadeia de ligação do arquivo de imagem](service-fabric-image-store-connection-string.md) para informações suplementares sobre o armazenamento de imagens e a imagem de armazenamento da cadeia de ligação.
>
>
>

O tempo que demora a carregar um pacote difere consoante vários fatores. Alguns desses fatores são o número de ficheiros no pacote, o tamanho do pacote e o tamanho dos arquivos. A velocidade da rede entre o computador de origem e o cluster do Service Fabric também afeta o tempo de carregamento. O tempo limite predefinido para [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) é de 30 minutos.
Dependendo dos fatores descritos, poderá ter de aumentar o tempo limite. Se estiver a compressão do pacote na chamada de cópia, é preciso também considerar o tempo de compressão.



## <a name="register-the-application-package"></a>Registre-se o pacote de aplicação
O tipo de aplicação e a versão declarados no manifesto do aplicativo fica disponível para utilização quando o pacote de aplicação está registado. O sistema lê o pacote seja carregado no passo anterior, verifica se o pacote, processa o conteúdo do pacote e copia o pacote processado para uma localização de sistema interno.  

Executar o [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) cmdlet para registar o tipo de aplicação no cluster e disponibilizá-la para a implementação:

### <a name="register-the-application-package-copied-to-image-store"></a>Registre-se o pacote de aplicação copiado para armazenamento de imagens
Quando um pacote foi anteriormente copiado para o arquivo de imagem, a operação de registo Especifica o caminho relativo no arquivo de imagem.

```powershell
PS C:\> Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" é a pasta no arquivo de imagem, onde está localizado o pacote de aplicação. O tipo de aplicação com o nome "MyApplicationType" e versão "1.0.0" (ambos forem encontrados no manifesto do aplicativo) agora está registado no cluster.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Registar o pacote de aplicação copiado para um armazenamento externo
A partir da versão 6.1 do Service Fabric, Aprovisione suporta transferir o pacote a partir de um armazenamento externo. O download URI representa o caminho para o [ `sfpkg` pacote de aplicação](service-fabric-package-apps.md#create-an-sfpkg) de onde o pacote de aplicação pode ser baixado usando protocolos HTTP ou HTTPS. O pacote tem ter sido carregado anteriormente para esta localização externa. O URI tem de permitir acesso de leitura para que o Service Fabric, pode transferir o ficheiro. O `sfpkg` ficheiro tem de ter a extensão ". sfpkg". A operação de aprovisionamento deve incluir as informações de tipo de aplicação, como encontrada no manifesto do aplicativo.

```
PS C:\> Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

O [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) comando devolve apenas depois do sistema registou com êxito o pacote de aplicação. O tempo que leva de registo depende do tamanho e conteúdo do pacote do aplicativo. Se for necessário, o **- TimeoutSec** parâmetro pode ser utilizado para fornecer um tempo de mais tempo limite (o tempo limite predefinido é de 60 segundos).

Se tiver um aplicativo grande de pacotes ou se estiver a sofrer tempos limite, utilize o **- Async** parâmetro. O comando devolve quando o cluster aceita o comando de Registro. A operação de registo continua conforme necessário.
O [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) comando lista as versões do tipo de aplicativo e o respetivo estado de registo. Pode utilizar este comando para determinar quando o registo é concluído.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Remover um pacote de aplicação a partir do arquivo de imagem
Se um pacote foi copiado para o armazenamento de imagens, deve removê-lo da localização temporária depois do aplicativo for registado com êxito. A eliminação de pacotes de aplicação do arquivo de imagens liberta os recursos do sistema. Manter os pacotes de aplicações não utilizadas consome armazenamento em disco e leva a problemas de desempenho do aplicativo.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Criar a aplicação
Pode criar uma instância de uma aplicação a partir de qualquer versão do tipo de aplicação que foi registado com êxito utilizando a [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet. O nome de cada aplicativo tem de começar com o *"recursos de infraestrutura:"* esquema e tem de ser exclusivo para cada instância da aplicação. Também são criados quaisquer serviços padrão definidos no manifesto do aplicativo do tipo de aplicação de destino.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Várias instâncias de aplicações podem ser criadas em qualquer versão específica de um tipo de aplicação registada. Cada instância da aplicação é executada isoladamente, com o seu próprio diretório de trabalho e o processo.

Para ver que aplicações e serviços estão em execução no cluster, execute o [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) e [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) cmdlets:

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Remover uma aplicação
Quando uma instância de aplicação já não for necessário, pode removê-lo permanentemente com o nome da [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) cmdlet. [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automaticamente remove todos os serviços que pertencem à aplicação também, de forma permanente a remoção de todos os Estados de serviço. 

> [!WARNING]
> Esta operação não pode ser invertida e não é possível recuperar o estado da aplicação.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Anular o registo de um tipo de aplicação
Quando já não é necessária uma versão específica de um tipo de aplicação, deve anular o registo do tipo de aplicação com o [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) cmdlet. Anular o registo de tipos de aplicações não utilizadas libera espaço de armazenamento utilizado pelo arquivo de imagem, removendo os arquivos do tipo de aplicação. Anular o registo de um tipo de aplicação não remove o pacote de aplicação copiado para a localização temporária do arquivo de imagem, se a cópia para o arquivo de imagem foi utilizada. Um tipo de aplicação pode ser anulado o registo, desde que não existem aplicações são instanciadas em relação a ele e não pendentes aplicação atualizações estão fazendo referência a ele.

Execute [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) para ver os tipos de aplicação atualmente registados no cluster:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Execute [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) ao anular o registo de um tipo de aplicação específica:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Cópia ServiceFabricApplicationPackage pede-lhe um ImageStoreConnectionString
O ambiente do SDK do Service Fabric já deverá ter os padrões corretos de configurar. Mas se for necessário, ImageStoreConnectionString para todos os comandos deve corresponder ao valor que o cluster do Service Fabric está a utilizar. Pode encontrar o ImageStoreConnectionString no manifesto do cluster, obtido com o [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) e Get-ImageStoreConnectionStringFromClusterManifest comandos:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

O **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet, o que faz parte do módulo do PowerShell do Service Fabric SDK, é utilizado para obter a cadeia de ligação do arquivo de imagem.  Para importar o módulo SDK, execute:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

O ImageStoreConnectionString encontra-se no manifesto do cluster:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Ver [entender a cadeia de ligação do arquivo de imagem](service-fabric-image-store-connection-string.md) para informações suplementares sobre o armazenamento de imagens e a imagem de armazenamento da cadeia de ligação.

### <a name="deploy-large-application-package"></a>Implementar o pacote de aplicações grande
Problema: [Cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) exceder o tempo limite para um pacote de aplicações grandes (ordem de GB).
Experimente:
- Especifique o tempo limite para [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) comando, com `TimeoutSec` parâmetro. Por predefinição, o tempo limite é de 30 minutos.
- Verifique a ligação de rede entre o computador de origem e o cluster. Se a conexão estiver lenta, considere a utilização de uma máquina com uma ligação de rede melhor.
Se o computador cliente estiver em outra região que o cluster, considere a utilização de uma máquina de cliente numa região perto ou mesmo que o cluster.
- Verifique se está a atingir limitação externo. Por exemplo, quando o arquivo de imagens está configurado para utilizar o armazenamento do azure, pode ser limitado carregamento.

Problema: Carregar o pacote foi concluída com êxito, mas [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) exceder o tempo limite. Experimente:
- [Comprimir pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o arquivo de imagem.
A compressão reduz o tamanho e o número de ficheiros, que por sua vez, reduz a quantidade de tráfego e que o Service Fabric de trabalho tem de efetuar. A operação de carregamento pode ser mais lenta (especialmente se incluir a hora de compressão), mas são mais rápidos, registre-se e anular o registo do tipo de aplicação.
- Especifique o tempo limite para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) com `TimeoutSec` parâmetro.
- Especifique `Async` mude para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). O comando devolve quando o cluster aceita o comando e o registo do tipo de aplicação continua de modo assíncrono. Por esse motivo, não é necessário especificar um tempo limite superior neste caso. O [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) comando apresenta uma lista de todas as versões de tipo de aplicação registada com êxito e o respetivo estado de registo. Pode utilizar este comando para determinar quando o registo é concluído.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Implementar o pacote de aplicações com muitos ficheiros
Problema: [Registre-se-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) exceder o tempo limite para um pacote de aplicação com muitos ficheiros (ordem de milhares).
Experimente:
- [Comprimir pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o arquivo de imagem. A compressão reduz o número de ficheiros.
- Especifique o tempo limite para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) com `TimeoutSec` parâmetro.
- Especifique `Async` mude para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). O comando devolve quando o cluster aceita o comando e o registo do tipo de aplicação continua de modo assíncrono.
Por esse motivo, não é necessário especificar um tempo limite superior neste caso. O [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) comando apresenta uma lista de todas as versões de tipo de aplicação registada com êxito e o respetivo estado de registo. Pode utilizar este comando para determinar quando o registo é concluído.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Passos Seguintes
[Criar pacote de uma aplicação](service-fabric-package-apps.md)

[Atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md)

[Introdução de estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)

[Diagnosticar e resolver problemas com um serviço do Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar uma aplicação no Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
