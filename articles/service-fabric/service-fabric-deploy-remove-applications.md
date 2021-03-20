---
title: Implantação de tecido de serviço Azure com PowerShell
description: Saiba como remover e implementar aplicações no Azure Service Fabric e como realizar estas ações em Powershell.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: d428a51c0bc224ca8706403ae176d46f1db82a32
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785452"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Implementar e remover aplicações usando PowerShell

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [APIs FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Uma vez embalado um tipo de [aplicação,][10]está pronto para ser implantado num cluster de tecido de serviço Azure. A implantação envolve os três passos seguintes:

1. Faça o upload do pacote de aplicações para a loja de imagens.
2. Registe o tipo de aplicação com o caminho relativo da loja de imagens.
3. Crie a aplicação.

Uma vez que a aplicação implementada deixou de ser necessária, pode eliminar a instância de aplicação e o seu tipo de aplicação. Para remover completamente uma aplicação do cluster envolve os seguintes passos:

1. Remova (ou elimine) a instância de aplicação de execução.
2. Desagregar o tipo de aplicação se já não precisar.
3. Retire o pacote de aplicações da loja de imagens.

Se utilizar o Visual Studio para implementar e depurar aplicações no seu cluster de desenvolvimento local, todos os passos anteriores são manuseados automaticamente através de um script PowerShell.  Este script encontra-se na pasta *Scripts* do projeto de candidatura. Este artigo fornece informações sobre o que esse script está a fazer para que possas realizar as mesmas operações fora do Visual Studio. 

Outra forma de implementar uma aplicação é utilizando a provisão externa. O pacote de aplicações pode ser [embalado `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) e enviado para uma loja externa. Neste caso, não é necessário fazer o upload para a loja de imagens. A implantação necessita dos seguintes passos:

1. Faça o upload `sfpkg` para uma loja externa. A loja externa pode ser qualquer loja que exponha um ponto final REST http ou https.
2. Registe o tipo de aplicação utilizando o URI de descarregamento externo e as informações do tipo de aplicação.
2. Crie a aplicação.

Para a limpeza, remova as instâncias de aplicação e não regisçar o tipo de aplicação. Como o pacote não foi copiado para a loja de imagens, não há local temporário para a limpeza. O fornecimento de loja externa está disponível a partir da versão 6.1 do Service Fabric.

>[!NOTE]
> O Visual Studio não suporta atualmente a oferta externa.

 

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Antes de executar quaisquer comandos PowerShell neste artigo, comece sempre a utilizar [o Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para ligar ao cluster de Tecido de Serviço. Para ligar ao cluster de desenvolvimento local, execute o seguinte:

```powershell
Connect-ServiceFabricCluster
```

Por exemplo, a ligação a um cluster ou cluster remoto protegido utilizando o Azure Ative Directory, os certificados X509 ou o Windows Ative Directory ver [Connect a um cluster seguro](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Faça o upload do pacote de aplicações

O upload do pacote de aplicações coloca-o num local acessível por componentes internos do Service Fabric.
Se pretender verificar o pacote de aplicações localmente, utilize o [cmdlet Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage)

O comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) envia o pacote de aplicações para a loja de imagens cluster.

Suponha que construa e embale uma aplicação chamada *MyApplication* in Visual Studio 2015. Por predefinição, o nome do tipo de aplicação listado no ApplicationManifest.xml é "MyApplicationType".  O pacote de aplicações, que contém o manifesto de aplicação necessário, manifestos de serviço e pacotes de código/config/dados, está localizado em *C:\Users \<username\> \Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

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

Se o pacote de aplicações for grande e/ou tiver muitos ficheiros, pode [comprimi-lo](service-fabric-package-apps.md#compress-a-package). A compressão reduz o tamanho e o número de ficheiros.
Isto resulta num registo mais rápido e não registo do tipo de aplicação. O tempo de upload pode ser mais lento atualmente, especialmente se incluir o tempo para comprimir o pacote. 

Para comprimir um pacote, utilize o mesmo comando [Copy-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) A compressão pode ser feita separada do upload, utilizando a `SkipCopy` bandeira ou juntamente com a operação de upload. Aplicar compressão num pacote comprimido não é op.
Para descomprimir um pacote comprimido, utilize o mesmo comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) com o `UncompressPackage` interruptor.

O cmdlet seguinte comprime a embalagem sem copiá-la para a loja de imagens. O pacote agora inclui ficheiros com fecho para o `Code` e `Config` pacotes. A aplicação e os manifestos de serviço não são fechados, pois são necessários para muitas operações internas (como partilha de pacotes, nome de tipo de aplicação e extração de versão para determinadas validações). Fechar os manifestos tornaria estas operações ineficientes.

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

Para pacotes de aplicações grandes, a compressão leva tempo. Para obter melhores resultados, utilize uma unidade SSD rápida. Os tempos de compressão e o tamanho do pacote comprimido também diferem com base no conteúdo da embalagem.
Por exemplo, aqui estão as estatísticas de compressão de alguns pacotes, que mostram o tamanho inicial e o tamanho do pacote comprimido, com o tempo de compressão.

|Tamanho inicial (MB)|Contagem de ficheiros|Tempo de compressão|Tamanho do pacote comprimido (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Uma vez que um pacote é comprimido, pode ser carregado para um ou vários clusters de Tecido de Serviço, conforme necessário. O mecanismo de implantação é o mesmo para os pacotes comprimidos e descomprimidos. As embalagens comprimidas são armazenadas como tal na loja de imagens de cluster. As embalagens não estão compactas no nó, antes da aplicação ser executada.


O exemplo a seguir envia o pacote para a loja de imagens, numa pasta chamada "MyApplicationV1":

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Se não especificar o parâmetro *-ApplicationPackagePathInImageStore,* o pacote de aplicações é copiado para a pasta "Debug" na loja de imagens.

>[!NOTE]
>**Copy-ServiceFabricApplicationPackage** detetará automaticamente a cadeia de ligação adequada à loja de imagens se a sessão PowerShell estiver ligada a um cluster de tecido de serviço. Para versões de Tecido de Serviço com mais de 5.6, o argumento **-ImageStoreConnectionString** deve ser explicitamente fornecido.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>O **cmdlet Get-ImageStoreConnectionStringFromClusterManifest,** que faz parte do módulo Service Fabric SDK PowerShell, é utilizado para obter a cadeia de ligação à loja de imagens.  Para importar o módulo SDK, corra:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Consulte [a cadeia de ligação da loja de imagens](service-fabric-image-store-connection-string.md) para obter informações suplementares sobre a cadeia de ligação à loja de imagens e à loja de imagens.
>
>
>

O tempo que leva para carregar um pacote difere dependendo de vários fatores. Alguns destes fatores são o número de ficheiros na embalagem, o tamanho do pacote e os tamanhos dos ficheiros. A velocidade de rede entre a máquina de origem e o cluster De Tecido de Serviço também impacta o tempo de upload. O tempo limite padrão para [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) é de 30 minutos.
Dependendo dos fatores descritos, poderá ter de aumentar o tempo limite. Se estiver a comprimir a embalagem na chamada de cópia, também tem de considerar o tempo de compressão.



## <a name="register-the-application-package"></a>Registar o pacote de candidaturas

O tipo de aplicação e a versão declarada no manifesto de aplicação ficam disponíveis para utilização quando o pacote de aplicação estiver registado. O sistema lê o pacote carregado no passo anterior, verifica o pacote, processa o conteúdo do pacote e copia o pacote processado para uma localização interna do sistema.  

Executar o [Registo-ServiçoFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) cmdlet para registar o tipo de aplicação no cluster e disponibilizá-lo para implementação:

### <a name="register-the-application-package-copied-to-image-store"></a>Registar o pacote de aplicações copiado para a loja de imagens

Quando uma embalagem foi previamente copiada para a loja de imagens, a operação de registo especifica o caminho relativo na loja de imagens.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

"MyApplicationV1" é a pasta na loja de imagens onde está localizado o pacote de aplicações. O tipo de aplicação com o nome "MyApplicationType" e a versão "1.0.0" (ambos encontram-se no manifesto de aplicação) está agora registado no cluster.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Registe o pacote de candidatura copiado para uma loja externa

A partir da versão 6.1 do Service Fabric, a provisão suporta o descarregamento do pacote a partir de uma loja externa. O download URI representa o caminho para o pacote de [ `sfpkg` aplicações](service-fabric-package-apps.md#create-an-sfpkg) a partir do local onde o pacote de aplicações pode ser descarregado usando protocolos HTTP ou HTTPS. A embalagem deve ter sido previamente enviada para este local externo. O URI deve permitir o acesso READ para que o Service Fabric possa descarregar o ficheiro. O `sfpkg` ficheiro deve ter a extensão ".sfpkg". A operação de fornecimento deve incluir as informações do tipo de aplicação, conforme encontrado no manifesto de aplicação.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

O comando [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) só regressa depois de o sistema ter registado com sucesso o pacote de aplicações. O tempo de inscrição depende do tamanho e do conteúdo do pacote de candidaturas. Se necessário, o parâmetro **-TimeoutSec** pode ser utilizado para fornecer um tempo limite mais longo (o tempo limite de tempo padrão é de 60 segundos).

Se tiver um grande pacote de aplicações ou se estiver a experimentar intervalos de tempo, utilize o parâmetro **-Async.** O comando retorna quando o cluster aceita o comando de registo. A operação de registo continua como necessário.
O comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) lista as versões do tipo de aplicação e o seu estado de registo. Pode utilizar este comando para determinar quando a inscrição é feita.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Remova um pacote de aplicações da loja de imagens

Se uma embalagem foi copiada para a loja de imagens, deve removê-la do local temporário após o registo da aplicação com sucesso. A eliminação de pacotes de aplicações da loja de imagens liberta recursos do sistema. Manter pacotes de aplicações não reutilizados consome armazenamento de discos e leva a problemas de desempenho da aplicação.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Criar a aplicação

Pode instantaneamente uma aplicação a partir de qualquer versão do tipo de aplicação que tenha sido registada com sucesso utilizando o [cmdlet New-ServiceFabricApplication.](/powershell/module/servicefabric/new-servicefabricapplication) O nome de cada pedido deve começar pelo esquema *"tecido:"* e deve ser único para cada instância de aplicação. São também criados quaisquer serviços predefinidos definidos no manifesto de aplicação do tipo de aplicação-alvo.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Podem ser criados vários casos de aplicação para qualquer versão de um tipo de aplicação registado. Cada instância de aplicação decorre isoladamente, com o seu próprio diretório de trabalho e processo.

Para ver quais aplicações e serviços nomeados estão a ser executados no cluster, executa os cmdlets [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) e [Get-ServiceFabricService:](/powershell/module/servicefabric/get-servicefabricservice)

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

Quando uma instância de aplicação já não é necessária, pode removê-la permanentemente pelo nome usando o [cmdlet Remove-ServiceFabricApplication.](/powershell/module/servicefabric/remove-servicefabricapplication) [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) remove automaticamente todos os serviços que pertencem à aplicação também, removendo permanentemente todo o estado de serviço. 

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

## <a name="unregister-an-application-type"></a>Não registar um tipo de aplicação

Quando uma versão específica de um tipo de aplicação já não for necessária, deve desmarcar o tipo de aplicação utilizando o cmdlet [Unregister-ServiceFabricApplicationType.](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) Os tipos de aplicações não utilizados não registados libertam espaço de armazenamento utilizado pela loja de imagens removendo os ficheiros do tipo de aplicação. A não inscrição de um tipo de aplicação não remove o pacote de aplicações copiado para a loja de imagens localidade temporária, se for utilizada cópia para a loja de imagens. Um tipo de aplicação pode não ser registado desde que nenhuma aplicação seja instantânea contra o mesmo e nenhuma atualização pendente da aplicação esteja a fazê-lo.

Executar [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) para ver os tipos de aplicação atualmente registados no cluster:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Executar [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) para desregistral de um tipo de aplicação específico:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage pede um ImageStoreConnectionString

O ambiente SDK do tecido de serviço já deve ter as predefinições corretas configuradas. Mas, se necessário, o ImageStoreConnectionString para todos os comandos deve corresponder ao valor que o cluster de Tecido de Serviço está a utilizar. Pode encontrar o ImageStoreConnectionString no manifesto de cluster, recuperado através dos comandos [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) e Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

O **cmdlet Get-ImageStoreConnectionStringFromClusterManifest,** que faz parte do módulo Service Fabric SDK PowerShell, é utilizado para obter a cadeia de ligação à loja de imagens.  Para importar o módulo SDK, corra:

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

Consulte [a cadeia de ligação da loja de imagens](service-fabric-image-store-connection-string.md) para obter informações suplementares sobre a cadeia de ligação à loja de imagens e à loja de imagens.

### <a name="deploy-large-application-package"></a>Implementar pacote de aplicações de grande porção

Emissão: [Copy-ServiceFabricApplicationComplicationSsem](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) horários para um grande pacote de aplicações (ordem de GB).
Experimente:
- Especifique um tempo limite maior para o comando [Copy-ServiceFabricApplicationPackage,](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) com `TimeoutSec` parâmetro. Por predefinição, o tempo limite é de 30 minutos.
- Verifique a ligação da rede entre a sua máquina de origem e o cluster. Se a ligação for lenta, considere utilizar uma máquina com uma melhor ligação de rede.
Se a máquina cliente estiver noutra região que não o cluster, considere usar uma máquina cliente numa região mais próxima ou igual ao cluster.
- Verifique se está a atingir o estrangulamento externo. Por exemplo, quando a loja de imagens estiver configurada para utilizar o armazenamento azul, o upload pode ser acelerado.

Problema: O pacote de upload foi concluído com sucesso, mas [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) times out. Tente:
- [Comprima a embalagem](service-fabric-package-apps.md#compress-a-package) antes de copiar para a loja de imagens.
A compressão reduz o tamanho e o número de ficheiros, o que, por sua vez, reduz a quantidade de tráfego e trabalho que o Service Fabric deve realizar. A operação de upload pode ser mais lenta (especialmente se incluir o tempo de compressão), mas registar e desregunar o tipo de aplicação é mais rápido.
- Especifique um tempo limite maior para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) com `TimeoutSec` parâmetro.
- Especificar `Async` o interruptor para [Registar-ServiçoFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype). O comando retorna quando o cluster aceita o comando e o registo do tipo de aplicação continua assíncronamente. Por esta razão, não há necessidade de especificar um prazo mais elevado neste caso. O comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) lista todas as versões de tipo de aplicação registadas com sucesso e o seu estado de registo. Pode utilizar este comando para determinar quando a inscrição é feita.

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

Problema: [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) times out for a application package with many files (porção de milhares).
Experimente:
- [Comprima a embalagem](service-fabric-package-apps.md#compress-a-package) antes de copiar para a loja de imagens. A compressão reduz o número de ficheiros.
- Especifique um tempo limite maior para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) com `TimeoutSec` parâmetro.
- Especificar `Async` o interruptor para [Registar-ServiçoFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype). O comando retorna quando o cluster aceita o comando e o registo do tipo de aplicação continua assíncronamente.
Por esta razão, não há necessidade de especificar um prazo mais elevado neste caso. O comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) lista todas as versões de tipo de aplicação registadas com sucesso e o seu estado de registo. Pode utilizar este comando para determinar quando a inscrição é feita.

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

[Atualização da aplicação do Tecido de Serviço](service-fabric-application-upgrade.md)

[Introdução da saúde do tecido de serviço](service-fabric-health-introduction.md)

[Diagnosticar e resolver problemas de um serviço de tecido de serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar uma aplicação em Tecido de Serviço](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
