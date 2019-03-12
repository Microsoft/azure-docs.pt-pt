---
title: Comunicação para funções nos serviços Cloud | Documentos da Microsoft
description: Instâncias de função nos serviços Cloud podem ter pontos finais (http, https, tcp, udp) definidos para os mesmos que se comunicam com o exterior ou entre outras instâncias de função.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7008a083-acbe-4fb8-ae60-b837ef971ca1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: jeconnoc
ms.openlocfilehash: 4adc6ef6e7dd445eea3fd567072a995e3ac07dda
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539637"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Ativar a comunicação para instâncias de função no azure
Funções de serviço cloud comunicam através de ligações internas e externas. Ligações externas são chamadas **pontos finais de entrada** enquanto são chamadas de ligações internas **pontos finais internos**. Este tópico descreve como modificar o [definição do serviço](cloud-services-model-and-package.md#csdef) para criar pontos finais.

## <a name="input-endpoint"></a>Ponto final de entrada
O ponto final de entrada é usado quando deseja expor uma porta para o exterior. Especifique o tipo de protocolo e a porta do ponto de extremidade que, em seguida, aplica-se para ambas as portas externas e internas para o ponto final. Se quiser, pode especificar uma porta diferente interna para o ponto final com o [localPort](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) atributo.

O ponto final de entrada, pode utilizar os seguintes protocolos: **http, https, tcp, udp**.

Para criar um ponto de final de entrada, adicione a **ao InputEndpoint** elemento subordinado para o **pontos de extremidade** elemento da função de uma web ou de uma função de trabalho.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Ponto final de entrada de instância
Pontos finais de entrada de instância são semelhantes a entrada de pontos de extremidade, mas permite-lhe mapear portas públicas específicas para cada instância de função individuais com o encaminhamento de porta no balanceador de carga. Pode especificar uma única porta destinado ao público, ou um intervalo de portas.

Só pode utilizar o ponto de final de entrada de instância **tcp** ou **udp** como o protocolo.

Para criar um ponto de final de entrada de instância, adicione a **InstanceInputEndpoint** elemento subordinado para o **pontos de extremidade** elemento da função de uma web ou de uma função de trabalho.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Ponto final interno
Pontos finais internos estão disponíveis para a comunicação de instância a instância. A porta é opcional e se for omitido, uma porta dinâmica é atribuída para o ponto final. Pode ser utilizado um intervalo de portas. Existe um limite de cinco pontos finais internos por função.

O ponto final interno pode utilizar os seguintes protocolos: **http, tcp, udp, qualquer**.

Para criar um ponto de final de entrada interno, adicione a **InternalEndpoint** elemento subordinado para o **pontos de extremidade** elemento da função de uma web ou de uma função de trabalho.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Também pode utilizar um intervalo de portas.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Vs de funções de trabalho. Funções da Web
Há uma diferença mínima com pontos finais ao trabalhar com as funções de web e de trabalho. A função da web tem de ter pelo menos um ponto final de entrada único utilizando a **HTTP** protocolo.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Com o SDK .NET para aceder a um ponto final
A biblioteca gerida do Azure fornece métodos para instâncias de função comunicar em tempo de execução. A partir do código em execução dentro de uma instância de função, pode obter informações sobre a existência de outras instâncias de função e seus pontos de extremidade, bem como informações sobre a instância de função atual.

> [!NOTE]
> Só é possível recuperar informações sobre as instâncias de função que são executados no seu serviço cloud e que definir, pelo menos, um ponto de extremidade interno. Não é possível obter dados sobre instâncias de função em execução num serviço diferente.
> 
> 

Pode utilizar o [instâncias](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) propriedade para recuperar instâncias de uma função. Utilizar pela primeira vez o [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) para devolver uma referência para a instância de função atual e, em seguida, utilize o [função](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) propriedade para retornar uma referência para a função em si.

Quando se liga a uma instância de função programaticamente através do SDK de .NET, é relativamente fácil de acessar as informações de ponto final. Por exemplo, depois que já ligou a um ambiente de função específica, pode obter a porta de um ponto final específico com este código:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

O **instâncias** propriedade retorna uma coleção de **RoleInstance** objetos. Esta coleção sempre contém a instância atual. Se a função não define um ponto final interno, a coleção inclui a instância atual, mas não existem outras instâncias. O número de instâncias de função na coleção será sempre 1 no caso em que nenhum ponto final interno é definida para a função. Se a função define um ponto final interno, suas instâncias são descobertas em tempo de execução e o número de instâncias na coleção irão corresponder ao número de instâncias especificada para a função no ficheiro de configuração do serviço.

> [!NOTE]
> A biblioteca gerida do Azure não fornece um meio de determinar o estado de funcionamento de outras instâncias de função, mas é possível implementar essas avaliações de estado de funcionamento por conta própria se precisar de seu serviço essa funcionalidade. Pode usar [diagnóstico do Azure](cloud-services-dotnet-diagnostics.md) para obter informações sobre a execução de instâncias de função.
> 
> 

Para determinar o número de porta para um ponto final interno numa instância de função, pode utilizar o [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) propriedade para retornar um objeto de dicionário que contém nomes de ponto final e o IP correspondente endereços e portas. O [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) propriedade retorna o endereço IP e porta para um ponto de extremidade especificado. O **PublicIPEndpoint** propriedade devolve a porta para um ponto final com balanceamento de carga. A parte do endereço IP do **PublicIPEndpoint** propriedade não é utilizada.

Eis um exemplo que faz a iteração de instâncias de função.

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

Eis um exemplo de uma função de trabalho que obtém o ponto de extremidade exposto através da definição de serviço e começa a escutar ligações.

> [!WARNING]
> Este código só funciona para um serviço implementado. Quando em execução no emulador de computação do Azure, os elementos de configuração que criar pontos finais de porta direta do serviço (**InstanceInputEndpoint** elementos) são ignorados.
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
Depois de definir pontos finais internos, pode adicionar regras de tráfego de rede (com base em pontos de extremidade que criou) para controlar como as instâncias de função podem comunicar entre si. O diagrama seguinte mostra alguns cenários comuns para controlar a comunicação de função:

![Cenários de regras de tráfego de rede](./media/cloud-services-enable-communication-role-instances/scenarios.png "cenários de regras de tráfego de rede")

O exemplo de código seguinte mostra as definições de funções para as funções mostradas no diagrama anterior. Cada definição de função inclui pelo menos um ponto final interno definido:

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
> Restrição de comunicação entre funções pode ocorrer com pontos finais internos de ambos fixo e atribuídas automaticamente as portas.
> 
> 

Por predefinição, depois de um ponto final interno é definido, comunicação pode circular de qualquer função para o ponto final interno de uma função sem quaisquer restrições. Para restringir a comunicação, tem de adicionar uma **NetworkTrafficRules** elemento para a **ServiceDefinition** elemento no ficheiro de definição do serviço.

### <a name="scenario-1"></a>Cenário 1
Permitir apenas tráfego de rede da **WebRole1** ao **WorkerRole1**.

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
Permite apenas tráfego de rede da **WebRole1** ao **WorkerRole1** e **WorkerRole2**.

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
Permite apenas tráfego de rede da **WebRole1** ao **WorkerRole1**, e **WorkerRole1** para **WorkerRole2**.

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

### <a name="scenario-4"></a>Cenário de 4
Permite apenas tráfego de rede da **WebRole1** ao **WorkerRole1**, **WebRole1** para **WorkerRole2**, e **WorkerRole1**  para **WorkerRole2**.

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

Uma referência de esquema XML para os elementos usados acima pode ser encontrada [aqui](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o serviço em nuvem [modelo](cloud-services-model-and-package.md).

