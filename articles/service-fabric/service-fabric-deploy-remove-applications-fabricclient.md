---
title: Implementação de aplicação do Service Fabric do Azure | Documentos da Microsoft
description: Utilize as APIs FabricClient para implementar e remover aplicações no Service Fabric.
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
ms.openlocfilehash: 9b3641ddd9d27c0ffa18e62f317d7a8c8ecb6eb3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834936"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Implantar e remover aplicativos usando FabricClient
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [APIs FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Uma vez um [empacotou do tipo de aplicação][10], ele está pronto para a implantação num cluster do Azure Service Fabric. Implantação envolve os seguintes três passos:

1. Carregar o pacote de aplicação para o armazenamento de imagens
2. Registar o tipo de aplicação
3. Remover o pacote de aplicação a partir do arquivo de imagem
4. Criar a instância da aplicação

Depois de uma aplicação é implementada e uma instância está em execução no cluster, pode eliminar a instância da aplicação e o respetivo tipo de aplicação. Para remover completamente uma aplicação do cluster envolve os seguintes passos:

1. Remover (ou eliminar) o executado instância da aplicação
2. Anular o registo do tipo de aplicação, se não precisa

Se utilizar o Visual Studio para a implantação e depuração de aplicativos no seu cluster de desenvolvimento local, todos os passos anteriores são processados automaticamente por meio de um script do PowerShell.  Este script está localizado na *Scripts* pasta do projeto de aplicativo. Este artigo fornece em segundo plano sobre o que esse script está fazendo, de modo a que possa efetuar as mesmas operações fora do Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Ligar ao cluster
Ligar ao cluster através da criação de um [FabricClient](/dotnet/api/system.fabric.fabricclient) instância antes de executar qualquer um dos exemplos de código neste artigo. Para obter exemplos de ligar a um cluster de desenvolvimento local ou um cluster remoto ou o cluster protegido com o Azure Active Directory, X509 certificados, ou consulte do Active Directory do Windows [ligar a um cluster seguro](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Para ligar ao cluster de desenvolvimento local, execute o seguinte:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Carregar o pacote de aplicação
Suponha que criar e empacotar um aplicativo chamado *MyApplication* no Visual Studio. Por predefinição, o nome do tipo de aplicativo listado no applicationmanifest. xml é "MyApplicationType".  O pacote de aplicação, que contém o manifesto do aplicativo necessário, manifestos de serviço e os pacotes de código/configuração/dados, está localizado em *C:\Users\&lt; nome de utilizador&gt;\Documents\Visual 2017\Projects\ do Studio MyApplication\MyApplication\pkg\Debug*.

Carregar o pacote de aplicação, coloca-o numa localização que esteja acessível para os componentes internos do Service Fabric. Service Fabric verifica se o pacote de aplicação durante o registo do pacote de aplicação. No entanto, se pretender verificar o pacote de aplicação localmente (ou seja, antes de carregar), utilize o [teste ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet.

O [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API carrega o pacote de aplicação para o armazenamento de imagens do cluster. 

Se o pacote de aplicação é grande e/ou tenha muitos ficheiros, pode [comprimi-los](service-fabric-package-apps.md#compress-a-package) e copie-o para o armazenamento de imagens com o PowerShell. A compressão reduz o tamanho e o número de ficheiros.

Ver [entender a cadeia de ligação do arquivo de imagem](service-fabric-image-store-connection-string.md) para informações suplementares sobre o armazenamento de imagens e a imagem de armazenamento da cadeia de ligação.

## <a name="register-the-application-package"></a>Registre-se o pacote de aplicação
O tipo de aplicação e a versão declarados no manifesto do aplicativo fica disponível para utilização quando o pacote de aplicação está registado. O sistema lê o pacote seja carregado no passo anterior, verifica se o pacote, processa o conteúdo do pacote e copia o pacote processado para uma localização de sistema interno.  

O [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) registradores de API, o aplicativo escreva no cluster e disponibilizá-la para implementação.

O [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API fornece informações sobre todos os tipos de aplicação registada com êxito. Pode utilizar esta API para determinar quando o registo é concluído.

## <a name="remove-an-application-package-from-the-image-store"></a>Remover um pacote de aplicação a partir do arquivo de imagem
É recomendado que remova o pacote de aplicação depois do aplicativo for registado com êxito.  A eliminação de pacotes de aplicação do arquivo de imagens liberta os recursos do sistema.  Manter os pacotes de aplicações não utilizadas consome armazenamento em disco e leva a problemas de desempenho do aplicativo. Eliminar o pacote de aplicação a partir da loja de imagem com o [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API.

## <a name="create-an-application-instance"></a>Criar uma instância de aplicação
Pode criar uma instância de uma aplicação a partir de qualquer tipo de aplicação que foi registado com êxito utilizando a [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API. O nome de cada aplicativo tem de começar com o *"recursos de infraestrutura:"* esquema e tem de ser exclusivo para cada instância de aplicação (dentro de um cluster). Também são criados quaisquer serviços padrão definidos no manifesto do aplicativo do tipo de aplicação de destino.

Várias instâncias de aplicações podem ser criadas em qualquer versão específica de um tipo de aplicação registada. Cada instância da aplicação é executada isoladamente, com seu próprio diretório de trabalho e o conjunto de processos.

Para ver que aplicações e serviços estão em execução no cluster, execute o [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) e [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) APIs.

## <a name="create-a-service-instance"></a>Criar uma instância de serviço
Pode criar uma instância de um serviço a partir de um tipo de serviço com o [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API.  Se o serviço está declarado como um serviço predefinido no manifesto do aplicativo, o serviço for instanciado quando o aplicativo é instanciado.  Chamar o [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API para um serviço que já é instanciado retornará uma exceção de tipo FabricException contendo um código de erro com um valor de FabricErrorCode.ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Remover uma instância de serviço
Quando já não é necessária uma instância de serviço, pode removê-lo a execução de instância da aplicação ao chamar o [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API.  

> [!WARNING]
> Esta operação não pode ser invertida e não é possível recuperar o estado do serviço.

## <a name="remove-an-application-instance"></a>Remover uma instância de aplicação
Quando uma instância de aplicação já não for necessário, pode removê-lo permanentemente com o nome da [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automaticamente remove todos os serviços que pertencem à aplicação também, de forma permanente a remoção de todos os Estados de serviço.

> [!WARNING]
> Esta operação não pode ser invertida e não é possível recuperar o estado da aplicação.

## <a name="unregister-an-application-type"></a>Anular o registo de um tipo de aplicação
Quando já não é necessária uma versão específica de um tipo de aplicação, deverá anular o registo dessa versão específica do tipo de aplicação com o [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API. A anulação do registo não utilizadas versões dos tipos de aplicativos libera espaço de armazenamento utilizado pelo arquivo de imagem. Uma versão de um tipo de aplicação pode ser anulado o registo, desde que não existem aplicações são instanciadas em relação a essa versão do tipo de aplicação e não atualizações pendentes de aplicações estão a referenciar essa versão do tipo de aplicação.

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
Problema: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API exceder o tempo limite para um pacote de aplicações grandes (ordem de GB).
Experimente:
- Especifique o tempo limite para [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) método, com `timeout` parâmetro. Por predefinição, o tempo limite é de 30 minutos.
- Verifique a ligação de rede entre o computador de origem e o cluster. Se a conexão estiver lenta, considere a utilização de uma máquina com uma ligação de rede melhor.
Se o computador cliente estiver em outra região que o cluster, considere a utilização de uma máquina de cliente numa região perto ou mesmo que o cluster.
- Verifique se está a atingir limitação externo. Por exemplo, quando o arquivo de imagens está configurado para utilizar o armazenamento do azure, pode ser limitado carregamento.

Problema: Carregar o pacote foi concluída com êxito, mas [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API exceder o tempo limite. Experimente:
- [Comprimir pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o arquivo de imagem.
A compressão reduz o tamanho e o número de ficheiros, que por sua vez, reduz a quantidade de tráfego e que o Service Fabric de trabalho tem de efetuar. A operação de carregamento pode ser mais lenta (especialmente se incluir a hora de compressão), mas são mais rápidos, registre-se e anular o registo do tipo de aplicação.
- Especifique o tempo limite para [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API com `timeout` parâmetro.

### <a name="deploy-application-package-with-many-files"></a>Implementar o pacote de aplicações com muitos ficheiros
Problema: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) exceder o tempo limite para um pacote de aplicação com muitos ficheiros (ordem de milhares).
Experimente:
- [Comprimir pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o arquivo de imagem. A compressão reduz o número de ficheiros.
- Especifique o tempo limite para [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) com `timeout` parâmetro.

## <a name="code-example"></a>Exemplo de código
O exemplo seguinte copia um pacote de aplicação para o arquivo de imagem, Aprovisiona o tipo de aplicação, cria uma instância de aplicação, cria uma instância de serviço, remove a instância da aplicação, anular Aprovisiona o tipo de aplicação e elimina o pacote de aplicações a partir do arquivo de imagem.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2017\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Passos Seguintes
[Atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md)

[Introdução de estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)

[Diagnosticar e resolver problemas com um serviço do Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar uma aplicação no Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
