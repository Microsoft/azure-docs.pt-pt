---
title: Comunicação para Papéis em Serviços em Nuvem (clássico) | Microsoft Docs
description: As instâncias de função nos Serviços cloud podem ter pontos finais (https, tcp, udp) definidos para eles que comunicam com o exterior ou entre outras instâncias de papel.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 82aa1579a1f7feb36732153341e1eacf266a7218
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743037"
---
# <a name="enable-communication-for-role-instances-in-azure-cloud-services-classic"></a>Permitir a comunicação para instâncias de função em Azure Cloud Services (clássico)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

As funções de serviço em nuvem comunicam através de ligações internas e externas. As ligações externas são chamadas **pontos finais de entrada,** enquanto as ligações internas são chamadas **pontos finais internos**. Este tópico descreve como modificar a [definição](cloud-services-model-and-package.md#csdef) de serviço para criar pontos finais.

## <a name="input-endpoint"></a>Ponto final de entrada
O ponto final de entrada é utilizado quando se pretende expor uma porta ao exterior. Especifica o tipo de protocolo e a porta do ponto final que depois se aplica tanto às portas externas como internas para o ponto final. Se quiser, pode especificar uma porta interna diferente para o ponto final com o atributo [LocalPort.](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint)

O ponto final de entrada pode utilizar os seguintes protocolos: **https, tcp, udp**.

Para criar um ponto final de entrada, adicione o elemento criança **InputEndpoint** ao elemento **Endpoints** de uma web ou papel de trabalhador.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Ponto final de entrada de exemplo
Os pontos finais de entrada de exemplo são semelhantes aos pontos finais de entrada, mas permite mapear portas específicas viradas para o público para cada instância de função, utilizando o reencaminhamento da porta no equilibrador de carga. Pode especificar uma única porta virada para o público ou uma gama de portas.

O ponto final de entrada de exemplo só pode usar **tcp** ou **udp** como protocolo.

Para criar um ponto final de entrada de exemplo, adicione o elemento criança **InstanceInputEndpoint** ao elemento **Endpoints** de uma função web ou de trabalhador.

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
Os pontos finais internos estão disponíveis para comunicação por exemplo. A porta é opcional e, se omitida, uma porta dinâmica é atribuída ao ponto final. Pode ser utilizada uma gama de portas. Há um limite de cinco pontos finais internos por função.

O ponto final interno pode utilizar os seguintes protocolos: **http, tcp, udp, qualquer**.

Para criar um ponto final de entrada interno, adicione o elemento criança **InternalEndpoint** ao elemento **Endpoints** de uma web ou papel de trabalhador.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Também pode utilizar uma gama de portas.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Funções dos trabalhadores vs. Funções Web
Há uma pequena diferença com os pontos finais quando se trabalha com papéis de trabalhador e web. A função web deve ter no mínimo um ponto final de entrada único utilizando o protocolo **HTTP.**

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Utilizar o .NET SDK para aceder a um ponto final
A Azure Managed Library fornece métodos para que as funções se comuniquem em tempo de execução. A partir de códigos que executam dentro de uma instância de função, você pode obter informações sobre a existência de outras instâncias de papel e seus pontos finais, bem como informações sobre a instância de papel atual.

> [!NOTE]
> Só é possível obter informações sobre as instâncias de função que estão a ser executando no seu serviço de nuvem e que definem pelo menos um ponto final interno. Não é possível obter dados sobre instâncias de função que estão a decorrer num serviço diferente.
> 
> 

Você pode usar a propriedade [Instances](/previous-versions/azure/reference/ee741904(v=azure.100)) para recuperar instâncias de um papel. Primeiro use o [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)) para devolver uma referência à instância de função atual, e depois use a propriedade [Role](/previous-versions/azure/reference/ee741918(v=azure.100)) para devolver uma referência ao papel em si.

Quando se conecta a uma instância de função programáticamente através do .NET SDK, é relativamente fácil aceder à informação do ponto final. Por exemplo, depois de já ter ligado a um ambiente de função específica, pode obter a porta de um ponto final específico com este código:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

A propriedade **Instances** devolve uma coleção de objetos **RoleInstance.** Esta coleção contém sempre a instância atual. Se a função não definir um ponto final interno, a coleção inclui a instância atual, mas nenhuma outra instância. O número de instâncias na coleção será sempre 1 no caso de não ser definido nenhum ponto final interno para o papel. Se a função definir um ponto final interno, as suas instâncias são detetáveis em tempo de execução, e o número de casos na coleção corresponderá ao número de casos especificados para a função no ficheiro de configuração de serviço.

> [!NOTE]
> A Biblioteca Gerida Azure não fornece um meio de determinar a saúde de outras instâncias de papel, mas você pode implementar tais avaliações de saúde se o seu serviço precisar desta funcionalidade. Pode utilizar [o Azure Diagnostics](cloud-services-dotnet-diagnostics.md) para obter informações sobre as instâncias de execução.
> 
> 

Para determinar o número da porta para um ponto final interno numa instância de função, pode utilizar a [`InstanceEndpoints`](/previous-versions/azure/reference/ee741917(v=azure.100)) propriedade para devolver um objeto do Dicionário que contenha nomes de ponto final e os respetivos endereços IP e portas. A [`IPEndpoint`](/previous-versions/azure/reference/ee741919(v=azure.100)) propriedade devolve o endereço IP e a porta para um ponto final especificado. A `PublicIPEndpoint` propriedade devolve a porta para um ponto final equilibrado de carga. A parte do endereço IP da `PublicIPEndpoint` propriedade não é utilizada.

Aqui está um exemplo que itera instâncias de papel.

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

Aqui está um exemplo de um papel de trabalhador que deixa o ponto final exposto através da definição de serviço e começa a ouvir as ligações.

> [!WARNING]
> Este código só funcionará para um serviço implantado. Ao correr no Emulador Azure Compute, os elementos de configuração de serviço que criam pontos finais diretos de porta **(elementos InstanceInputEndpoint)** são ignorados.
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

## <a name="network-traffic-rules-to-control-role-communication"></a>Regras de tráfego de rede para controlar a comunicação de funções
Depois de definir pontos finais internos, pode adicionar regras de tráfego de rede (com base nos pontos finais que criou) para controlar como as instâncias de função podem comunicar entre si. O seguinte diagrama mostra alguns cenários comuns para controlar a comunicação de funções:

![Cenários de regras de tráfego de rede](./media/cloud-services-enable-communication-role-instances/scenarios.png "Cenários de regras de tráfego de rede")

O exemplo de código a seguir mostra definições de função para as funções mostradas no diagrama anterior. Cada definição de função inclui pelo menos um ponto final interno definido:

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
> A restrição de comunicação entre funções pode ocorrer com pontos finais internos de portas fixas e automaticamente atribuídas.
> 
> 

Por padrão, após a definição de um ponto final interno, a comunicação pode fluir de qualquer papel para o ponto final interno de uma função sem quaisquer restrições. Para restringir a comunicação, deve adicionar um elemento **NetworkTrafficRules** ao elemento **'Definição de Serviço'** no ficheiro de definição de serviço.

### <a name="scenario-1"></a>Cenário 1
Só permitir o tráfego de rede de **WebRole1** a **WorkerRole1**.

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
Só permite o tráfego de rede de **WebRole1** a **WorkerRole1** e **WorkerRole2**.

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
Só permite o tráfego de rede de **WebRole1** a **WorkerRole1,** e **WorkerRole1** a **WorkerRole2**.

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
Só permite o tráfego de rede de **WebRole1** a **WorkerRole1,** **WebRole1** a **WorkerRole2,** e **WorkerRole1** a **WorkerRole2**.

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

Uma referência de esquema XML para os elementos acima utilizados pode ser encontrada [aqui](/previous-versions/azure/reference/gg557551(v=azure.100)).

## <a name="next-steps"></a>Passos seguintes
Leia mais sobre o [modelo](cloud-services-model-and-package.md)cloud service .




