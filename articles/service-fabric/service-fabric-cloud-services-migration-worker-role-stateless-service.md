---
title: Converter aplicativos Azure Cloud Services para Serviço Tecido
description: Este guia compara os serviços apátridas da Cloud Services Web e do Trabalhador e do Tecido de Serviço para ajudar a migrar dos Serviços cloud para o Tecido de Serviço.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: caf067f793ca2086bc068907e86a82266627d128
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75463337"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Guia para converter funções web e trabalhadores para serviços apátridas de tecido de serviço
Este artigo descreve como migrar as suas Funções Web e Trabalhadores da Cloud Services para serviços apátridas de Tecido sacana. Esta é a mais simples rota de migração dos Serviços cloud para o Tecido de Serviço para aplicações cuja arquitetura global vai permanecer aproximadamente a mesma.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Projeto de serviço na nuvem para projeto de aplicação de Tecido de Serviço
 Um projeto de Serviço cloud e um projeto de Aplicação de Tecido de Serviço têm uma estrutura semelhante e ambos representam a unidade de implementação para a sua aplicação - isto é, cada um define o pacote completo que é implementado para executar a sua aplicação. Um projeto de Serviço de Nuvem contém uma ou mais Funções Web ou Trabalhadores. Da mesma forma, um projeto de Aplicação de Tecido de Serviço contém um ou mais serviços. 

A diferença é que o projeto Cloud Service acopla a implementação da aplicação com uma implementação vM e, assim, contém configurações de configuração VM no mesmo, enquanto o projeto de Aplicação de Tecido de Serviço apenas define uma aplicação que será implementada para um conjunto de VMs existentes num cluster de Tecido de Serviço. O cluster Service Fabric em si só é implantado uma vez, seja através de um modelo de Gestor de Recursos ou através do portal Azure, e várias aplicações de Tecido de Serviço podem ser implantadas para o mesmo.

![Comparação de projetos de Tecido de Serviço e Cloud Services][3]

## <a name="worker-role-to-stateless-service"></a>Papel do trabalhador para o serviço apátrida
Conceptualmente, um Papel de Trabalhador representa uma carga de trabalho apátrida, o que significa que cada instância da carga de trabalho é idêntica e os pedidos podem ser encaminhados para qualquer instância a qualquer momento. Não se espera que cada instância se lembre do pedido anterior. Estado que a carga de trabalho opera é gerida por uma loja estatal externa, como o Azure Table Storage ou o Azure Cosmos DB. No Tecido de Serviço, este tipo de carga de trabalho é representado por um Serviço Apátrida. A abordagem mais simples para migrar um papel de trabalhador para o tecido de serviço pode ser feita através da conversão do código de papel do trabalhador para um Serviço Apátrida.

![Papel do trabalhador para o serviço apátrida][4]

## <a name="web-role-to-stateless-service"></a>Papel Web para serviço apátrida
Semelhante ao Papel do Trabalhador, um Papel Web também representa uma carga de trabalho apátrida, e por isso conceptualmente também pode ser mapeado para um serviço apátrida de Tecido de Serviço. No entanto, ao contrário das Funções Web, o Tecido de Serviço não suporta o IIS. Migrar uma aplicação web de uma Função Web para um serviço apátrida requer a primeira mudança para um quadro web que pode ser auto-hospedado e não depende do IIS ou System.Web, como ASP.NET Core 1.

| **Aplicação** | **Apoiado** | **Caminho de migração** |
| --- | --- | --- |
| formulários web ASP.NET |Não |Converter para ASP.NET Core 1 MVC |
| ASP.NET MVC |Com migração |Upgrade para ASP.NET Core 1 MVC |
| API Web ASP.NET |Com migração |Utilize servidor auto-hospedado ou ASP.NET Core 1 |
| ASP.NET Core 1 |Sim |N/D |

## <a name="entry-point-api-and-lifecycle"></a>Ponto de entrada API e ciclo de vida
As APIs de serviço de função e serviço de trabalho oferecem pontos de entrada semelhantes: 

| **Ponto de entrada** | **Papel do Trabalhador** | **Serviço de Tecido de Serviço** |
| --- | --- | --- |
| Em processamento |`Run()` |`RunAsync()` |
| Início vm |`OnStart()` |N/D |
| Paragem VM |`OnStop()` |N/D |
| Ouvinte aberto para pedidos de clientes |N/D |<ul><li> `CreateServiceInstanceListener()`para apátridas</li><li>`CreateServiceReplicaListener()`para o audato</li></ul> |

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

### <a name="service-fabric-stateless-service"></a>Serviço apátrida de Tecido de Serviço
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

Ambos têm uma sobreposição primária de "Run" para começar a processar. Os serviços `Run` `Start`de `Stop` tecido de serviço `RunAsync`combinam, e num único ponto de entrada, . O seu serviço `RunAsync` deve começar a funcionar `RunAsync` quando começar e deve deixar de funcionar quando o cancelamento do método For sinalizado. 

Existem várias diferenças fundamentais entre o ciclo de vida e a vida útil dos funções dos trabalhadores e os serviços de tecido de serviço:

* **Ciclo de vida:** A maior diferença é que um Papel de Trabalhador é um VM e, por isso, o seu ciclo de vida está ligado ao VM, que inclui eventos para quando o VM começa e para. Um serviço de tecido de serviço tem um ciclo de vida separado do ciclo de vida VM, pelo que não inclui eventos para quando o VM hospedeiro ou máquina começa e para, uma vez que não estão relacionados.
* **Vida útil:** Uma instância de função `Run` operária reciclará se o método sair. No `RunAsync` entanto, o método de um serviço de Tecido de Serviço pode esgotar-se e a instância de serviço permanecerá em pé. 

O Service Fabric fornece um ponto de entrada opcional de configuração de comunicação para serviços que ouvem pedidos de clientes. Tanto o RunAsync como o ponto de entrada de comunicação são substituições opcionais nos serviços de Tecido de Serviço - o seu serviço pode optar por apenas ouvir pedidos de clientes, ou apenas executar um ciclo de processamento, ou ambos - razão pela qual o método RunAsync é autorizado a sair sem reiniciar a instância de serviço, porque pode continuar a ouvir pedidos de clientes.

## <a name="application-api-and-environment"></a>API de aplicação e ambiente
O ambiente cloud Services API fornece informações e funcionalidades para a atual instância VM, bem como informações sobre outras instâncias de papel VM. O Service Fabric fornece informações relacionadas com o seu tempo de funcionamento e algumas informações sobre o nó em que um serviço está atualmente a funcionar. 

| **Tarefa ambiental** | **Serviços em Nuvem** | **Service Fabric** |
| --- | --- | --- |
| Configurações de configuração e notificação de alteração |`RoleEnvironment` |`CodePackageActivationContext` |
| Armazenamento Local |`RoleEnvironment` |`CodePackageActivationContext` |
| Informação sobre pontofinal |`RoleInstance` <ul><li>Instância atual:`RoleEnvironment.CurrentRoleInstance`</li><li>Outras funções e exemplos:`RoleEnvironment.Roles`</li> |<ul><li>`NodeContext`para o endereço atual do Nó</li><li>`FabricClient`e `ServicePartitionResolver` para a descoberta do ponto final do serviço</li> |
| Emulação ambiental |`RoleEnvironment.IsEmulated` |N/D |
| Evento de mudança simultânea |`RoleEnvironment` |N/D |

## <a name="configuration-settings"></a>Definições de configuração
As configurações de configuração nos Serviços cloud são definidas para uma função VM e aplicam-se a todos os casos dessa função VM. Estas definições são pares de valor-chave definidos em ficheiros ServiceConfiguration.*.cscfg e podem ser acedidos diretamente através do RoleEnvironment. No Tecido de Serviço, as configurações aplicam-se individualmente a cada serviço e a cada aplicação, em vez de a um VM, porque um VM pode acolher vários serviços e aplicações. Um serviço é composto por três pacotes:

* **Código:** contém os executáveis de serviço, binários, DLLs e quaisquer outros ficheiros que um serviço precisa de executar.
* **Config:** todos os ficheiros de configuração e configurações para um serviço.
* **Dados:** ficheiros de dados estáticos associados ao serviço.

Cada um destes pacotes pode ser versão e atualizado de forma independente. Semelhante ao Cloud Services, um pacote de config pode ser acedido programáticamente através de uma API e os eventos estão disponíveis para notificar o serviço de uma alteração de pacote config. Um ficheiro Definições.xml pode ser utilizado para configuração de valor-chave e acesso programático semelhante à secção de definições da aplicação de um ficheiro App.config. No entanto, ao contrário dos Serviços cloud, um pacote de config de tecido de serviço pode conter quaisquer ficheiros de configuração em qualquer formato, seja XML, JSON, YAML ou um formato binário personalizado. 

### <a name="accessing-configuration"></a>Aceder à configuração
#### <a name="cloud-services"></a>Serviços Cloud
As definições de configuração a partir de ServiceConfiguration.*.cscfg podem ser acedidas através `RoleEnvironment`de . Estas configurações estão globalmente disponíveis para todas as instâncias de função na mesma implementação do Serviço cloud.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Cada serviço tem o seu próprio pacote de configuração individual. Não existe um mecanismo incorporado para configurações de configuração global acessíveis por todas as aplicações num cluster. Ao utilizar o ficheiro de configuração definição de Definições especiais do Tecido de Serviço.xml dentro de um pacote de configuração, os valores em Definições.xml podem ser substituídos ao nível da aplicação, tornando possível as definições de configuração do nível de aplicação.

As definições de configuração são acessos `CodePackageActivationContext`dentro de cada instância de serviço através do serviço .

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
O `RoleEnvironment.Changed` evento é usado para notificar todas as instâncias de função quando uma mudança ocorre no ambiente, como uma mudança de configuração. Isto é usado para consumir atualizações de configuração sem casos de funções de reciclagem ou reiniciar um processo de trabalhador.

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
Cada um dos três tipos de pacotes de um serviço - Código, Config e Dados - tem eventos que notificam uma instância de serviço quando um pacote é atualizado, adicionado ou removido. Um serviço pode conter vários pacotes de cada tipo. Por exemplo, um serviço pode ter vários pacotes de config, cada um versão individualmente e atualizável. 

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
As tarefas de arranque são ações que são tomadas antes do início de uma aplicação. Uma tarefa de arranque é normalmente usada para executar scripts de configuração usando privilégios elevados. Tanto os Serviços cloud como as tarefas de arranque do Tecido de Serviço. A principal diferença é que, na Cloud Services, uma tarefa de arranque está ligada a um VM porque faz parte de uma instância de papel, enquanto no Service Fabric uma tarefa de arranque está ligada a um serviço, que não está ligado a nenhum VM em particular.

| Service Fabric | Serviços Cloud |
| --- | --- |
| Localização de configuração |ServiceDefinition.csdef |
| Privilégios |"limitado" ou "elevado" |
| Sequenciação |"simples", "fundo", "primeiro plano" |

### <a name="cloud-services"></a>Serviços Cloud
Nos Serviços cloud um ponto de entrada de arranque é configurado por função em ServiceDefinition.csdef. 

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
No Tecido de Serviço, um ponto de entrada de arranque está configurado por serviço em ServiceManifest.xml:

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

## <a name="a-note-about-development-environment"></a>Uma nota sobre o ambiente de desenvolvimento
Tanto os Serviços cloud como o Tecido de Serviço estão integrados com o Estúdio Visual com modelos de projeto e suporte para depuração, configuração e implantação tanto local mente como para o Azure. Tanto os Serviços cloud como o Service Fabric também proporcionam um ambiente de execução de desenvolvimento local. A diferença é que, enquanto o tempo de desenvolvimento do Cloud Service emudece o ambiente Azure em que funciona, o Service Fabric não utiliza um emulador - utiliza o tempo de funcionamento completo do Tecido de Serviço. O ambiente de Tecido de Serviço que executa na sua máquina de desenvolvimento local é o mesmo ambiente que funciona em produção.

## <a name="next-steps"></a>Passos seguintes
Leia mais sobre serviços de confiança em tecidos e as diferenças fundamentais entre serviços de nuvem e arquitetura de aplicação de tecido de serviço para entender como aproveitar todo o conjunto de funcionalidades de Tecido de Serviço.

* [Começar com serviços fiáveis de tecido de serviço](service-fabric-reliable-services-quick-start.md)
* [Guia conceptual para as diferenças entre serviços na nuvem e tecido de serviço](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
