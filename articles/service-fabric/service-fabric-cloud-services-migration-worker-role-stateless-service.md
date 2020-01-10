---
title: Converter aplicativos dos serviços de nuvem do Azure para Service Fabric
description: Este guia compara as funções Web e de trabalho dos serviços de nuvem e Service Fabric serviços sem monitoração de estado para ajudar a migrar dos serviços de nuvem para Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: caf067f793ca2086bc068907e86a82266627d128
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463337"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Guia para converter funções Web e de trabalho para Service Fabric serviços sem estado
Este artigo descreve como migrar suas funções Web e de trabalho dos serviços de nuvem para Service Fabric serviços sem monitoração de estado. Esse é o caminho de migração mais simples dos serviços de nuvem para Service Fabric para aplicativos cuja arquitetura geral permanecerá aproximadamente o mesmo.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projeto de serviço de nuvem para Service Fabric projeto de aplicativo
 Um projeto de serviço de nuvem e um projeto de aplicativo Service Fabric têm uma estrutura semelhante e ambos representam a unidade de implantação para seu aplicativo, ou seja, cada um deles define o pacote completo que é implantado para executar seu aplicativo. Um projeto de serviço de nuvem contém uma ou mais funções Web ou de trabalho. Da mesma forma, um projeto de aplicativo Service Fabric contém um ou mais serviços. 

A diferença é que o projeto de serviço de nuvem associa a implantação do aplicativo a uma implantação de VM e, portanto, contém definições de configuração de VM, enquanto o projeto de aplicativo Service Fabric define apenas um aplicativo que será implantado em um conjunto de VMs existentes em um Cluster Service Fabric. O próprio cluster de Service Fabric é implantado apenas uma vez, seja por meio de um modelo do Resource Manager ou por meio do portal do Azure, e vários aplicativos de Service Fabric podem ser implantados nele.

![Comparação de projetos de Service Fabric e serviços de nuvem][3]

## <a name="worker-role-to-stateless-service"></a>Função de trabalho para serviço sem estado
Conceitualmente, uma função de trabalhador representa uma carga de trabalho sem monitoração de estado, o que significa que cada instância de Workload é idêntica e as solicitações podem ser roteadas para qualquer instância a qualquer momento. Não se espera que cada instância se lembre da solicitação anterior. Estado em que a carga de trabalho Opera é gerenciada por um armazenamento de estado externo, como o armazenamento de tabelas do Azure ou Azure Cosmos DB. No Service Fabric, esse tipo de carga de trabalho é representado por um serviço sem estado. A abordagem mais simples para migrar uma função de trabalho para Service Fabric pode ser feita convertendo o código de função de trabalho em um serviço sem estado.

![Função de trabalho para serviço sem estado][4]

## <a name="web-role-to-stateless-service"></a>Função Web para serviço sem estado
Da mesma forma que a função de trabalho, uma função Web também representa uma carga sem estado e, portanto, conceitualmente também pode ser mapeada para um serviço sem estado Service Fabric. No entanto, ao contrário das funções Web, Service Fabric não oferece suporte ao IIS. A migração de um aplicativo Web de uma função Web para um serviço sem estado requer primeiro a movimentação para uma estrutura da Web que pode ser hospedada internamente e não depende do IIS ou System. Web, como ASP.NET Core 1.

| **Aplicação** | **Suportado** | **Caminho de migração** |
| --- | --- | --- |
| Web Forms do ASP.NET |Não |Converter em ASP.NET Core 1 MVC |
| ASP.NET MVC |Com a migração |Atualizar para o ASP.NET Core 1 MVC |
| ASP.NET Web API |Com a migração |Usar o servidor auto-hospedado ou o ASP.NET Core 1 |
| ASP.NET Core 1 |Sim |N/A |

## <a name="entry-point-api-and-lifecycle"></a>API de ponto de entrada e ciclo de vida
As APIs de função de trabalho e serviço de Service Fabric oferecem pontos de entrada semelhantes: 

| **Ponto de entrada** | **Função de trabalho** | **Serviço de Service Fabric** |
| --- | --- | --- |
| Em processamento |`Run()` |`RunAsync()` |
| Início da VM |`OnStart()` |N/A |
| Parada da VM |`OnStop()` |N/A |
| Abrir o ouvinte para solicitações de cliente |N/A |<ul><li> `CreateServiceInstanceListener()` para sem estado</li><li>`CreateServiceReplicaListener()` para monitoração de estado</li></ul> |

### <a name="worker-role"></a>Função de trabalho
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Service Fabric serviço sem estado
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Ambos têm uma substituição de "execução" primária na qual começar o processamento. Service Fabric serviços combinam `Run`, `Start`e `Stop` em um único ponto de entrada, `RunAsync`. Seu serviço deve começar a trabalhar quando `RunAsync` é iniciado e deve parar de funcionar quando o CancellationToken do método de `RunAsync` é sinalizado. 

Há várias diferenças importantes entre o ciclo de vida e o tempo de vida de funções de trabalho e serviços de Service Fabric:

* **Ciclo de vida:** A maior diferença é que uma função de trabalho é uma VM e, portanto, seu ciclo de vida está vinculado à VM, o que inclui eventos para quando a VM é iniciada e interrompida. Um serviço de Service Fabric tem um ciclo de vida separado do ciclo de vida da VM, portanto, não inclui eventos para quando a máquina virtual ou o computador host é iniciado e interrompido, pois eles não estão relacionados.
* **Tempo de vida:** Uma instância de função de trabalho será reciclada se o método de `Run` sair. O método `RunAsync` em um serviço Service Fabric, no entanto, pode ser executado até a conclusão e a instância do serviço permanecerá ativa. 

Service Fabric fornece um ponto de entrada de configuração de comunicação opcional para serviços que escutam solicitações de cliente. O ponto de entrada de RunAsync e de comunicação são substituições opcionais em serviços de Service Fabric-seu serviço pode optar por escutar apenas as solicitações do cliente ou apenas executar um loop de processamento, ou ambos, ou seja, por que o método RunAsync tem permissão para sair sem reinicialização a instância do serviço, porque ela pode continuar a escutar solicitações do cliente.

## <a name="application-api-and-environment"></a>API e ambiente de aplicativo
A API do ambiente de serviços de nuvem fornece informações e funcionalidade para a instância de VM atual, bem como informações sobre outras instâncias de função VM. Service Fabric fornece informações relacionadas ao seu tempo de execução e algumas informações sobre o nó em que um serviço está sendo executado no momento. 

| **Tarefa de ambiente** | **Serviços Cloud** | **Service Fabric** |
| --- | --- | --- |
| Definições de configuração e notificação de alteração |`RoleEnvironment` |`CodePackageActivationContext` |
| Armazenamento Local |`RoleEnvironment` |`CodePackageActivationContext` |
| Informações do ponto de extremidade |`RoleInstance` <ul><li>Instância atual: `RoleEnvironment.CurrentRoleInstance`</li><li>Outras funções e instância: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` para o endereço do nó atual</li><li>`FabricClient` e `ServicePartitionResolver` para descoberta de ponto de extremidade de serviço</li> |
| Emulação de ambiente |`RoleEnvironment.IsEmulated` |N/A |
| Evento de alteração simultânea |`RoleEnvironment` |N/A |

## <a name="configuration-settings"></a>Definições de configuração
As definições de configuração nos serviços de nuvem são definidas para uma função VM e se aplicam a todas as instâncias dessa função VM. Essas configurações são pares chave-valor definidos em arquivos de configuração. *. cscfg e podem ser acessadas diretamente por meio de RoleEnvironment. Em Service Fabric, as configurações se aplicam individualmente a cada serviço e a cada aplicativo, em vez de a uma VM, porque uma VM pode hospedar vários serviços e aplicativos. Um serviço é composto por três pacotes:

* **Código:** contém os executáveis de serviço, binários, DLLs e quaisquer outros arquivos que um serviço precisa executar.
* **Configuração:** todos os arquivos de configuração e configurações de um serviço.
* **Dados:** arquivos de dados estáticos associados ao serviço.

Cada um desses pacotes pode ser com controle de versão e atualização independentes. Semelhante aos serviços de nuvem, um pacote de configuração pode ser acessado programaticamente por meio de uma API e os eventos estão disponíveis para notificar o serviço de uma alteração de pacote de configuração. Um arquivo Settings. XML pode ser usado para configuração de chave-valor e acesso programático semelhante à seção de configurações do aplicativo de um arquivo app. config. No entanto, diferentemente dos serviços de nuvem, um pacote de configuração Service Fabric pode conter qualquer arquivo de configuração em qualquer formato, seja ele XML, JSON, YAML ou um formato binário personalizado. 

### <a name="accessing-configuration"></a>Acessando configuração
#### <a name="cloud-services"></a>Serviços em Nuvem
As definições de configuração de inconfiguration. *. cscfg podem ser acessadas por meio de `RoleEnvironment`. Essas configurações estão disponíveis globalmente para todas as instâncias de função na mesma implantação de serviço de nuvem.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Cada serviço tem seu próprio pacote de configuração individual. Não há nenhum mecanismo interno para definições de configuração globais acessíveis por todos os aplicativos em um cluster. Ao usar o arquivo de configuração especial Settings. XML do Service Fabric em um pacote de configuração, os valores em Settings. XML podem ser substituídos no nível do aplicativo, tornando possível as definições de configuração no nível do aplicativo.

As definições de configuração são acessados em cada instância de serviço por meio do `CodePackageActivationContext`do serviço.

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Eventos de atualização de configuração
#### <a name="cloud-services"></a>Serviços em Nuvem
O evento `RoleEnvironment.Changed` é usado para notificar todas as instâncias de função quando ocorre uma alteração no ambiente, como uma alteração de configuração. Isso é usado para consumir atualizações de configuração sem reciclar instâncias de função ou reiniciar um processo de trabalho.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Cada um dos três tipos de pacote em um código de serviço, configuração e dados-têm eventos que notificam uma instância de serviço quando um pacote é atualizado, adicionado ou removido. Um serviço pode conter vários pacotes de cada tipo. Por exemplo, um serviço pode ter vários pacotes de configuração, cada um com controle de versão individual e atualizável. 

Esses eventos estão disponíveis para consumir alterações em pacotes de serviço sem reiniciar a instância do serviço.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Tarefas de inicialização
As tarefas de inicialização são ações executadas antes do início de um aplicativo. Uma tarefa de inicialização normalmente é usada para executar scripts de instalação usando privilégios elevados. Os serviços de nuvem e Service Fabric oferecem suporte a tarefas de inicialização. A principal diferença é que nos serviços de nuvem, uma tarefa de inicialização está vinculada a uma VM porque faz parte de uma instância de função, enquanto que, em Service Fabric uma tarefa de inicialização está vinculada a um serviço, que não está vinculado a nenhuma VM específica.

| Service Fabric | Serviços em Nuvem |
| --- | --- |
| Local de configuração |ServiceDefinition.csdef |
| Privilégios |"limitado" ou "elevado" |
| Sequenciação |"simples", "plano de fundo", "primeiro plano" |

### <a name="cloud-services"></a>Serviços em Nuvem
Em serviços de nuvem, um ponto de entrada de inicialização é configurado por função em @ Definition. csdef. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
No Service Fabric um ponto de entrada de inicialização é configurado por serviço em Service manifest. xml:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>Uma observação sobre o ambiente de desenvolvimento
Os serviços de nuvem e os Service Fabric são integrados ao Visual Studio com modelos de projeto e suporte para depuração, configuração e implantação tanto localmente quanto no Azure. Os serviços de nuvem e Service Fabric também fornecem um ambiente de tempo de execução de desenvolvimento local. A diferença é que, embora o tempo de execução de desenvolvimento do serviço de nuvem eMule o ambiente do Azure em que ele é executado, Service Fabric não usa um emulador, ele usa o tempo de execução completo Service Fabric. O ambiente de Service Fabric que você executa em seu computador de desenvolvimento local é o mesmo ambiente executado em produção.

## <a name="next-steps"></a>Passos seguintes
Leia mais sobre Service Fabric Reliable Services e as diferenças fundamentais entre os serviços de nuvem e a arquitetura de aplicativo Service Fabric para entender como aproveitar o conjunto completo de recursos de Service Fabric.

* [Introdução ao Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Guia conceitual das diferenças entre os serviços de nuvem e Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
