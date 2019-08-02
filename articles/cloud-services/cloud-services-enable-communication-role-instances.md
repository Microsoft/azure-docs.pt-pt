---
title: Comunicação para funções nos serviços de nuvem | Microsoft Docs
description: As instâncias de função nos serviços de nuvem podem ter pontos de extremidade (http, HTTPS, TCP, UDP) definidos para eles que se comunicam com o exterior ou entre outras instâncias de função.
services: cloud-services
documentationcenter: ''
author: georgewallace
manager: carmonm
ms.service: cloud-services
ms.topic: article
ms.date: 12/14/2016
ms.author: gwallace
ms.openlocfilehash: 74ef5567becee27b4af837a6977119d7cf0f3e4b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359098"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Habilitar a comunicação para instâncias de função no Azure
As funções do serviço de nuvem se comunicam por meio de conexões internas e externas. As conexões externas são chamadas de **pontos de extremidade de entrada** enquanto as conexões internas são chamadas de pontos de **extremidade internos**. Este tópico descreve como modificar a [definição de serviço](cloud-services-model-and-package.md#csdef) para criar pontos de extremidade.

## <a name="input-endpoint"></a>Ponto de extremidade de entrada
O ponto de extremidade de entrada é usado quando você deseja expor uma porta para fora. Você especifica o tipo de protocolo e a porta do ponto de extremidade que, em seguida, se aplica para as portas externas e internas do ponto de extremidade. Se desejar, você pode especificar uma porta interna diferente para o ponto de extremidade com o atributo [localPort](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) .

O ponto de extremidade de entrada pode usar os seguintes protocolos: **http, HTTPS, TCP, UDP**.

Para criar um ponto de extremidade de entrada, adicione o elemento filho **InputEndpoint** ao elemento de **pontos** de extremidade de uma função Web ou de trabalho.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Ponto de extremidade de entrada de instância
Os pontos de extremidade de entrada de instância são semelhantes aos pontos de extremidade de entrada, mas permitem mapear portas específicas para o público para cada instância de função individual usando o encaminhamento de porta no balanceador de carga. Você pode especificar uma única porta voltada para o público ou um intervalo de portas.

O ponto de extremidade de entrada de instância só pode usar **TCP** ou **UDP** como o protocolo.

Para criar um ponto de extremidade de entrada de instância, adicione o elemento filho **InstanceInputEndpoint** ao elemento de **pontos** de extremidade de uma função Web ou de trabalho.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Ponto de extremidade interno
Os pontos de extremidade internos estão disponíveis para comunicação de instância para instância. A porta é opcional e, se omitida, uma porta dinâmica é atribuída ao ponto de extremidade. Um intervalo de portas pode ser usado. Há um limite de cinco pontos de extremidade internos por função.

O ponto de extremidade interno pode usar os seguintes protocolos: **http, TCP, UDP, any**.

Para criar um ponto de extremidade de entrada interno, adicione o elemento filho **InternalEndpoint** ao elemento de **pontos** de extremidade de uma função Web ou de trabalho.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Você também pode usar um intervalo de portas.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Funções de trabalho vs. Funções da Web
Há uma pequena diferença com pontos de extremidade ao trabalhar com funções de trabalho e Web. A função Web deve ter, no mínimo, um único ponto de extremidade de entrada usando o protocolo **http** .

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Usando o SDK do .NET para acessar um ponto de extremidade
A biblioteca gerenciada do Azure fornece métodos para que as instâncias de função se comuniquem em tempo de execução. Do código em execução em uma instância de função, você pode recuperar informações sobre a existência de outras instâncias de função e seus pontos de extremidade, bem como informações sobre a instância de função atual.

> [!NOTE]
> Você só pode recuperar informações sobre instâncias de função que estão sendo executadas em seu serviço de nuvem e que definem pelo menos um ponto de extremidade interno. Você não pode obter dados sobre instâncias de função em execução em um serviço diferente.
> 
> 

Você pode usar a [](/previous-versions/azure/reference/ee741904(v=azure.100)) Propriedade instances para recuperar instâncias de uma função. Primeiro, use o [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)) para retornar uma referência à instância de função atual e, em seguida, use a propriedade [role](/previous-versions/azure/reference/ee741918(v=azure.100)) para retornar uma referência à função em si.

Quando você se conecta a uma instância de função programaticamente por meio do SDK do .NET, é relativamente fácil acessar as informações do ponto de extremidade. Por exemplo, depois que você já tiver se conectado a um ambiente de função específico, poderá obter a porta de um ponto de extremidade específico com este código:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

A  Propriedade instances retorna uma coleção de objetos **RoleInstance** . Essa coleção sempre contém a instância atual. Se a função não definir um ponto de extremidade interno, a coleção incluirá a instância atual, mas não outras instâncias. O número de instâncias de função na coleção sempre será 1 no caso em que nenhum ponto de extremidade interno for definido para a função. Se a função definir um ponto de extremidade interno, suas instâncias serão detectáveis em tempo de execução e o número de instâncias na coleção corresponderá ao número de instâncias especificado para a função no arquivo de configuração de serviço.

> [!NOTE]
> A biblioteca gerenciada do Azure não fornece um meio de determinar a integridade de outras instâncias de função, mas você mesmo pode implementar essas avaliações de integridade se o seu serviço precisar dessa funcionalidade. Você pode usar [diagnóstico do Azure](cloud-services-dotnet-diagnostics.md) para obter informações sobre a execução de instâncias de função.
> 
> 

Para determinar o número da porta para um ponto de extremidade interno em uma instância de função, você pode usar a propriedade [InstanceEndpoints](/previous-versions/azure/reference/ee741917(v=azure.100)) para retornar um objeto Dictionary que contém nomes de ponto de extremidade e seus endereços IP e portas correspondentes. A propriedade [IPEndPoint](/previous-versions/azure/reference/ee741919(v=azure.100)) retorna o endereço IP e a porta para um ponto de extremidade especificado. A propriedade **PublicIPEndpoint** retorna a porta para um ponto de extremidade com balanceamento de carga. A parte do endereço IP da propriedade **PublicIPEndpoint** não é usada.

Aqui está um exemplo que itera as instâncias de função.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Aqui está um exemplo de uma função de trabalho que obtém o ponto de extremidade exposto por meio da definição de serviço e começa a escutar conexões.

> [!WARNING]
> Esse código só funcionará para um serviço implantado. Ao executar no emulador de computação do Azure, os elementos de configuração de serviço que criam pontos de extremidade de porta direta (elementos**InstanceInputEndpoint** ) são ignorados.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Regras de tráfego de rede para controlar a comunicação de função
Depois de definir pontos de extremidade internos, você pode adicionar regras de tráfego de rede (com base nos pontos de extremidade que você criou) para controlar como as instâncias de função podem se comunicar entre si. O diagrama a seguir mostra alguns cenários comuns para controlar a comunicação de função:

![Cenários de regras de tráfego de rede](./media/cloud-services-enable-communication-role-instances/scenarios.png "Cenários de regras de tráfego de rede")

O exemplo de código a seguir mostra as definições de função para as funções mostradas no diagrama anterior. Cada definição de função inclui pelo menos um ponto de extremidade interno definido:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> A restrição de comunicação entre funções pode ocorrer com pontos de extremidade internos de portas fixas e atribuídas automaticamente.
> 
> 

Por padrão, depois que um ponto de extremidade interno é definido, a comunicação pode fluir de qualquer função para o ponto de extremidade interno de uma função sem nenhuma restrição. Para restringir a comunicação, você deve adicionar um elemento **NetworkTrafficRules** ao elemento de Service **Definition** no arquivo de definição de serviço.

### <a name="scenario-1"></a>Cenário 1
Permitir somente o tráfego de rede de **WebRole1** para **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Cenário 2
Permite apenas o tráfego de rede de **WebRole1** para **WorkerRole1** e **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Cenário 3
Permite apenas o tráfego de rede de **WebRole1** para **WorkerRole1**e **WorkerRole1** para **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Cenário 4
Permite apenas o tráfego de rede de **WebRole1** para **WorkerRole1**, **WebRole1** para **WorkerRole2**e **WorkerRole1** para **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Uma referência de esquema XML para os elementos usados acima pode ser encontrada [aqui](/previous-versions/azure/reference/gg557551(v=azure.100)).

## <a name="next-steps"></a>Passos Seguintes
Leia mais sobre o [modelo](cloud-services-model-and-package.md)de serviço de nuvem.

