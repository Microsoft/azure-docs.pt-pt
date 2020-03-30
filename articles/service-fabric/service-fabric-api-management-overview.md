---
title: Tecido de serviço Azure com visão geral da API Management
description: Este artigo é uma introdução à utilização da Azure API Management como porta de entrada para as suas aplicações de Tecido de Serviço.
author: vturecek
ms.topic: conceptual
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 2a331715d4e4538cfdda8d958ff549a81b627b79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028546"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric with Azure API Management overview(Service Fabric com descrição geral da Gestão de API do Azure)

Geralmente, as aplicações da cloud precisam de um gateway de front-end que forneça um único ponto de entrada para utilizadores, dispositivos ou outras aplicações. No Tecido de Serviço, um gateway pode ser qualquer serviço apátrida, como uma [aplicação ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), ou outro serviço projetado para ingresso de tráfego, como Hubs de [Eventos,](https://docs.microsoft.com/azure/event-hubs/) [Hub IoT](https://docs.microsoft.com/azure/iot-hub/)ou [Azure API Management.](https://docs.microsoft.com/azure/api-management/)

Este artigo é uma introdução à utilização da Azure API Management como porta de entrada para as suas aplicações de Tecido de Serviço. A API Management integra-se diretamente com o Service Fabric, permitindo-lhe publicar APIs com um conjunto rico de regras de encaminhamento para os seus serviços de tecido de serviço de back-end.

## <a name="availability"></a>Disponibilidade

> [!IMPORTANT]
> Esta funcionalidade está disponível nos níveis **Premium** e **Developer** da API Management devido ao suporte de rede virtual necessário.

## <a name="architecture"></a>Arquitetura

Uma arquitetura comum de tecido de serviço usa uma aplicação web de uma página única que faz chamadas HTTP para serviços back-end que expõem HTTP APIs. A [aplicação de início de amostra](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) de Tecido de Serviço mostra um exemplo desta arquitetura.

Neste cenário, um serviço web apátrida serve de porta de entrada para a aplicação Service Fabric. Esta abordagem requer que escreva um serviço web que possa proxy HTTP solicitar serviços back-end, como mostra o seguinte diagrama:

![Tecido de serviço com visão geral da Gestão da API Azure][sf-web-app-stateless-gateway]

À medida que as aplicações crescem em complexidade, também os gateways que devem apresentar uma API em frente a miríades de serviços back-end. A Azure API Management foi concebida para lidar com APIs complexas com regras de encaminhamento, controlo de acesso, limitação de tarifas, monitorização, registo de eventos e cachede resposta com o mínimo de trabalho da sua parte. A Azure API Management apoia a descoberta do serviço de tecido de serviço, resolução de divisórias e seleção de réplicas para direcionar inteligentemente os pedidos para serviços de back-end em Tecido de Serviço para que não tenha de escrever o seu próprio gateway API apátrida. 

Neste cenário, a UI web ainda é servida através de um serviço web, enquanto as chamadas HTTP API são geridas e encaminhadas através da Azure API Management, como mostra o seguinte diagrama:

![Tecido de serviço com visão geral da Gestão da API Azure][sf-apim-web-app]

## <a name="application-scenarios"></a>Cenários de aplicações

Os serviços em Tecido de Serviço podem ser apátridas ou apátridas, e podem ser divididos usando um de três esquemas: singleton, int-64 range, e nomeado. A resolução do ponto final do serviço requer a identificação de uma partição específica de uma instância de serviço específica. Ao resolver um ponto final de um serviço, tanto `fabric:/myapp/myservice`o nome da instância de serviço (por exemplo, ) como a partição específica do serviço devem ser especificados, exceto no caso da partição singleton.

A Azure API Management pode ser utilizada com qualquer combinação de serviços apátridas, serviços estatais e qualquer esquema de partilha.

## <a name="send-traffic-to-a-stateless-service"></a>Envie tráfego para um serviço apátrida

No caso mais simples, o tráfego é encaminhado para uma instância de serviço apátrida. Para tal, uma operação de Gestão API contém uma política de processamento de entrada com um back-end service Fabric que mapeia para uma instância específica de serviço apátrida no back-end do Tecido de Serviço. Os pedidos enviados para esse serviço são enviados para uma instância aleatória do serviço.

**Exemplo**

No seguinte cenário, uma aplicação Service Fabric `fabric:/app/fooservice`contém um serviço apátrida chamado , que expõe uma API http interna. O nome da instância de serviço é bem conhecido e pode ser codificado diretamente na política de processamento de entrada da API Management. 

![Tecido de serviço com visão geral da Gestão da API Azure][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Envie tráfego para um serviço imponente

À semelhança do cenário de serviço apátrida, o tráfego pode ser encaminhado para uma instância de serviço apátrida. Neste caso, uma operação de Gestão aPI contém uma política de processamento de entrada com um back-end service Fabric que mapeia um pedido para uma partição específica de uma instância de serviço *específica.* A partição para mapear cada pedido é calculada através de um método lambda usando alguma entrada do pedido http que se aproxima, como um valor no caminho url. A política pode ser configurada para enviar pedidos apenas para a réplica primária, ou para uma réplica aleatória para operações de leitura.

**Exemplo**

No seguinte cenário, uma aplicação Service Fabric contém `fabric:/app/userservice` um serviço estatal dividido chamado que expõe uma API http interna. O nome da instância de serviço é bem conhecido e pode ser codificado diretamente na política de processamento de entrada da API Management.  

O serviço é dividido utilizando o esquema de partição Int64 `Int64.MinValue` `Int64.MaxValue`com duas divisórias e uma gama de chaves que se estende até . A política de back-end calcula uma chave de `id` partição dentro desse intervalo, convertendo o valor fornecido no caminho de pedido de URL para um inteiro de 64 bits, embora qualquer algoritmo possa ser usado aqui para calcular a chave de partição. 

![Tecido de serviço com visão geral da Gestão da API Azure][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Envie tráfego para vários serviços apátridas

Em cenários mais avançados, pode definir uma operação de Gestão API que mapeia pedidos para mais do que uma instância de serviço. Neste caso, cada operação contém uma política que mapeia pedidos para uma instância de serviço específica com base em valores do pedido http que se aproxima, como a rota de URL ou a cadeia de consulta, e no caso de serviços estatais, uma partição dentro da instância de serviço.

Para tal, uma operação de Gestão API contém uma política de processamento de entrada com um back-end service Fabric que mapeia para uma instância de serviço apátrida no back-end do Tecido de Serviço com base nos valores recuperados do pedido http que se aproxima. Os pedidos para um serviço são enviados para uma instância aleatória do serviço.

**Exemplo**

Neste exemplo, é criada uma nova instância de serviço apátrida para cada utilizador de uma aplicação com um nome gerado dinamicamente utilizando a seguinte fórmula:

- `fabric:/app/users/<username>`

  Cada serviço tem um nome único, mas os nomes não são conhecidos antecipadamente porque os serviços são criados em resposta à entrada de utilizadores ou administradores e, portanto, não podem ser codificados em políticas de APIM ou regras de encaminhamento. Em vez disso, o nome do serviço para o qual enviar um `name` pedido é gerado na definição de política de back-end a partir do valor fornecido na rota de pedido de URL. Por exemplo:

  - Um pedido `/api/users/foo` é encaminhado para a instância de serviço`fabric:/app/users/foo`
  - Um pedido `/api/users/bar` é encaminhado para a instância de serviço`fabric:/app/users/bar`

![Tecido de serviço com visão geral da Gestão da API Azure][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Envie tráfego para vários serviços estatais

Semelhante ao exemplo de serviço apátrida, uma operação de Gestão API pode mapear pedidos para mais do que uma instância de serviço **audatuosa,** caso em que também poderá ter de realizar resolução de partição para cada instância de serviço audativa.

Para tal, uma operação de Gestão API contém uma política de processamento de entrada com um back-end service Fabric que mapeia para uma instância de serviço imponente no back-end do Tecido de Serviço com base nos valores recuperados do pedido http que se aproxima. Além de mapear um pedido para uma instância de serviço específica, o pedido também pode ser mapeado para uma partição específica dentro da instância de serviço, e opcionalmente para a réplica primária ou uma réplica secundária aleatória dentro da divisória.

**Exemplo**

Neste exemplo, é criada uma nova instância de serviço estatal para cada utilizador da aplicação com um nome gerado dinamicamente utilizando a seguinte fórmula:

- `fabric:/app/users/<username>`

  Cada serviço tem um nome único, mas os nomes não são conhecidos antecipadamente porque os serviços são criados em resposta à entrada de utilizadores ou administradores e, portanto, não podem ser codificados em políticas de APIM ou regras de encaminhamento. Em vez disso, o nome do serviço para o qual enviar um `name` pedido é gerado na definição de política de back-end a partir do valor desde que a rota de pedido de URL. Por exemplo:

  - Um pedido `/api/users/foo` é encaminhado para a instância de serviço`fabric:/app/users/foo`
  - Um pedido `/api/users/bar` é encaminhado para a instância de serviço`fabric:/app/users/bar`

Cada instância de serviço também é dividida utilizando o esquema de partição `Int64.MinValue` Int64 com duas divisórias e uma gama de chaves que se estende até `Int64.MaxValue`. A política de back-end calcula uma chave de `id` partição dentro desse intervalo, convertendo o valor fornecido no caminho de pedido de URL para um inteiro de 64 bits, embora qualquer algoritmo possa ser usado aqui para calcular a chave de partição. 

![Tecido de serviço com visão geral da Gestão da API Azure][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Passos seguintes

Siga o [tutorial](service-fabric-tutorial-deploy-api-management.md) para configurar o seu primeiro cluster de Tecido de Serviço com Gestão API e pedidos de fluxo através da API Management para os seus serviços.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
