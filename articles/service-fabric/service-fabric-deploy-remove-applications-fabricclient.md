---
title: Implantação de tecido de serviço Azure com FabricClient
description: Utilize as APIs doNcims do Tecido para implantar e remover aplicações em Tecido de Serviço.
ms.topic: conceptual
ms.date: 01/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 343a37c983b1d64a4b1986913d9d6fd648a113fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785554"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Implementar e remover aplicações usando o FabricClient
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [APIs FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Uma vez embalado um tipo de [aplicação,][10]está pronto para ser implantado num cluster de tecido de serviço Azure. A implantação envolve os três passos seguintes:

1. Faça o upload do pacote de aplicações para a loja de imagens
2. Registar o tipo de candidatura
3. Remova o pacote de aplicações da loja de imagens
4. Criar a instância de aplicação

Depois de implementar uma aplicação e executar um caso no cluster, pode eliminar a instância de aplicação e o seu tipo de aplicação. Retire completamente uma aplicação do cluster seguindo estes passos:

1. Remover (ou eliminar) a instância de aplicação em execução
2. Desregralar o tipo de aplicação se já não precisar

Se utilizar o Visual Studio para implementar e depurar aplicações no seu cluster de desenvolvimento local, todos os passos anteriores são manuseados automaticamente através de um script PowerShell.  Este script encontra-se na pasta *Scripts* do projeto de candidatura. Este artigo fornece informações sobre o que esse script está a fazer para que possas fazer as mesmas operações fora do Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Ligar ao cluster
Ligue-se ao cluster criando uma instância [FabricClient](/dotnet/api/system.fabric.fabricclient) antes de executar qualquer um dos exemplos de código neste artigo. Por exemplo, a ligação a um cluster de desenvolvimento local ou a um cluster ou cluster remoto protegido utilizando o Azure Ative Directory, os certificados X509 ou o Windows Ative Directory ver [Connect a um cluster seguro](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Para ligar ao cluster de desenvolvimento local, corra o seguinte exemplo:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Faça o upload do pacote de aplicações
Suponha que construa e embale uma aplicação chamada *MyApplication* in Visual Studio. Por predefinição, o nome do tipo de aplicação listado no ApplicationManifest.xml é "MyApplicationType".  O pacote de aplicações, que contém o manifesto de aplicação necessário, manifestos de serviço e pacotes de código/config/dados, está localizado em *C:\Users \& lt;username &gt; \Documents\Visual Studio 2019\Projects\MyApplication\MyApplication\pkg\Debug*.

O upload do pacote de aplicações coloca-o num local acessível pelos componentes internos do Service Fabric. A Service Fabric verifica o pacote de pedidos durante o registo do pacote de candidaturas. No entanto, se pretender verificar localmente o pacote de aplicações (isto é, antes de carregar), utilize o [cmdlet Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage)

A [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API envia o pacote de aplicações para a loja de imagem cluster. 

Se o pacote de aplicações for grande e/ou tiver muitos ficheiros, pode [comprimi-lo](service-fabric-package-apps.md#compress-a-package) e copiá-lo para a loja de imagens utilizando o PowerShell. A compressão reduz o tamanho e o número de ficheiros.

Consulte [a cadeia de ligação da loja de imagens](service-fabric-image-store-connection-string.md) para obter informações suplementares sobre a cadeia de ligação à loja de imagens e à loja de imagens.

## <a name="register-the-application-package"></a>Registar o pacote de candidaturas
O tipo de aplicação e a versão declarada no manifesto de aplicação ficam disponíveis para utilização quando o pacote de aplicação estiver registado. O sistema lê o pacote carregado no passo anterior, verifica o pacote, processa o conteúdo do pacote e copia o pacote processado para uma localização interna do sistema.  

A [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API regista o tipo de aplicação no cluster e disponibiliza-o para implementação.

A [API GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) fornece informações sobre todos os tipos de aplicações registados com sucesso. Pode utilizar esta API para determinar quando o registo é feito.

## <a name="remove-an-application-package-from-the-image-store"></a>Remova um pacote de aplicações da loja de imagens
Recomenda-se que remova o pacote de aplicações após o registo da aplicação com sucesso.  A eliminação de pacotes de aplicações da loja de imagens liberta recursos do sistema.  Manter pacotes de aplicações não reutilizados consome armazenamento de discos e leva a problemas de desempenho da aplicação. Elimine o pacote de aplicações da loja de imagens utilizando a [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API.

## <a name="create-an-application-instance"></a>Criar uma instância de aplicação
Pode instantaneamente uma aplicação a partir de qualquer tipo de aplicação que tenha sido registada com sucesso utilizando a [API CreateApplicationAsync.](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) O nome de cada pedido deve começar pelo esquema *"tecido:"* e deve ser único para cada instância de aplicação (dentro de um cluster). São também criados quaisquer serviços predefinidos definidos no manifesto de aplicação do tipo de aplicação-alvo.

Podem ser criados vários casos de aplicação para qualquer versão de um tipo de aplicação registado. Cada instância de aplicação decorre isoladamente, com o seu próprio diretório de trabalho e conjunto de processos.

Para ver quais aplicações e serviços nomeados estão a ser executados no cluster, executa as APIs [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) e [GetServiceListAsync.](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)

## <a name="create-a-service-instance"></a>Criar uma instância de serviço
Pode instantaneamente um serviço a partir de um tipo de serviço utilizando a [API CreateServiceAsync.](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync)  Se o serviço for declarado como um serviço predefinido no manifesto de aplicação, o serviço é instantâneo quando a aplicação é instantânea.  Chamar a [API CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) para um serviço que já está instantâneo devolverá uma exceção do tipo FabricException. A exceção conterá um código de erro com o valor de FabricErrorCode.ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Remover uma instância de serviço
Quando uma instância de serviço já não é necessária, pode removê-la da aplicação em execução, chamando a API [deleteServiceAsync.](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync)  

> [!WARNING]
> Esta operação não pode ser revertida e o estado de serviço não pode ser recuperado.

## <a name="remove-an-application-instance"></a>Remover uma instância de aplicação
Quando uma instância de aplicação já não é necessária, pode removê-la permanentemente pelo nome usando a [API DeleteApplicationAsync.](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) remove automaticamente todos os serviços que pertencem à aplicação também, removendo permanentemente todo o estado de serviço.

> [!WARNING]
> Esta operação não pode ser revertida e o estado de aplicação não pode ser recuperado.

## <a name="unregister-an-application-type"></a>Não registar um tipo de aplicação
Quando uma versão específica de um tipo de aplicação já não for necessária, deve desmarcar essa versão específica do tipo de aplicação utilizando o [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API. Versões não utilizadas não utilizadas dos tipos de aplicações liberta espaço de armazenamento utilizado pela loja de imagens. Uma versão de um tipo de aplicação pode ser não registada desde que nenhuma aplicação seja instantânea contra essa versão do tipo de aplicação. Além disso, o tipo de aplicação não pode ter atualizações pendentes de aplicação referendo-se a essa versão do tipo de aplicação.

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage pede um ImageStoreConnectionString
O ambiente SDK do tecido de serviço já deve ter as predefinições corretas configuradas. Mas, se necessário, o ImageStoreConnectionString para todos os comandos deve corresponder ao valor que o cluster de Tecido de Serviço está a utilizar. Pode encontrar o ImageStoreConnectionString no manifesto de cluster, recuperado através dos comandos [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) e Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
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
Emissão: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API times out for a large application package (porção de GB).
Experimente:
- Especifique um tempo limite maior para o método [CopyApplicationPackage,](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) com `timeout` parâmetro. Por predefinição, o tempo limite é de 30 minutos.
- Verifique a ligação da rede entre a sua máquina de origem e o cluster. Se a ligação for lenta, considere utilizar uma máquina com uma melhor ligação de rede.
Se a máquina cliente estiver noutra região que não o cluster, considere usar uma máquina cliente numa região mais próxima ou igual ao cluster.
- Verifique se está a atingir o estrangulamento externo. Por exemplo, quando a loja de imagens estiver configurada para utilizar o armazenamento azul, o upload pode ser acelerado.

Problema: O pacote de upload foi concluído com sucesso, mas [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API times out. Tente:
- [Comprima a embalagem](service-fabric-package-apps.md#compress-a-package) antes de copiar para a loja de imagens.
A compressão reduz o tamanho e o número de ficheiros, o que, por sua vez, reduz a quantidade de tráfego e trabalho que o Service Fabric deve realizar. A operação de upload pode ser mais lenta (especialmente se incluir o tempo de compressão), mas registar e não registar o tipo de aplicação é mais rápido.
- Especifique um tempo limite maior para [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API com `timeout` parâmetro.

### <a name="deploy-application-package-with-many-files"></a>Implementar pacote de aplicações com muitos ficheiros
Problema: [ProvisionApplicationAssasa](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) vezes para um pacote de aplicações com muitos ficheiros (ordem de milhares).
Experimente:
- [Comprima a embalagem](service-fabric-package-apps.md#compress-a-package) antes de copiar para a loja de imagens. A compressão reduz o número de ficheiros.
- Especifique um tempo limite maior para [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) com `timeout` parâmetro.

## <a name="code-example"></a>Exemplo de código
O exemplo a seguir copia um pacote de aplicações para a loja de imagens e fornece o tipo de aplicação. Em seguida, o exemplo cria uma instância de aplicação e cria uma instância de serviço. Por fim, o exemplo remove a instância da aplicação, desmente o tipo de aplicação e elimina o pacote de aplicações da loja de imagens.

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
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
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

## <a name="next-steps"></a>Passos seguintes
[Atualização da aplicação do Tecido de Serviço](service-fabric-application-upgrade.md)

[Introdução da saúde do tecido de serviço](service-fabric-health-introduction.md)

[Diagnosticar e resolver problemas de um serviço de tecido de serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar uma aplicação em Tecido de Serviço](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
