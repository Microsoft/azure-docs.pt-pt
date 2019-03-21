---
title: O Azure Service Fabric com a descrição geral da gestão de API | Documentos da Microsoft
description: Este artigo é uma introdução à utilização de API Management do Azure como um gateway para seus aplicativos do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 60466f0c3c0e674dcbfa287a0368462fd5a1d18f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58120317"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric com a descrição geral da gestão de API do Azure

Geralmente, as aplicações da cloud precisam de um gateway de front-end que forneça um único ponto de entrada para utilizadores, dispositivos ou outras aplicações. No Service Fabric, um gateway pode ser qualquer serviço sem estado, tais como um [aplicação ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), ou outro serviço concebido para entrada de tráfego, tal como [dos Hubs de eventos](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/), ou [API Management do Azure](https://docs.microsoft.com/azure/api-management/).

Este artigo é uma introdução à utilização de API Management do Azure como um gateway para seus aplicativos do Service Fabric. Gestão de API integra-se diretamente com o Service Fabric, que lhe permite publicar APIs com um conjunto avançado de regras de encaminhamento para os serviços de recursos de infraestrutura do serviço de back-end. 

## <a name="availability"></a>Disponibilidade

> [!IMPORTANT]
> Esta funcionalidade está disponível na **Premium** e **programador** escalões da gestão de API devido a necessários virtual suporte de rede.

## <a name="architecture"></a>Arquitetura

Uma arquitetura comum do Service Fabric utiliza uma aplicação web de página única que faz chamadas HTTP para os serviços de back-end que expõem HTTP APIs. O [aplicação de exemplo da introdução do Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) mostra um exemplo dessa arquitetura.

Neste cenário, um serviço web sem monitorização de estado serve como o gateway para a aplicação do Service Fabric. Essa abordagem exige que escreva um serviço web que pode pedidos HTTP de proxy para serviços de back-end, conforme mostrado no diagrama seguinte:

![Service Fabric com a descrição geral da topologia de API Management do Azure][sf-web-app-stateless-gateway]

À medida que aplicações aumentam em complexidade, então, fazer os gateways que tem de apresentar uma API na frente de uma infinidade de serviços de back-end. Gestão de API do Azure foi concebido para lidar com APIs complexas com regras de encaminhamento, controlo de acesso, limitação de taxa, monitorização, registo de eventos e cache de resposta com um mínimo de trabalho da sua parte. Gestão de API do Azure suporta a deteção de serviços do Service Fabric, resolução de partições e seleção de réplica inteligentemente rotear solicitações diretamente para os serviços de back-end no Service Fabric para que não tenha de escrever seu próprio gateway de API sem monitoração de estado. 

Neste cenário, a web interface do Usuário é ainda servido através de um serviço da web, enquanto as chamadas de API de HTTP são gerenciadas e encaminhadas através da gestão de API do Azure, conforme mostrado no diagrama seguinte:

![Service Fabric com a descrição geral da topologia de API Management do Azure][sf-apim-web-app]

## <a name="application-scenarios"></a>Cenários de aplicações

Serviços no Service Fabric podem ser com ou sem estado, e eles podem ser particionados utilizando um dos três esquemas: singleton, intervalo de int 64 e com nome. Resolução de ponto final de serviço requer a identificação de uma partição específica de uma instância de serviço específico. Ao resolver um ponto final de um serviço, tanto o nome de instância de serviço (por exemplo, `fabric:/myapp/myservice`), bem como a partição específica do serviço tem de ser especificada, exceto no caso da partição singleton.

Gestão de API do Azure pode ser utilizado com qualquer combinação de serviços sem estado, serviços com estado e qualquer esquema de particionamento.

## <a name="send-traffic-to-a-stateless-service"></a>Enviar tráfego para um serviço sem estado

No caso mais simples, o tráfego é encaminhado para uma instância de serviço sem estado. Para conseguir isso, uma operação de gestão de API contém uma política de processamento de entrada com um back-end de Service Fabric que mapeia para uma instância de serviço sem estado específico no back-end do Service Fabric. Os pedidos enviados para esse serviço são enviados para uma réplica aleatória a instância de serviço sem estado.

#### <a name="example"></a>Exemplo
No cenário seguinte, uma aplicação do Service Fabric contém um serviço sem estado com o nome `fabric:/app/fooservice`, que expõe uma API HTTP interna. O nome da instância de serviço é bem conhecido e pode ser inserida diretamente na política de processamento de entrada de gestão de API. 

![Service Fabric com a descrição geral da topologia de API Management do Azure][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Enviar tráfego para um serviço com estado

Assim como o cenário de serviço sem estado, o tráfego pode ser reencaminhado para uma instância de serviço com estado. Neste caso, uma operação de gestão de API contém uma política de processamento de entrada com um back-end de Service Fabric que mapeia um pedido para uma partição específica de um específico *com monitoração de estado* instância de serviço. A partição para mapear a cada pedido é calculada através de um método de lambda usando alguma entrada da solicitação HTTP recebida, como um valor no caminho do URL. A política pode ser configurada para enviar pedidos para a réplica primária apenas, ou para uma réplica aleatória para operações de leitura.

#### <a name="example"></a>Exemplo

No cenário seguinte, uma aplicação do Service Fabric contém um serviço com estado particionado com o nome `fabric:/app/userservice` que expõe uma API HTTP interna. O nome da instância de serviço é bem conhecido e pode ser inserida diretamente na política de processamento de entrada de gestão de API.  

O serviço é particionado usando o esquema de partição Int64 com duas partições e um intervalo de chaves que abranja `Int64.MinValue` para `Int64.MaxValue`. A política de back-end computa uma chave de partição dentro do intervalo ao converter o `id` valor fornecido no caminho de pedido de URL para um inteiro de 64 bits, embora qualquer algoritmo pode ser aqui utilizado para calcular a chave de partição. 

![Service Fabric com a descrição geral da topologia de API Management do Azure][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Enviar tráfego para vários serviços sem estado

Em cenários mais avançados, pode definir uma operação de gestão de API que mapeia as solicitações para mais de uma instância de serviço. Neste caso, cada operação contém uma política que mapeia as solicitações para uma instância de serviço específico com base nos valores de entrada solicitação HTTP, tais como a cadeia de consulta ou de caminho de URL e, no caso de serviços com estado, uma partição dentro da instância de serviço. 

Para conseguir isso, uma operação de gestão de API contém uma política de processamento de entrada com um back-end de Service Fabric que mapeia para uma instância de serviço sem estado no back-end de Service Fabric com base nos valores obtidos da solicitação HTTP recebida. Pedidos para uma instância de serviço são enviados para uma réplica aleatória a instância de serviço.

#### <a name="example"></a>Exemplo

Neste exemplo, uma nova instância de serviço sem estado é criada para cada utilizador de uma aplicação com um nome gerado dinamicamente usando a seguinte fórmula:
 
- `fabric:/app/users/<username>`

  Cada serviço tem um nome exclusivo, mas os nomes não são conhecidos iniciais porque os serviços são criados em resposta ao utilizador ou administrador de entrada e, portanto, não pode ser codificada em políticas APIM ou regras de encaminhamento. Em vez disso, o nome do serviço ao qual pretende enviar uma solicitação é gerado na definição de política de back-end do `name` valor fornecido no caminho de pedido do URL. Por exemplo:

  - Um pedido para `/api/users/foo` é encaminhado para a instância de serviço `fabric:/app/users/foo`
  - Um pedido para `/api/users/bar` é encaminhado para a instância de serviço `fabric:/app/users/bar`

![Service Fabric com a descrição geral da topologia de API Management do Azure][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Enviar tráfego para vários serviços com estado

Semelhante ao exemplo de serviço sem estado, uma operação de gestão de API pode mapear os pedidos para mais do que um **com monitoração de estado** instância de serviço, caso em que também poderá ter de efetuar a resolução de partição para cada instância de serviço com estado.

Para conseguir isso, uma operação de gestão de API contém uma política de processamento de entrada com um back-end de Service Fabric que mapeia para uma instância de serviço com estado no back-end de Service Fabric com base nos valores obtidos da solicitação HTTP recebida. Além do mapeamento um pedido para a instância de serviço específico, o pedido também pode ser mapeado para uma partição específica dentro da instância de serviço e, opcionalmente, para a réplica primária ou uma réplica secundária aleatória dentro da partição.

#### <a name="example"></a>Exemplo

Neste exemplo, é criada uma instância de serviço com estado novo para cada usuário do aplicativo com um nome gerado dinamicamente usando a seguinte fórmula:
 
- `fabric:/app/users/<username>`

  Cada serviço tem um nome exclusivo, mas os nomes não são conhecidos iniciais porque os serviços são criados em resposta ao utilizador ou administrador de entrada e, portanto, não pode ser codificada em políticas APIM ou regras de encaminhamento. Em vez disso, o nome do serviço ao qual pretende enviar uma solicitação é gerado na definição de política de back-end do `name` valor indicado o caminho de pedido de URL. Por exemplo:

  - Um pedido para `/api/users/foo` é encaminhado para a instância de serviço `fabric:/app/users/foo`
  - Um pedido para `/api/users/bar` é encaminhado para a instância de serviço `fabric:/app/users/bar`

Cada instância de serviço também é particionada usando o esquema de partição Int64 com duas partições e um intervalo de chaves que abranja `Int64.MinValue` para `Int64.MaxValue`. A política de back-end computa uma chave de partição dentro do intervalo ao converter o `id` valor fornecido no caminho de pedido de URL para um inteiro de 64 bits, embora qualquer algoritmo pode ser aqui utilizado para calcular a chave de partição. 

![Service Fabric com a descrição geral da topologia de API Management do Azure][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Passos Seguintes

Siga os [tutorial](service-fabric-tutorial-deploy-api-management.md) para configurar o seu primeiro cluster do Service Fabric com pedidos de gestão de API e o flow através da gestão de API para seus serviços.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png