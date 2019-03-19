---
title: Converter o Azure Cloud dos serviços de aplicações para o Service Fabric | Documentos da Microsoft
description: Este guia compara sem monitoração de estado dos serviços Web de serviços Cloud e funções de trabalho e o Service Fabric para o ajudar a migrar dos serviços Cloud para o Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 5880ebb3-8b54-4be8-af4b-95a1bc082603
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: f23f29d15c4c8f05551b20d42b92dda5632cde08
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58078742"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Guia de conversão de Web e funções de trabalho para serviços sem monitoração de estado do Service Fabric
Este artigo descreve como migrar a sua Cloud dos serviços Web e funções de trabalho para serviços sem monitoração de estado do Service Fabric. Este é o mais simples caminho de migração dos serviços Cloud para o Service Fabric para aplicativos cuja arquitetura geral vai manter-se mais ou menos o mesmo.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projeto de serviço em nuvem ao projeto de aplicação do Service Fabric
 Um projeto de serviço em nuvem e um projeto de aplicação do Service Fabric têm uma estrutura semelhante e ambos representam a unidade de implementação para a sua aplicação - ou seja, eles cada define o pacote completo que é implementado para executar seu aplicativo. Um projeto de serviço Cloud contém um ou mais funções de trabalho ou Web. Da mesma forma, um projeto de aplicação do Service Fabric contém um ou mais serviços. 

A diferença é que o projeto de serviço em nuvem vincula a implementação da aplicação com uma implementação de VM e, portanto, contém definições de configuração de VM, ao passo que o projeto de aplicação do Service Fabric define apenas um aplicativo que será implementado num conjunto de VMs existentes no cluster do Service Fabric. O próprio cluster do Service Fabric apenas é implementado uma vez, por meio de um modelo do Resource Manager ou através do portal do Azure, e várias aplicações do Service Fabric podem ser implementadas no mesmo.

![Comparação de projeto do Service Fabric e serviços em nuvem][3]

## <a name="worker-role-to-stateless-service"></a>Função de trabalho para o serviço sem estado
Conceitualmente, uma função de trabalho representa uma carga de trabalho sem monitorização de estado, que significa que todas as instâncias da carga de trabalho é idêntica e pedidos podem ser roteados para qualquer instância em qualquer altura. Cada instância não é esperada que lembre-se o pedido anterior. Estado de que a carga de trabalho opera em é gerido por um armazenamento de estado externo, como armazenamento de tabelas do Azure ou Azure Document DB. No Service Fabric, esse tipo de carga de trabalho é representado por um serviço sem estado. A abordagem mais simples para migrar uma função de trabalho para o Service Fabric pode ser feita ao converter o código de função de trabalho para um serviço sem estado.

![Função de trabalho para o serviço sem estado][4]

## <a name="web-role-to-stateless-service"></a>Função da Web para o serviço sem estado
Semelhante à função de trabalho, uma função da Web também representa uma carga de trabalho sem monitorização de estado e, assim, conceitualmente que também pode ser mapeado para um serviço sem estado do Service Fabric. No entanto, ao contrário das funções da Web, Service Fabric não suporta o IIS. Para migrar uma web aplicação a partir de uma função da Web para um serviço sem estado necessita de mover primeiro para uma estrutura web que pode ser autoalojada e não depende de IIS ou System. Web, como o ASP.NET Core 1.

| **Aplicação** | **Suportado** | **Caminho de migração** |
| --- | --- | --- |
| Web Forms do ASP.NET |Não |Converter em MVC do ASP.NET Core 1 |
| ASP.NET MVC |Com a migração |Atualização para o ASP.NET Core 1 MVC |
| API Web ASP.NET |Com a migração |Utilizar o servidor de hospedagem interna ou ASP.NET Core 1 |
| ASP.NET Core 1 |Sim |N/A |

## <a name="entry-point-api-and-lifecycle"></a>API de ponto de entrada e o ciclo de vida
Pontos de entrada de semelhante de oferta APIs do serviço de função de trabalho e o Service Fabric: 

| **Ponto de entrada** | **Função de trabalho** | **Serviço do Service Fabric** |
| --- | --- | --- |
| Em processamento |`Run()` |`RunAsync()` |
| Iniciar VM |`OnStart()` |N/A |
| Parar VM |`OnStop()` |N/A |
| Serviço de escuta aberto para pedidos de cliente |N/A |<ul><li> `CreateServiceInstanceListener()` para sem monitoração de estado</li><li>`CreateServiceReplicaListener()` para com monitoração de estado</li></ul> |

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

### <a name="service-fabric-stateless-service"></a>Serviço sem estado do Service Fabric
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

Ambos têm uma substituição de "Executar" principal na qual pretende iniciar o processamento. Combinação de serviços do Service Fabric `Run`, `Start`, e `Stop` num ponto de entrada único, `RunAsync`. O serviço deve começar a trabalhar quando `RunAsync` é iniciado e deve parar de funcionar quando o `RunAsync` CancellationToken do método é sinalizado. 

Existem várias diferenças importantes entre o ciclo de vida e o tempo de vida dos serviços de funções de trabalho e o Service Fabric:

* **Ciclo de vida:** A maior diferença é que uma função de trabalho é uma VM e por isso, o seu ciclo de vida é associado à VM, que inclui eventos para quando a VM é iniciada e interrompida. Um serviço do Service Fabric tem um ciclo de vida é diferente do ciclo de vida VM, para que ele não inclui eventos para quando o anfitrião, VM ou máquina é iniciado e para, à medida que eles não estão relacionados.
* **Tempo de vida:** Uma instância de função de trabalho haverá reciclagem se o `Run` sai do método. O `RunAsync` método num serviço do Service Fabric no entanto pode executar até à conclusão e a instância do serviço irá manter-se. 

O Service Fabric fornece um ponto de entrada de configuração de comunicação opcional para os serviços de escuta de pedidos de cliente. O RunAsync e a comunicação de ponto de entrada são substituições opcionais em serviços do Service Fabric - seu serviço pode optar por apenas escutar para pedidos de cliente ou apenas executar um loop de processamento, ou ambos - razão pela qual o método RunAsync tem permissão para sair sem reiniciar a instância do serviço, porque ele pode continuar a escutar os pedidos de cliente.

## <a name="application-api-and-environment"></a>Aplicação API e o ambiente
O ambiente de serviços Cloud API fornece informações e funcionalidades para a instância VM atual, bem como informações sobre outras instâncias de função VM. O Service Fabric fornece informações relacionadas com o seu tempo de execução e algumas informações acerca do nó de um serviço estão em execução. 

| **Tarefas do ambiente** | **Serviços Cloud** | **Service Fabric** |
| --- | --- | --- |
| Definições de configuração e a notificação de alteração |`RoleEnvironment` |`CodePackageActivationContext` |
| Armazenamento Local |`RoleEnvironment` |`CodePackageActivationContext` |
| Informações do ponto final |`RoleInstance` <ul><li>Instância atual: `RoleEnvironment.CurrentRoleInstance`</li><li>Outras funções e a instância: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` para o endereço do nó atual</li><li>`FabricClient` e `ServicePartitionResolver` para deteção de pontos finais de serviço</li> |
| Emulação de ambiente |`RoleEnvironment.IsEmulated` |N/A |
| Evento de alteração simultâneas |`RoleEnvironment` |N/A |

## <a name="configuration-settings"></a>Definições de configuração
Definições de configuração nos serviços Cloud são definidas para uma função de VM e aplicam-se a todas as instâncias dessa função de VM. Estas definições são pares chave-valor definidas ServiceConfiguration.*.cscfg ficheiros e podem ser acessadas diretamente por meio de RoleEnvironment. No Service Fabric, as definições aplicam-se individualmente para cada serviço e para cada aplicativo, em vez de para uma VM, uma vez que uma VM pode alojar várias aplicações e serviços. Um serviço é composto por três pacotes:

* **Código:** contém os executáveis de serviço, binários, DLLs e outros ficheiros de um serviço precisa ser executado.
* **Configuração:** todos os ficheiros de configuração e definições para um serviço.
* **Dados:** associados ao serviço de ficheiros de dados estáticos.

Cada um desses pacotes pode ser uma versão e atualizar independentemente. Semelhante aos serviços em nuvem, um pacote de configuração pode ser acedido através de programação através de uma API e eventos estão disponíveis para notificar o serviço de uma alteração de pacote de configuração. Um arquivo Settings pode ser utilizado para a configuração de chave-valor e o acesso programático semelhante da secção definições da aplicação de um arquivo App. config. No entanto, ao contrário dos serviços Cloud, um pacote de configuração do Service Fabric pode conter quaisquer ficheiros de configuração em qualquer formato, quer se trate de um formato binário personalizado, YAML, JSON ou XML. 

### <a name="accessing-configuration"></a>Aceder à configuração
#### <a name="cloud-services"></a>Serviços Cloud
Definições de configuração dos ServiceConfiguration.*.cscfg podem ser acessadas `RoleEnvironment`. Estas definições são globalmente disponíveis para todas as instâncias de função na mesma implementação do serviço em nuvem.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Cada serviço tem seu próprio pacote de configuração individuais. Não existe nenhum mecanismo incorporado para definições de configuração global acessível por todos os aplicativos num cluster. Ao utilizar o especial Settings ficheiro de configuração Service Fabric dentro de um pacote de configuração, os valores em Settings podem ser substituídas no nível do aplicativo, possibilitando a definições de configuração de nível de aplicativo.

Definições de configuração são acessos dentro de cada instância de serviço através do serviço `CodePackageActivationContext`.

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
#### <a name="cloud-services"></a>Serviços Cloud
O `RoleEnvironment.Changed` eventos é utilizado para notificar de todas as instâncias de função quando ocorre uma alteração no ambiente, como uma alteração de configuração. Isso é usado para consumir as atualizações de configuração sem reciclar instâncias de função ou reiniciar um processo de trabalho.

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
Cada um dos tipos de três pacotes num serviço - código, configuração e dados, tem de eventos que uma instância do serviço de notificar quando um pacote é atualizado, adicionado ou removido. Um serviço pode conter vários pacotes de cada tipo. Por exemplo, um serviço pode ter vários pacotes de configuração, cada um individualmente com versão e é atualizável. 

Estes eventos estão disponíveis para consumir as alterações nos pacotes de serviço sem reiniciar a instância do serviço.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Tarefas de arranque
Tarefas de arranque são ações que são executadas antes de iniciar uma aplicação. Uma tarefa de arranque é normalmente utilizada para executar scripts de configuração utilizando privilégios elevados. Os serviços Cloud e o Service Fabric suportam tarefas de arranque. A principal diferença é que nos serviços Cloud, uma tarefa de arranque está ligada a uma VM porque faz parte de uma instância de função, enquanto que no Service Fabric está associada uma tarefa de arranque para um serviço, que não está associado a qualquer VM específica.

| Service Fabric | Serviços Cloud |
| --- | --- |
| Localização de configuração |ServiceDefinition.csdef |
| Privilégios |"limitada" ou "elevado" |
| Sequenciamento |"simple", "background", "foreground" |

### <a name="cloud-services"></a>Serviços Cloud
Nos serviços Cloud, um ponto de entrada de arranque está configurado por função no servicedefinition. Csdef. 

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
No Service Fabric, um ponto de entrada de arranque está configurado por serviço no servicemanifest. XML:

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
Os serviços Cloud e o Service Fabric estão integradas com o Visual Studio com modelos de projeto e suporte para depuração, configurar e implementar ambos localmente e para o Azure. Os serviços Cloud e o Service Fabric também fornecem um ambiente de tempo de execução de desenvolvimento local. A diferença é que, enquanto o tempo de execução de desenvolvimento do serviço em nuvem emula o ambiente do Azure no qual é executado, Service Fabric não utiliza um emulador – ele usa o tempo de execução completado do Service Fabric. O ambiente do Service Fabric que executar no seu computador de desenvolvimento local é o mesmo ambiente que é executado na produção.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o Service Fabric Reliable Services e as diferenças fundamentais entre serviços Cloud e a arquitetura de aplicação do Service Fabric para compreender como tirar partido do conjunto completo de funcionalidades do Service Fabric.

* [Introdução ao Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Guia conceitual para as diferenças entre serviços em nuvem e o Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
