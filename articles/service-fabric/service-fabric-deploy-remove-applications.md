---
title: Implantação de Service Fabric do Azure com o PowerShell
description: Como implantar e remover aplicativos no Service Fabric usando o PowerShell.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: atsenthi
ms.openlocfilehash: 0080ba0807a4cb31fedeb132932e2e08137dd40b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013280"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Implantar e remover aplicativos usando o PowerShell

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [APIs FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Depois que um [tipo de aplicativo tiver sido empacotado][10], ele estará pronto para implantação em um cluster de Service Fabric do Azure. A implantação envolve as três etapas a seguir:

1. Carregue o pacote de aplicativos no repositório de imagens.
2. Registre o tipo de aplicativo com o caminho relativo do repositório de imagens.
3. Crie a instância do aplicativo.

Depois que o aplicativo implantado não for mais necessário, você poderá excluir a instância do aplicativo e seu tipo de aplicativo. Para remover completamente um aplicativo do cluster, você envolve as seguintes etapas:

1. Remova (ou exclua) a instância do aplicativo em execução.
2. Cancele o registro do tipo de aplicativo se você não precisar mais dele.
3. Remova o pacote de aplicativos do repositório de imagens.

Se você usar o Visual Studio para implantar e depurar aplicativos no cluster de desenvolvimento local, todas as etapas anteriores serão tratadas automaticamente por meio de um script do PowerShell.  Esse script é encontrado na pasta *scripts* do projeto de aplicativo. Este artigo fornece informações sobre o que o script está fazendo para que você possa executar as mesmas operações fora do Visual Studio. 

Outra maneira de implantar um aplicativo é usando o provisionamento externo. O pacote de aplicativos pode ser [empacotado como `sfpkg`](service-fabric-package-apps.md#create-an-sfpkg) e carregado em um repositório externo. Nesse caso, o carregamento para o repositório de imagens não é necessário. A implantação precisa das seguintes etapas:

1. Carregue o `sfpkg` em um repositório externo. O repositório externo pode ser qualquer armazenamento que expõe um ponto de extremidade http ou HTTPS REST.
2. Registre o tipo de aplicativo usando o URI de download externo e as informações de tipo de aplicativo.
2. Crie a instância do aplicativo.

Para limpeza, remova as instâncias do aplicativo e cancele o registro do tipo de aplicativo. Como o pacote não foi copiado para o repositório de imagens, não há local temporário para limpeza. O provisionamento do repositório externo está disponível a partir do Service Fabric versão 6,1.

>[!NOTE]
> No momento, o Visual Studio não dá suporte ao provisionamento externo.

 

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Antes de executar qualquer comando do PowerShell neste artigo, sempre comece usando [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) para se conectar ao cluster de Service Fabric. Para se conectar ao cluster de desenvolvimento local, execute o seguinte:

```powershell
Connect-ServiceFabricCluster
```

Para obter exemplos de como se conectar a um cluster remoto ou protegido usando Azure Active Directory, certificados X509 ou o Windows Active Directory consulte [conectar-se a um cluster seguro](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Carregar o pacote de aplicativos

O carregamento do pacote de aplicativos o coloca em um local acessível por componentes internos do Service Fabric.
Se você quiser verificar o pacote de aplicativos localmente, use o cmdlet [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) .

O comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) carrega o pacote de aplicativos no repositório de imagens do cluster.

Suponha que você crie e empacote um aplicativo chamado *MyApplication* no Visual Studio 2015. Por padrão, o nome do tipo de aplicativo listado no ApplicationManifest. xml é "myapplicationtype".  O pacote de aplicativos, que contém o manifesto do aplicativo, os manifestos do serviço e os pacotes de código/configuração/dados necessários, está localizado em *C:\Users\<username\>\Documents\Visual Studio 2015 \ Projects\MyApplication\MyApplication\pkg\Debug*. 

O comando a seguir lista o conteúdo do pacote de aplicativos:

```powershell
$path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
tree /f $path
```

```Output
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

Se o pacote de aplicativos for grande e/ou tiver muitos arquivos, você poderá [compactá-lo](service-fabric-package-apps.md#compress-a-package). A compactação reduz o tamanho e o número de arquivos.
O efeito colateral é que o registro e o cancelamento do registro do tipo de aplicativo são mais rápidos. O tempo de carregamento pode ser mais lento no momento, especialmente se você incluir o tempo para compactar o pacote. 

Para compactar um pacote, use o mesmo comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) . A compactação pode ser feita separadamente do carregamento, usando o sinalizador `SkipCopy` ou junto com a operação de upload. Aplicar a compactação em um pacote compactado é não operacional.
Para descompactar um pacote compactado, use o mesmo comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) com a opção `UncompressPackage`.

O cmdlet a seguir compacta o pacote sem copiá-lo para o repositório de imagens. O pacote agora inclui arquivos compactados para os pacotes `Code` e `Config`. O aplicativo e os manifestos do serviço não são compactados, pois são necessários para muitas operações internas (como compartilhamento de pacotes, extração de versão e nome de tipo de aplicativo para determinadas validações). O descompactar os manifestos tornaria essas operações ineficientes.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
tree /f $path
```

```Output
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

Para pacotes de aplicativos grandes, a compactação leva tempo. Para obter melhores resultados, use uma unidade SSD rápida. Os tempos de compactação e o tamanho do pacote compactado também diferem com base no conteúdo do pacote.
Por exemplo, aqui estão as estatísticas de compactação para alguns pacotes, que mostram o tamanho do pacote inicial e o compactado, com o tempo de compactação.

|Tamanho inicial (MB)|Contagem de arquivos|Tempo de compactação|Tamanho do pacote compactado (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Depois que um pacote é compactado, ele pode ser carregado em um ou vários clusters Service Fabric, conforme necessário. O mecanismo de implantação é o mesmo para pacotes compactados e descompactados. Os pacotes compactados são armazenados como tal no repositório de imagens do cluster. Os pacotes são descompactados no nó, antes da execução do aplicativo.


O exemplo a seguir carrega o pacote no repositório de imagens, em uma pasta chamada "MyApplicationV1":

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Se você não especificar o parâmetro *-ApplicationPackagePathInImageStore* , o pacote de aplicativo será copiado para a pasta "debug" no repositório de imagens.

>[!NOTE]
>O **Copy-ServiceFabricApplicationPackage** detectará automaticamente a cadeia de conexão apropriada do repositório de imagens se a sessão do PowerShell estiver conectada a um Cluster Service Fabric. Para Service Fabric versões anteriores a 5,6, o argumento **-ImageStoreConnectionString** deve ser fornecido explicitamente.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>O cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , que faz parte do módulo Service Fabric SDK do PowerShell, é usado para obter a cadeia de conexão do repositório de imagens.  Para importar o módulo SDK, execute:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Consulte [entender a cadeia de conexão do repositório de imagens](service-fabric-image-store-connection-string.md) para obter informações complementares sobre o repositório de imagens e a cadeia de conexão do repositório de imagens.
>
>
>

O tempo necessário para carregar um pacote é diferente dependendo de vários fatores. Alguns desses fatores são o número de arquivos no pacote, o tamanho do pacote e os tamanhos de arquivo. A velocidade de rede entre o computador de origem e o Service Fabric cluster também afeta o tempo de carregamento. O tempo limite padrão para [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) é de 30 minutos.
Dependendo dos fatores descritos, talvez seja necessário aumentar o tempo limite. Se você estiver compactando o pacote na chamada de cópia, também precisará considerar o tempo de compactação.



## <a name="register-the-application-package"></a>Registrar o pacote de aplicativos

O tipo de aplicativo e a versão declarados no manifesto do aplicativo tornam-se disponíveis para uso quando o pacote de aplicativos é registrado. O sistema lê o pacote carregado na etapa anterior, verifica o pacote, processa o conteúdo do pacote e copia o pacote processado para um local interno do sistema.  

Execute o cmdlet [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) para registrar o tipo de aplicativo no cluster e torná-lo disponível para implantação:

### <a name="register-the-application-package-copied-to-image-store"></a>Registrar o pacote de aplicativos copiado no repositório de imagens

Quando um pacote foi copiado anteriormente para o repositório de imagens, a operação de registro especifica o caminho relativo no repositório de imagens.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

"MyApplicationV1" é a pasta no repositório de imagens onde o pacote de aplicativos está localizado. O tipo de aplicativo com o nome "myapplicationtype" e a versão "1.0.0" (ambos são encontrados no manifesto do aplicativo) agora está registrado no cluster.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Registrar o pacote de aplicativos copiado em um repositório externo

A partir do Service Fabric versão 6,1, o provisionamento oferece suporte ao download do pacote de um repositório externo. O URI de download representa o caminho para o [pacote de aplicativos`sfpkg`](service-fabric-package-apps.md#create-an-sfpkg) de onde o pacote de aplicativos pode ser baixado usando protocolos http ou HTTPS. O pacote deve ter sido previamente carregado nesse local externo. O URI deve permitir acesso de leitura para que Service Fabric possa baixar o arquivo. O arquivo de `sfpkg` deve ter a extensão ". sfpkg". A operação de provisionamento deve incluir as informações de tipo de aplicativo, conforme encontradas no manifesto do aplicativo.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

O comando [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) retorna somente depois que o sistema registrou com êxito o pacote de aplicativos. O tempo que o registro leva depende do tamanho e do conteúdo do pacote de aplicativos. Se necessário, o parâmetro **-TimeoutSec** pode ser usado para fornecer um tempo limite maior (o tempo limite padrão é de 60 segundos).

Se você tiver um pacote de aplicativo grande ou se estiver experimentando tempos limite, use o parâmetro **-Async** . O comando retorna quando o cluster aceita o comando Register. A operação de registro continua conforme necessário.
O comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) lista as versões de tipo de aplicativo e seu status de registro. Você pode usar esse comando para determinar quando o registro é feito.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Remover um pacote de aplicativos do repositório de imagens

Se um pacote tiver sido copiado para o repositório de imagens, você deverá removê-lo do local temporário depois que o aplicativo for registrado com êxito. A exclusão de pacotes de aplicativos do repositório de imagens libera os recursos do sistema. Manter pacotes de aplicativos não utilizados consome armazenamento em disco e leva a problemas de desempenho de aplicativos.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Criar a aplicação

Você pode criar uma instância de um aplicativo de qualquer versão de tipo de aplicativo registrada com êxito usando o cmdlet [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) . O nome de cada aplicativo deve começar com o esquema *"Fabric:"* e deve ser exclusivo para cada instância do aplicativo. Todos os serviços padrão definidos no manifesto do aplicativo do tipo de aplicativo de destino também são criados.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Várias instâncias de aplicativo podem ser criadas para qualquer versão específica de um tipo de aplicativo registrado. Cada instância do aplicativo é executada isoladamente, com seu próprio diretório de trabalho e processo.

Para ver quais aplicativos e serviços nomeados estão em execução no cluster, execute os cmdlets [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) e [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) :

```powershell
Get-ServiceFabricApplication  
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}
```

```powershell
Get-ServiceFabricApplication | Get-ServiceFabricService
```

```Output
ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Remover uma aplicação

Quando uma instância do aplicativo não é mais necessária, você pode removê-la permanentemente pelo nome usando o cmdlet [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) . Remove [-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) remove automaticamente todos os serviços que pertencem ao aplicativo, removendo permanentemente todos os Estados de serviço. 

> [!WARNING]
> Esta operação não pode ser revertida e o estado do aplicativo não pode ser recuperado.

```powershell
Remove-ServiceFabricApplication fabric:/MyApp
```

```Output
Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded
```

```powershell
Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Cancelar o registro de um tipo de aplicativo

Quando uma versão específica de um tipo de aplicativo não é mais necessária, você deve cancelar o registro do tipo de aplicativo usando o cmdlet [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) . O cancelamento do registro de tipos de aplicativos não utilizados libera o espaço de armazenamento usado pelo repositório de imagens removendo os arquivos do tipo de aplicativo. O cancelamento do registro de um tipo de aplicativo não remove o pacote de aplicativos copiado para o local temporário do repositório de imagens, se a cópia para o repositório de imagens foi usada. Um tipo de aplicativo pode ter seu registro cancelado, desde que nenhum aplicativo seja instanciado em relação a ele e que nenhuma atualização de aplicativos pendente faça referência a ele.

Execute [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) para ver os tipos de aplicativos atualmente registrados no cluster:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Execute [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) para cancelar o registro de um tipo de aplicativo específico:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage solicita um ImageStoreConnectionString

O ambiente SDK do Service Fabric já deve ter os padrões corretos configurados. Mas, se necessário, o ImageStoreConnectionString para todos os comandos deve corresponder ao valor que o Cluster Service Fabric está usando. Você pode encontrar o ImageStoreConnectionString no manifesto do cluster, recuperado usando os comandos [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) e Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

O cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , que faz parte do módulo Service Fabric SDK do PowerShell, é usado para obter a cadeia de conexão do repositório de imagens.  Para importar o módulo SDK, execute:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

O ImageStoreConnectionString é encontrado no manifesto do cluster:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Consulte [entender a cadeia de conexão do repositório de imagens](service-fabric-image-store-connection-string.md) para obter informações complementares sobre o repositório de imagens e a cadeia de conexão do repositório de imagens.

### <a name="deploy-large-application-package"></a>Implantar pacote de aplicativo grande

Problema: [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) expira para um pacote de aplicativo grande (ordem de GB).
Experimente:
- Especifique um tempo limite maior para o comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) , com `TimeoutSec` parâmetro. Por padrão, o tempo limite é de 30 minutos.
- Verifique a conexão de rede entre o computador de origem e o cluster. Se a conexão estiver lenta, considere usar um computador com uma conexão de rede melhor.
Se o computador cliente estiver em outra região do que o cluster, considere usar um computador cliente em uma região mais próxima ou igual à do cluster.
- Verifique se você está atingindo a limitação externa. Por exemplo, quando o repositório de imagens é configurado para usar o armazenamento do Azure, o upload pode ser limitado.

Problema: o pacote de carregamento foi concluído com êxito, mas [o Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) expira. Tente
- [Compacte o pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o repositório de imagens.
A compactação reduz o tamanho e o número de arquivos, o que, por sua vez, reduz a quantidade de tráfego e o trabalho que o Service Fabric deve executar. A operação de upload pode ser mais lenta (especialmente se você incluir o tempo de compactação), mas o registro e o cancelamento do registro do tipo de aplicativo são mais rápidos.
- Especifique um tempo limite maior para [o registro-ServiceFabricApplicationType com o](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) parâmetro `TimeoutSec`.
- Especifique `Async` switch para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). O comando retorna quando o cluster aceita o comando e o registro do tipo de aplicativo continua de forma assíncrona. Por esse motivo, não há necessidade de especificar um tempo limite maior nesse caso. O comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) lista todas as versões de tipo de aplicativo registradas com êxito e seu status de registro. Você pode usar esse comando para determinar quando o registro é feito.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Implantar pacote de aplicativos com muitos arquivos

Problema: [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) expira para um pacote de aplicativos com muitos arquivos (ordem de milhares).
Experimente:
- [Compacte o pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o repositório de imagens. A compactação reduz o número de arquivos.
- Especifique um tempo limite maior para [o registro-ServiceFabricApplicationType com o](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) parâmetro `TimeoutSec`.
- Especifique `Async` switch para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). O comando retorna quando o cluster aceita o comando e o registro do tipo de aplicativo continua de forma assíncrona.
Por esse motivo, não há necessidade de especificar um tempo limite maior nesse caso. O comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) lista todas as versões de tipo de aplicativo registradas com êxito e seu status de registro. Você pode usar esse comando para determinar quando o registro é feito.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Passos seguintes

[Criar pacote de uma aplicação](service-fabric-package-apps.md)

[Atualização do aplicativo Service Fabric](service-fabric-application-upgrade.md)

[Introdução à integridade Service Fabric](service-fabric-health-introduction.md)

[Diagnosticar e solucionar problemas de um serviço de Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar um aplicativo no Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md