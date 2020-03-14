---
title: Implantação de tecido de serviço Azure com PowerShell
description: Saiba mais sobre a remoção e implantação de aplicações no Tecido de Serviço Azure e como realizar estas ações na Powershell.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: e3fdd194f2949f1246e991968e02b3278f33f7db
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282513"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Implementar e remover aplicações usando powerShell

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [APIs FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Uma vez embalado um tipo de [aplicação,][10]está pronto para ser implantado num cluster Azure Service Fabric. A implantação envolve os seguintes três passos:

1. Faça o upload do pacote de aplicação para a loja de imagens.
2. Registe o tipo de aplicação com o caminho relativo da loja de imagens.
3. Crie a instância de aplicação.

Uma vez que a aplicação implementada já não seja necessária, pode eliminar a instância de aplicação e o seu tipo de aplicação. Remover completamente uma aplicação do cluster envolve os seguintes passos:

1. Remover (ou eliminar) a instância de aplicação em execução.
2. Desregilhe o tipo de inscrição se já não precisar.
3. Retire o pacote de aplicações da loja de imagens.

Se utilizar o Visual Studio para implementar e depurar aplicações no seu cluster de desenvolvimento local, todos os passos anteriores são manuseados automaticamente através de um script PowerShell.  Este script encontra-se na pasta *scripts* do projeto de aplicação. Este artigo fornece antecedentes sobre o que esse guião está a fazer para que possa realizar as mesmas operações fora do Estúdio Visual. 

Outra forma de implementar uma aplicação é utilizando a oferta externa. O pacote de aplicações pode ser [embalado como `sfpkg`](service-fabric-package-apps.md#create-an-sfpkg) e enviado para uma loja externa. Neste caso, o upload para a loja de imagens não é necessário. A implantação necessita dos seguintes passos:

1. Faça o upload do `sfpkg` para uma loja externa. A loja externa pode ser qualquer loja que exponha um REST http ou https endpoint.
2. Registe o tipo de aplicação utilizando o URI de descarregamento externo e as informações do tipo de aplicação.
2. Crie a instância de aplicação.

Para limpeza, remova as instâncias de aplicação e desregilhe o tipo de pedido. Como o pacote não foi copiado para a loja de imagens, não há local temporário para limpeza. O fornecimento de loja externa está disponível a partir da versão 6.1 do Service Fabric.

>[!NOTE]
> A Tualmente, a Visual Studio não suporta a oferta externa.

 

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Antes de executar quaisquer comandos PowerShell neste artigo, comece sempre por utilizar o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) para se ligar ao cluster De serviço. Para se ligar ao cluster de desenvolvimento local, execute o seguinte:

```powershell
Connect-ServiceFabricCluster
```

Por exemplo, a ligação a um cluster ou cluster remoto protegido utilizando o Diretório Ativo Azure, os certificados X509 ou o Windows Ative Directory ver [Connect a um cluster seguro](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Faça upload do pacote de candidaturas

O upload do pacote de aplicações coloca-o num local acessível por componentes internos do Tecido de Serviço.
Se pretender verificar o pacote de aplicações localmente, utilize o [Cmdlet Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)

O comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) envia o pacote de aplicações para a loja de imagens do cluster.

Suponha que construa e embale uma aplicação chamada *MyApplication* in Visual Studio 2015. Por predefinição, o nome do tipo de aplicação listado no ApplicationManifest.xml é "MyApplicationType".  O pacote de aplicações, que contém o manifesto de aplicação necessário, manifestos de serviço e pacotes de código/config/dados, está localizado em *C:\Utilizadores\<nome de utilizador\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

O seguinte comando lista o conteúdo do pacote de aplicações:

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

Se o pacote de aplicação for grande e/ou tiver muitos ficheiros, pode [comprimir.](service-fabric-package-apps.md#compress-a-package) A compressão reduz o tamanho e o número de ficheiros.
O efeito colateral é que registar e desregistar o tipo de aplicação é mais rápido. O tempo de upload pode ser mais lento atualmente, especialmente se incluir o tempo para comprimir a embalagem. 

Para comprimir uma embalagem, utilize o mesmo comando [Copy-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) A compressão pode ser feita separadamente do upload, utilizando a bandeira `SkipCopy` ou juntamente com a operação de upload. Aplicar compressão num pacote comprimido não é de si.
Para descomprimir uma embalagem comprimido, utilize o mesmo comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) com o interruptor `UncompressPackage`.

O seguinte cmdlet comprime a embalagem sem a copiar para a loja de imagens. O pacote inclui agora ficheiros com fecho para os pacotes `Code` e `Config`. A aplicação e os manifestos de serviço não são fechados, porque são necessários para muitas operações internas (como partilha de pacotes, nome do tipo de aplicação e extração de versão para determinadas validações). Fechar os manifestos tornaria estas operações ineficientes.

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

Para grandes pacotes de aplicação, a compressão leva tempo. Para obter os melhores resultados, utilize uma unidade SSD rápida. Os tempos de compressão e o tamanho do pacote comprimido também diferem com base no conteúdo do pacote.
Por exemplo, aqui estão as estatísticas de compressão de alguns pacotes, que mostram o tamanho inicial e o tamanho do pacote comprimido, com o tempo de compressão.

|Tamanho inicial (MB)|Contagem de ficheiros|Tempo de compressão|Tamanho do pacote comprimido (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Uma vez que um pacote é comprimido, pode ser enviado para um ou vários clusters de Tecido de Serviço, se necessário. O mecanismo de implantação é o mesmo para embalagens comprimidos e não comprimidos. As embalagens comprimidos são armazenadas como tal na loja de imagens cluster. As embalagens não são comprimidas no nó, antes de a aplicação ser executada.


O exemplo seguinte envia a embalagem para a loja de imagens, numa pasta chamada "MyApplicationV1":

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Se não especificar o parâmetro *-ApplicationPackagePathInImageStore,* o pacote de aplicações é copiado para a pasta "Debug" na loja de imagens.

>[!NOTE]
>**Copy-ServiceFabricApplicationPackage** detetará automaticamente a cadeia de ligação adequada à loja de imagens se a sessão PowerShell estiver ligada a um cluster de tecido de serviço. Para versões de Tecido de Serviço com idade superior a 5.6, o argumento **-ImageStoreConnectionString** deve ser explicitamente fornecido.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>O **Cmdlet Get-ImageStoreConnectionStringFromClusterManifest,** que faz parte do módulo PowerShell de tecido de serviço, é utilizado para obter a cadeia de ligação da loja de imagens.  Para importar o módulo SDK, corra:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Veja compreender a cadeia de [ligação da loja de imagens](service-fabric-image-store-connection-string.md) para obter informações suplementares sobre a loja de imagens e a cadeia de ligação da loja de imagens.
>
>
>

O tempo que demora a carregar um pacote difere dependendo de vários fatores. Alguns destes fatores são o número de ficheiros no pacote, o tamanho do pacote e os tamanhos dos ficheiros. A velocidade de rede entre a máquina de origem e o cluster De Tecido de Serviço também afeta o tempo de upload. O tempo de tempo padrão para [copy-serviceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) é de 30 minutos.
Dependendo dos fatores descritos, pode ter de aumentar o tempo de paragem. Se estiver a comprimir a embalagem na chamada de cópia, também tem de considerar o tempo de compressão.



## <a name="register-the-application-package"></a>Registe o pacote de candidaturas

O tipo de aplicação e a versão declaradas no manifesto de aplicação ficam disponíveis para utilização quando o pacote de candidatura sou registado. O sistema lê o pacote carregado na etapa anterior, verifica o pacote, processa o conteúdo do pacote e copia o pacote processado para uma localização interna do sistema.  

Executar o [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) cmdlet para registar o tipo de aplicação no cluster e disponibilizá-lo para implementação:

### <a name="register-the-application-package-copied-to-image-store"></a>Registe o pacote de aplicação copiado para a loja de imagens

Quando uma embalagem foi previamente copiada para a loja de imagens, a operação de registo especifica o caminho relativo na loja de imagens.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

"MyApplicationV1" é a pasta na loja de imagens onde está localizada a embalagem de aplicação. O tipo de aplicação com o nome "MyApplicationType" e a versão "1.0.0" (ambos encontram-se no manifesto de aplicação) está agora registado no cluster.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Registe o pacote de candidaturacopiado para uma loja externa

Começando pela versão 6.1 do Service Fabric, a provisão suporta o download do pacote a partir de uma loja externa. O download URI representa o caminho para o [pacote de aplicações`sfpkg`](service-fabric-package-apps.md#create-an-sfpkg) de onde o pacote de aplicações pode ser descarregado usando protocolos HTTP ou HTTPS. A embalagem deve ter sido previamente enviada para este local externo. O URI deve permitir o acesso READ para que o Service Fabric possa descarregar o ficheiro. O ficheiro `sfpkg` deve ter a extensão ".sfpkg". A operação de fornecimento deve incluir as informações do tipo de aplicação, tal como consta do manifesto de candidatura.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

O comando [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) só regressa depois de o sistema ter registado com sucesso o pacote de aplicações. O tempo de duração do registo depende do tamanho e conteúdo do pacote de candidaturas. Se necessário, o parâmetro **-TimeoutSec** pode ser utilizado para fornecer um tempo de tempo mais longo (o tempo de paragem padrão é de 60 segundos).

Se tiver um pacote de aplicação grande ou se estiver a ter intervalos, utilize o parâmetro **-Async.** O comando regressa quando o cluster aceita o comando do registo. A operação de registo continua se necessário.
O comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) lista as versões do tipo de aplicação e o seu estado de registo. Pode utilizar este comando para determinar quando o registo está feito.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Remova um pacote de aplicação da loja de imagens

Se uma embalagem foi copiada para a loja de imagens, deverá removê-la do local temporário após a inscrição da aplicação com sucesso. A apagar pacotes de aplicações da loja de imagens liberta recursos do sistema. Manter pacotes de aplicação não utilizados consome armazenamento de discos e leva a problemas de desempenho da aplicação.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Criar a aplicação

Pode instantaneamente uma aplicação a partir de qualquer versão do tipo de aplicação que tenha sido registada com sucesso utilizando o cmdlet [New-ServiceFabricApplication.](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) O nome de cada aplicação deve começar pelo esquema *"tecido:"* e deve ser único para cada instância de aplicação. São também criados quaisquer serviços predefinidos definidos no manifesto de aplicação do tipo de aplicação-alvo.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Várias instâncias de aplicação podem ser criadas para qualquer versão de um tipo de aplicação registado. Cada instância de candidatura funciona isoladamente, com o seu próprio diretório de trabalho e processo.

Para ver quais as aplicações e serviços nomeados que estão a funcionar no cluster, execute os cmdlets [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) e [Get-ServiceFabricService:](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps)

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

Quando uma instância de aplicação já não é necessária, pode removê-la permanentemente pelo nome utilizando o cmdlet [Remove-ServiceFabricApplication.](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) [Remover o ServiçoFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) remove automaticamente todos os serviços que pertencem à aplicação, removendo permanentemente todo o estado de serviço. 

> [!WARNING]
> Esta operação não pode ser revertida e o estado de aplicação não pode ser recuperado.

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

## <a name="unregister-an-application-type"></a>Não registar um tipo de candidatura

Quando uma versão específica de um tipo de aplicação já não é necessária, deve desregistar o tipo de aplicação utilizando o [cmdlet Unregister-ServiceFabricApplicationType.](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) O não registo dos tipos de aplicação não utilizados liberta o espaço de armazenamento utilizado pela loja de imagens removendo os ficheiros do tipo de aplicação. O desregisto de um tipo de aplicação não remove o pacote de aplicação copiado para a localização temporária da loja de imagens, se for utilizada cópia para a loja de imagens. Um tipo de aplicação pode não ser registado desde que nenhuma aplicação seja instantânea contra ele e nenhuma atualização pendente de aplicação esteja a referenciar- se.

Executar [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) para ver os tipos de aplicação atualmente registados no cluster:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Executar [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) para não registar um tipo de aplicação específico:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Resolução de Problemas

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage pede uma ImageStoreConnectionString

O ambiente SDK de tecido de serviço já deve ter as predefinições corretas. Mas se necessário, o ImageStoreConnectionString para todos os comandos deve corresponder ao valor que o cluster Service Fabric está a utilizar. Pode encontrar o ImageStoreConnectionString no manifesto do cluster, recuperado utilizando os comandos [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) e Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

O **Cmdlet Get-ImageStoreConnectionStringFromClusterManifest,** que faz parte do módulo PowerShell de tecido de serviço, é utilizado para obter a cadeia de ligação da loja de imagens.  Para importar o módulo SDK, corra:

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

Veja compreender a cadeia de [ligação da loja de imagens](service-fabric-image-store-connection-string.md) para obter informações suplementares sobre a loja de imagens e a cadeia de ligação da loja de imagens.

### <a name="deploy-large-application-package"></a>Implementar grande pacote de aplicações

Problema: [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) tem prazo para um pacote de aplicação grande (encomenda de GB).
Experimente:
- Especifique um intervalo maior para o comando [Copy-ServiceFabricApplicationPackage,](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) com `TimeoutSec` parâmetro. Por defeito, o tempo de paragem é de 30 minutos.
- Verifique a ligação de rede entre a sua máquina de origem e o cluster. Se a ligação for lenta, considere utilizar uma máquina com uma melhor ligação de rede.
Se a máquina cliente estiver noutra região que não o cluster, considere utilizar uma máquina cliente numa região mais próxima ou igual ao cluster.
- Verifique se está a bater no acelerador externo. Por exemplo, quando a loja de imagens estiver configurada para utilizar o armazenamento azul, o upload pode ser estrangulado.

Problema: Pacote de upload concluído com sucesso, mas [Registr-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) horários fora. Tente:
- [Comprima a embalagem](service-fabric-package-apps.md#compress-a-package) antes de copiar para a loja de imagens.
A compressão reduz o tamanho e o número de ficheiros, o que por sua vez reduz a quantidade de tráfego e trabalho que o Tecido de Serviço deve realizar. O funcionamento de upload pode ser mais lento (especialmente se incluir o tempo de compressão), mas registar e desregistar o tipo de aplicação é mais rápido.
- Especifique um intervalo maior para [o Register-ServiceFabricApplicationApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) com `TimeoutSec` parâmetro.
- Especifique `Async` interruptor para [o Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). O comando regressa quando o cluster aceita o comando e o registo do tipo de aplicação continua assíncrona. Por esta razão, não há necessidade de especificar um prazo mais elevado neste caso. O comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) lista todas as versões do tipo de aplicação registadas com sucesso e o seu estado de registo. Pode utilizar este comando para determinar quando o registo está feito.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Implementar pacote de aplicações com muitos ficheiros

Problema: [Registro-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) horários para um pacote de aplicação com muitos ficheiros (ordem de milhares).
Experimente:
- [Comprima a embalagem](service-fabric-package-apps.md#compress-a-package) antes de copiar para a loja de imagens. A compressão reduz o número de ficheiros.
- Especifique um intervalo maior para [o Register-ServiceFabricApplicationApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) com `TimeoutSec` parâmetro.
- Especifique `Async` interruptor para [o Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). O comando regressa quando o cluster aceita o comando e o registo do tipo de aplicação continua assíncrona.
Por esta razão, não há necessidade de especificar um prazo mais elevado neste caso. O comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) lista todas as versões do tipo de aplicação registadas com sucesso e o seu estado de registo. Pode utilizar este comando para determinar quando o registo está feito.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Passos Seguintes

[Criar pacote de uma aplicação](service-fabric-package-apps.md)

[Upgrade de aplicação de tecido de serviço](service-fabric-application-upgrade.md)

[Introdução à saúde do tecido de serviço](service-fabric-health-introduction.md)

[Diagnosticar e resolver problemas um serviço de Tecido de Serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelo de uma aplicação em Tecido de Serviço](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md