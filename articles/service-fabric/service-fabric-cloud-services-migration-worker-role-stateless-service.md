---
title: Converter aplicativos Azure Cloud Services para o Tecido de Serviço
description: Este guia compara os serviços de Web e Worker Roles e Service Fabric apátridas para ajudar a migrar dos Serviços cloud para o Tecido de Serviço.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: cf593f793aabf2a0650684ed8d02fe02d756ec2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575742"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Guia para converter funções web e trabalhadoras para serviços apátridas de tecido de serviço
Este artigo descreve como migrar os seus serviços web e trabalhadores para serviços apátridas de tecido. Esta é a rota de migração mais simples dos Serviços de Cloud para o Tecido de Serviço para aplicações cuja arquitetura global vai permanecer aproximadamente a mesma.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projeto de Serviço de Nuvem para projeto de aplicação de Tecido de Serviço
 Um projeto de Cloud Service e um projeto de Aplicação de Tecido de Serviço têm uma estrutura semelhante e ambos representam a unidade de implantação para a sua aplicação - isto é, cada um define o pacote completo que é implementado para executar a sua aplicação. Um projeto de Cloud Service contém uma ou mais funções web ou de trabalhadores. Da mesma forma, um projeto de Aplicação de Tecido de Serviço contém um ou mais serviços. 

A diferença é que o projeto Cloud Service acoplia a implementação da aplicação com uma implementação VM e, portanto, contém configurações de configuração VM no mesmo, enquanto o projeto de Aplicação de Tecido de Serviço apenas define uma aplicação que será implantada para um conjunto de VMs existentes num cluster de Tecido de Serviço. O cluster de Tecido de Serviço em si só é implantado uma vez, seja através de um modelo de Gestor de Recursos ou através do portal Azure, e várias aplicações de Tecido de Serviço podem ser implementadas no mesmo.

![Comparação de projetos de tecido de serviço e cloud][3]

## <a name="worker-role-to-stateless-service"></a>Papel do Trabalhador ao serviço apátrida
Conceptualmente, um papel de trabalhador representa uma carga de trabalho apátrida, o que significa que cada instância da carga de trabalho é idêntica e os pedidos podem ser encaminhados para qualquer instância a qualquer momento. Não se espera que cada instância se lembre do pedido anterior. Indicar que a carga de trabalho opera é gerida por uma loja estatal externa, como o Azure Table Storage ou a Azure Cosmos DB. No Tecido de Serviço, este tipo de carga de trabalho é representado por um Serviço Apátrida. A abordagem mais simples para migrar um papel operário para o tecido de serviço pode ser feita através da conversão do código de função dos trabalhadores para um serviço apátrida.

![Papel do Trabalhador para o Serviço Apátrida][4]

## <a name="web-role-to-stateless-service"></a>Função Web para o serviço apátrida
Semelhante ao Papel dos Trabalhadores, um Papel Web também representa uma carga de trabalho apátrida, e por isso conceptualmente também pode ser mapeado para um serviço apátrida de Tecido de Serviço. No entanto, ao contrário das Funções Web, o Tecido de Serviço não suporta o IIS. Para migrar uma aplicação web de um Papel Web para um serviço apátrida requer primeiro a mudança para uma estrutura web que pode ser auto-hospedada e não depende do IIS ou System.Web, como ASP.NET Core 1.

| **Aplicação** | **Suportado** | **Caminho de migração** |
| --- | --- | --- |
| ASP.NET Formas Web |No |Converter para ASP.NET Core 1 MVC |
| ASP.NET MVC |Com a migração |Upgrade para ASP.NET Core 1 MVC |
| API Web ASP.NET |Com a migração |Utilize servidor auto-hospedado ou ASP.NET Core 1 |
| ASP.NET Core 1 |Yes |N/D |

## <a name="entry-point-api-and-lifecycle"></a>API ponto de entrada e ciclo de vida
Os APIs de serviço de função de trabalhador e de tecido de serviço oferecem pontos de entrada semelhantes: 

| **Ponto de entrada** | **Papel do Trabalhador** | **Serviço de tecido de serviço** |
| --- | --- | --- |
| Em processamento |`Run()` |`RunAsync()` |
| Início do VM |`OnStart()` |N/D |
| Paragem VM |`OnStop()` |N/D |
| Ouvinte aberto para pedidos de clientes |N/D |<ul><li> `CreateServiceInstanceListener()` para apátridas</li><li>`CreateServiceReplicaListener()` para imponente</li></ul> |

### <a name="worker-role"></a>Papel do Trabalhador
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

### <a name="service-fabric-stateless-service"></a>Serviço apátrida de tecido de serviço
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

Ambos têm uma sobreposição primária de "Run" para começar a processar. Os serviços de tecido de serviço `Run` combinam, e num único ponto de `Start` `Stop` entrada, `RunAsync` . O seu serviço deve começar a funcionar quando `RunAsync` começar e deve parar de funcionar quando o método `RunAsync` 'CancelamentoToken' estiver sinalizado. 

Existem várias diferenças fundamentais entre o ciclo de vida e a vida útil dos serviços de Funções de Trabalhador e Tecido de Serviço:

* **Ciclo de vida:** A maior diferença é que um Papel Operário é um VM e por isso o seu ciclo de vida está ligado ao VM, que inclui eventos para quando o VM começa e para. Um serviço de Tecido de Serviço tem um ciclo de vida separado do ciclo de vida VM, pelo que não inclui eventos para quando o VM ou máquina de anfitrião iniciar e parar, uma vez que não estão relacionados.
* **Vida útil:** Uma instância de função operária reciclá-se-á se o `Run` método sair. No `RunAsync` entanto, o método de um serviço de Tecido de Serviço pode ser concluído e a instância de serviço permanecerá em pé. 

A Service Fabric fornece um ponto de entrada de configuração de comunicação opcional para serviços que ouvem os pedidos do cliente. Tanto o RunAsync como o ponto de entrada de comunicação são substituições opcionais nos serviços de Service Fabric - o seu serviço pode optar por apenas ouvir pedidos do cliente, ou apenas executar um ciclo de processamento, ou ambos - razão pela qual o método RunAsync é autorizado a sair sem reiniciar a instância de serviço, porque pode continuar a ouvir os pedidos do cliente.

## <a name="application-api-and-environment"></a>Aplicação API e ambiente
A API de ambiente cloud Services fornece informações e funcionalidades para a instância VM atual, bem como informações sobre outras instâncias de função VM. A Service Fabric fornece informações relacionadas com o seu tempo de funcionamento e algumas informações sobre o nó que um serviço está atualmente a funcionar. 

| **Tarefa ambiental** | **Serviços em Nuvem** | **Service Fabric** |
| --- | --- | --- |
| Configurações de configuração e notificação de alteração |`RoleEnvironment` |`CodePackageActivationContext` |
| Armazenamento local |`RoleEnvironment` |`CodePackageActivationContext` |
| Informação sobre ponto final |`RoleInstance` <ul><li>Instância atual: `RoleEnvironment.CurrentRoleInstance`</li><li>Outros papéis e instâncias: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` para o endereço atual do nó</li><li>`FabricClient` e `ServicePartitionResolver` para a descoberta do ponto final de serviço</li> |
| Emulação ambiental |`RoleEnvironment.IsEmulated` |N/D |
| Evento de mudança simultânea |`RoleEnvironment` |N/D |

## <a name="configuration-settings"></a>Definições de configuração
As definições de configuração nos Serviços cloud são definidas para uma função VM e aplicam-se a todas as instâncias desse papel em VM. Estas definições são pares de valor-chave definidos nos ficheiros ServiceConfiguration.*.cscfg e podem ser acedidos diretamente através do RoleEnvironment. No Tecido de Serviço, as definições aplicam-se individualmente a cada serviço e a cada aplicação, em vez de um VM, porque um VM pode acolher vários serviços e aplicações. Um serviço é composto por três pacotes:

* **Código:** contém os executáveis de serviço, binários, DLLs e quaisquer outros ficheiros que um serviço precisa de executar.
* **Config:** todos os ficheiros de configuração e configurações para um serviço.
* **Dados:** ficheiros de dados estáticos associados ao serviço.

Cada um destes pacotes pode ser versão e atualização independente. Semelhante aos Serviços Cloud, um pacote config pode ser acedido programáticamente através de uma API e os eventos estão disponíveis para notificar o serviço de uma alteração de pacote config. Um ficheiro Settings.xml pode ser utilizado para configuração de valor chave e acesso programático semelhante à secção de definições de aplicações de um ficheiro App.config. No entanto, ao contrário dos Serviços cloud, um pacote de configuração do Service Fabric pode conter quaisquer ficheiros de configuração em qualquer formato, seja XML, JSON, YAML ou um formato binário habitual. 

### <a name="accessing-configuration"></a>Aceder à configuração
#### <a name="cloud-services"></a>Serviços Cloud
As definições de configuração a partir de ServiceConfiguration.*.cscfg podem ser acedidas através `RoleEnvironment` de . Estas definições estão globalmente disponíveis para todas as instâncias de papel na mesma implementação do Cloud Service.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Cada serviço tem o seu próprio pacote de configuração individual. Não existe um mecanismo incorporado para configurações globais acessíveis por todas as aplicações num cluster. Ao utilizar o ficheiro de configuração especial de Settings.xml do Service Fabric dentro de um pacote de configuração, os valores em Settings.xml podem ser substituídos ao nível da aplicação, tornando possíveis as definições de configuração ao nível da aplicação.

As definições de configuração são acessos dentro de cada instância de serviço através do serviço `CodePackageActivationContext` .

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
O `RoleEnvironment.Changed` evento é usado para notificar todas as instâncias de função quando ocorre uma mudança no ambiente, como uma mudança de configuração. Isto é usado para consumir atualizações de configuração sem reciclar instâncias de função ou reiniciar um processo de trabalhador.

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
Cada um dos três tipos de pacotes de um serviço - Código, Config e Dados - tem eventos que notificam uma instância de serviço quando um pacote é atualizado, adicionado ou removido. Um serviço pode conter vários pacotes de cada tipo. Por exemplo, um serviço pode ter vários pacotes config, cada versão individual e atualizável. 

Estes eventos estão disponíveis para consumir alterações nos pacotes de serviços sem reiniciar a instância de serviço.

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
As tarefas de arranque são ações que são tomadas antes do início de uma aplicação. Uma tarefa de arranque é normalmente usada para executar scripts de configuração usando privilégios elevados. Tanto os Serviços cloud como as tarefas de arranque do Service Fabric suportam tarefas de arranque. A principal diferença é que nos Serviços Cloud, uma tarefa de startup está ligada a um VM porque faz parte de uma instância de papel, enquanto no Service Fabric uma tarefa de startup está ligada a um serviço, que não está ligado a nenhum VM em particular.

| Service Fabric | Serviços Cloud |
| --- | --- |
| Localização de configuração |ServiçoDefinition.csdef |
| Privilégios |"limitado" ou "elevado" |
| Sequenciação |"simples", "fundo", "primeiro plano" |

### <a name="cloud-services"></a>Serviços Cloud
Nos Serviços Cloud, um ponto de entrada de startup é configurado por função em ServiceDefinition.csdef. 

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
No Tecido de Serviço é configurado um ponto de entrada de arranque por serviço em ServiceManifest.xml:

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

## <a name="a-note-about-development-environment"></a>Uma nota sobre ambiente de desenvolvimento
Tanto os Serviços cloud como o Tecido de Serviço são integrados no Visual Studio com modelos de projeto e suporte para depuração, configuração e implantação tanto local como para Azure. Tanto os Serviços cloud como o Tecido de Serviço também fornecem um ambiente de execução de desenvolvimento local. A diferença é que enquanto o tempo de funcionamento do Serviço cloud imita o ambiente Azure em que funciona, o Service Fabric não utiliza um emulador - utiliza o tempo completo de funcionamento do Tecido de Serviço. O ambiente de Tecido de Serviço que você executa na sua máquina de desenvolvimento local é o mesmo ambiente que funciona em produção.

## <a name="next-steps"></a>Passos seguintes
Leia mais sobre serviços fiáveis de tecido de serviço e as diferenças fundamentais entre a arquitetura da aplicação Cloud Services e Service Fabric para entender como aproveitar o conjunto completo de funcionalidades do Service Fabric.

* [Começar com serviços fiáveis de tecido de serviço](service-fabric-reliable-services-quick-start.md)
* [Guia conceptual para as diferenças entre serviços em nuvem e tecido de serviço](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
