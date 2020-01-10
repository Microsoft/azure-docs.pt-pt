---
title: Azure Service Fabric com visão geral do gerenciamento de API
description: Este artigo é uma introdução ao uso do gerenciamento de API do Azure como um gateway para seus aplicativos Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 656bb6d400461c93540b77d871502b738c679f47
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75378115"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric com visão geral do gerenciamento de API do Azure

Geralmente, as aplicações da cloud precisam de um gateway de front-end que forneça um único ponto de entrada para utilizadores, dispositivos ou outras aplicações. No Service Fabric, um gateway pode ser qualquer serviço sem estado, como um [aplicativo ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), ou outro serviço projetado para entrada de tráfego, como [hubs de eventos](https://docs.microsoft.com/azure/event-hubs/), [Hub IOT](https://docs.microsoft.com/azure/iot-hub/)ou [Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/).

Este artigo é uma introdução ao uso do gerenciamento de API do Azure como um gateway para seus aplicativos Service Fabric. O gerenciamento de API se integra diretamente com o Service Fabric, permitindo que você publique APIs com um conjunto avançado de regras de roteamento para seus serviços de back-end Service Fabric. 

## <a name="availability"></a>Disponibilidade

> [!IMPORTANT]
> Esse recurso está disponível nas camadas **Premium** e **desenvolvedor** do gerenciamento de API devido ao suporte necessário à rede virtual.

## <a name="architecture"></a>Arquitetura

Uma arquitetura de Service Fabric comum usa um aplicativo Web de página única que faz chamadas HTTP para serviços de back-end que expõem APIs HTTP. O [aplicativo de exemplo Service Fabric introdução](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) mostra um exemplo dessa arquitetura.

Nesse cenário, um serviço Web sem estado serve como o gateway para o aplicativo Service Fabric. Essa abordagem exige que você grave um serviço Web que pode fazer proxy de solicitações HTTP para serviços de back-end, conforme mostrado no diagrama a seguir:

![Service Fabric com visão geral da topologia de gerenciamento de API do Azure][sf-web-app-stateless-gateway]

À medida que os aplicativos crescem em complexidade, os gateways devem apresentar uma API na frente de inúmeros serviços de back-end. O gerenciamento de API do Azure foi projetado para lidar com APIs complexas com regras de roteamento, controle de acesso, limitação de taxa, monitoramento, log de eventos e cache de resposta com o mínimo de trabalho de sua parte. O gerenciamento de API do Azure dá suporte à descoberta de serviço Service Fabric, resolução de partição e seleção de réplica para rotear solicitações de forma inteligente diretamente para serviços de back-end no Service Fabric para que você não precise escrever seu próprio gateway de API sem estado. 

Nesse cenário, a interface do usuário da Web ainda é servida por meio de um serviço Web, enquanto as chamadas da API HTTP são gerenciadas e roteadas por meio do gerenciamento de API do Azure, conforme mostrado no diagrama a seguir:

![Service Fabric com visão geral da topologia de gerenciamento de API do Azure][sf-apim-web-app]

## <a name="application-scenarios"></a>Cenários de aplicações

Os serviços no Service Fabric podem ser com ou sem estado e podem ser particionados usando um dos três esquemas: singleton, intervalo int-64 e nomeado. A resolução de ponto de extremidade de serviço requer a identificação de uma partição específica de uma instância de serviço específica. Ao resolver um ponto de extremidade de um serviço, o nome da instância de serviço (por exemplo, `fabric:/myapp/myservice`), bem como a partição específica do serviço, deve ser especificado, exceto no caso de partição singleton.

O gerenciamento de API do Azure pode ser usado com qualquer combinação de serviços sem estado, serviços com estado e qualquer esquema de particionamento.

## <a name="send-traffic-to-a-stateless-service"></a>Enviar tráfego para um serviço sem estado

No caso mais simples, o tráfego é encaminhado para uma instância de serviço sem estado. Para fazer isso, uma operação de gerenciamento de API contém uma política de processamento de entrada com um back-end Service Fabric que mapeia para uma instância de serviço sem estado específica no back-end de Service Fabric. As solicitações enviadas para esse serviço são enviadas a uma instância aleatória do serviço.

#### <a name="example"></a>Exemplo
No cenário a seguir, um aplicativo Service Fabric contém um serviço sem estado chamado `fabric:/app/fooservice`, que expõe uma API HTTP interna. O nome da instância de serviço é bem conhecido e pode ser embutido em código diretamente na política de processamento de entrada do gerenciamento de API. 

![Service Fabric com visão geral da topologia de gerenciamento de API do Azure][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Enviar tráfego para um serviço com estado

Semelhante ao cenário de serviço sem estado, o tráfego pode ser encaminhado para uma instância de serviço com estado. Nesse caso, uma operação de gerenciamento de API contém uma política de processamento de entrada com um back-end Service Fabric que mapeia uma solicitação para uma partição específica de uma instância específica de serviço com *estado* . A partição para mapear cada solicitação é calculada por meio de um método Lambda usando alguma entrada da solicitação HTTP de entrada, como um valor no caminho da URL. A política pode ser configurada para enviar solicitações somente para a réplica primária ou para uma réplica aleatória para operações de leitura.

#### <a name="example"></a>Exemplo

No cenário a seguir, um aplicativo Service Fabric contém um serviço com estado particionado chamado `fabric:/app/userservice` que expõe uma API HTTP interna. O nome da instância de serviço é bem conhecido e pode ser embutido em código diretamente na política de processamento de entrada do gerenciamento de API.  

O serviço é particionado usando o esquema de partição Int64 com duas partições e um intervalo de chaves que abrange `Int64.MinValue` para `Int64.MaxValue`. A política de back-end computa uma chave de partição dentro desse intervalo convertendo o valor de `id` fornecido no caminho de solicitação de URL para um inteiro de 64 bits, embora qualquer algoritmo possa ser usado aqui para computar a chave de partição. 

![Service Fabric com visão geral da topologia de gerenciamento de API do Azure][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Enviar tráfego para vários serviços sem estado

Em cenários mais avançados, você pode definir uma operação de gerenciamento de API que mapeia solicitações para mais de uma instância de serviço. Nesse caso, cada operação contém uma política que mapeia solicitações para uma instância de serviço específica com base em valores da solicitação HTTP de entrada, como o caminho da URL ou a cadeia de caracteres de consulta, e no caso de serviços com estado, uma partição dentro da instância do serviço. 

Para conseguir isso, uma operação de gerenciamento de API contém uma política de processamento de entrada com um back-end Service Fabric que mapeia para uma instância de serviço sem estado no Service Fabric back-end com base nos valores recuperados da solicitação HTTP de entrada. As solicitações para um serviço são enviadas para uma instância aleatória do serviço.

#### <a name="example"></a>Exemplo

Neste exemplo, uma nova instância de serviço sem estado é criada para cada usuário de um aplicativo com um nome gerado dinamicamente usando a seguinte fórmula:
 
- `fabric:/app/users/<username>`

  Cada serviço tem um nome exclusivo, mas os nomes não são conhecidos antes porque os serviços são criados em resposta à entrada do usuário ou do administrador e, portanto, não podem ser embutidos em código em políticas de APIM ou regras de roteamento. Em vez disso, o nome do serviço para o qual enviar uma solicitação é gerado na definição de política de back-end do valor `name` fornecido no caminho de solicitação de URL. Por exemplo:

  - Uma solicitação para `/api/users/foo` é roteada para a instância de serviço `fabric:/app/users/foo`
  - Uma solicitação para `/api/users/bar` é roteada para a instância de serviço `fabric:/app/users/bar`

![Service Fabric com visão geral da topologia de gerenciamento de API do Azure][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Enviar tráfego para vários serviços com estado

Semelhante ao exemplo de serviço sem estado, uma operação de gerenciamento de API pode mapear solicitações para mais de uma instância de serviço com **estado** ; nesse caso, você também pode precisar executar a resolução de partição para cada instância de serviço com estado.

Para conseguir isso, uma operação de gerenciamento de API contém uma política de processamento de entrada com um back-end Service Fabric que mapeia para uma instância de serviço com estado no Service Fabric back-end com base nos valores recuperados da solicitação HTTP de entrada. Além de mapear uma solicitação para uma instância de serviço específica, a solicitação também pode ser mapeada para uma partição específica dentro da instância de serviço e, opcionalmente, para a réplica primária ou uma réplica secundária aleatória dentro da partição.

#### <a name="example"></a>Exemplo

Neste exemplo, uma nova instância de serviço com estado é criada para cada usuário do aplicativo com um nome gerado dinamicamente usando a seguinte fórmula:
 
- `fabric:/app/users/<username>`

  Cada serviço tem um nome exclusivo, mas os nomes não são conhecidos antes porque os serviços são criados em resposta à entrada do usuário ou do administrador e, portanto, não podem ser embutidos em código em políticas de APIM ou regras de roteamento. Em vez disso, o nome do serviço para o qual enviar uma solicitação é gerado na definição de política de back-end do valor `name` forneceu o caminho de solicitação de URL. Por exemplo:

  - Uma solicitação para `/api/users/foo` é roteada para a instância de serviço `fabric:/app/users/foo`
  - Uma solicitação para `/api/users/bar` é roteada para a instância de serviço `fabric:/app/users/bar`

Cada instância de serviço também é particionada usando o esquema de partição Int64 com duas partições e um intervalo de chaves que abrange `Int64.MinValue` para `Int64.MaxValue`. A política de back-end computa uma chave de partição dentro desse intervalo convertendo o valor de `id` fornecido no caminho de solicitação de URL para um inteiro de 64 bits, embora qualquer algoritmo possa ser usado aqui para computar a chave de partição. 

![Service Fabric com visão geral da topologia de gerenciamento de API do Azure][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Passos seguintes

Siga o [tutorial](service-fabric-tutorial-deploy-api-management.md) para configurar seu primeiro cluster de Service Fabric com o gerenciamento de API e as solicitações de fluxo por meio do gerenciamento de API para seus serviços.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png