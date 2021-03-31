---
title: Tecido de serviço Azure com visão geral da Gestão da API
description: Este artigo é uma introdução à utilização da Azure API Management como porta de entrada para as suas aplicações de Tecido de Serviço.
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: 32f47d62cc9dda7cc88421dbf616bf69ffe152fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575691"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric with Azure API Management overview(Service Fabric com descrição geral da Gestão de API do Azure)

Geralmente, as aplicações da cloud precisam de um gateway de front-end que forneça um único ponto de entrada para utilizadores, dispositivos ou outras aplicações. Em Service Fabric, um gateway pode ser qualquer serviço apátrida, como uma [aplicação Core ASP.NET,](service-fabric-reliable-services-communication-aspnetcore.md)ou outro serviço projetado para entrada de tráfego, como [o Event Hubs,](../event-hubs/index.yml) [IoT Hub,](../iot-hub/index.yml)ou [Azure API Management.](../api-management/index.yml)

Este artigo é uma introdução à utilização da Azure API Management como porta de entrada para as suas aplicações de Tecido de Serviço. A API Management integra-se diretamente com o Service Fabric, permitindo-lhe publicar APIs com um rico conjunto de regras de encaminhamento para os seus serviços de Tecido de Serviço de back-end.

## <a name="availability"></a>Disponibilidade

> [!IMPORTANT]
> Esta funcionalidade está disponível nos níveis **Premium** e **Developer** da API Management devido ao suporte de rede virtual necessário.

## <a name="architecture"></a>Arquitetura

Uma arquitetura de Tecido de Serviço comum usa uma aplicação web de uma página que faz chamadas HTTP para serviços back-end que expõem APIs HTTP. A [aplicação de amostra de tecido de serviço que começa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) mostra um exemplo desta arquitetura.

Neste cenário, um serviço web apátrida serve de porta de entrada para a aplicação Service Fabric. Esta abordagem requer que você escreva um serviço web que pode proxy HTTP solicita a serviços back-end, como mostrado no diagrama seguinte:

![Diagrama que mostra como um serviço web apátrida serve como porta de entrada para a aplicação De Tecido de Serviço.][sf-web-app-stateless-gateway]

À medida que as aplicações crescem em complexidade, também os gateways que devem apresentar uma API em frente a miríades de serviços back-end. A Azure API Management é projetado para lidar com APIs complexos com regras de encaminhamento, controlo de acesso, limitação de tarifas, monitorização, registo de eventos e caching de resposta com o mínimo de trabalho da sua parte. A Azure API Management suporta a descoberta do serviço service Fabric, resolução de divisórias e seleção de réplicas para solicitar inteligentemente os pedidos de back-end em Service Fabric para que não tenha que escrever o seu próprio gateway apátrida API. 

Neste cenário, o UI web ainda é servido através de um serviço web, enquanto as chamadas HTTP API são geridas e encaminhadas através da Azure API Management, como mostrado no seguinte diagrama:

![Diagrama que mostra como a UI web ainda é servida através de um serviço web, enquanto as chamadas HTTP API são geridas e encaminhada através da Azure API Management.][sf-apim-web-app]

## <a name="application-scenarios"></a>Cenários de aplicações

Os serviços em Tecido de Serviço podem ser apátridas ou imponentes, podendo ser divididos usando um de três esquemas: singleton, int-64 range, e nomeado. A resolução do ponto final do serviço requer a identificação de uma divisão específica de uma instância de serviço específica. Ao resolver um ponto final de um serviço, deve especificar-se o nome da instância de serviço (por exemplo, `fabric:/myapp/myservice` ) bem como a partição específica do serviço, exceto no caso da partição singleton.

A Azure API Management pode ser usada com qualquer combinação de serviços apátridas, serviços estatais e qualquer esquema de partição.

## <a name="send-traffic-to-a-stateless-service"></a>Enviar tráfego para um serviço apátrida

No caso mais simples, o tráfego é encaminhado para uma instância de serviço apátrida. Para isso, uma operação de Gestão da API contém uma política de processamento de entrada com um Service Fabric back-end que mapeia para uma instância de serviço apátrida específica no back-end do Service Fabric. Os pedidos enviados para esse serviço são enviados para uma instância aleatória do serviço.

**Exemplo**

No cenário seguinte, uma aplicação Service Fabric contém um serviço apátrida denominado `fabric:/app/fooservice` , que expõe uma API HTTP interna. O nome da instância de serviço é bem conhecido e pode ser codificado diretamente na política de processamento de entrada da API Management. 

![Diagrama que mostra uma aplicação de Tecido de Serviço contém um serviço apátrida que expõe uma API HTTP interna.][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Enviar tráfego para um serviço estatal

À semelhança do cenário de serviço apátrida, o tráfego pode ser encaminhado para uma instância de serviço estatal. Neste caso, uma operação de Gestão da API contém uma política de processamento de entrada com um service fabric back-end que mapeia um pedido para uma divisão específica de uma instância *de* serviço específica. A partição para mapear cada pedido é calculada através de um método lambda usando algumas entradas do pedido HTTP de entrada, como um valor no caminho URL. A política pode ser configurada para enviar pedidos apenas para a réplica primária, ou para uma réplica aleatória para operações de leitura.

**Exemplo**

No cenário seguinte, uma aplicação Service Fabric contém um serviço estatal dividido com o nome `fabric:/app/userservice` que expõe uma API HTTP interna. O nome da instância de serviço é bem conhecido e pode ser codificado diretamente na política de processamento de entrada da API Management.  

O serviço é dividido utilizando o sistema de partição Int64 com duas divisórias e uma gama chave que se estende `Int64.MinValue` até `Int64.MaxValue` . A política de back-end calcula uma chave de partição dentro desse intervalo, convertendo o `id` valor fornecido no caminho de pedido de URL para um inteiro de 64 bits, embora qualquer algoritmo possa ser usado aqui para calcular a chave de partição. 

![Tecido de serviço com topologia de gestão API Azure][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Enviar tráfego para vários serviços apátridas

Em cenários mais avançados, pode definir uma operação de Gestão de API que mapeia pedidos para mais de uma instância de serviço. Neste caso, cada operação contém uma política que mapeia pedidos a uma instância de serviço específica com base em valores do pedido HTTP, como o caminho URL ou cadeia de consulta, e no caso de serviços estatais, uma divisão dentro da instância de serviço.

Para tal, uma operação de Gestão da API contém uma política de processamento de entrada com um Service Fabric back-end que mapeia para uma instância de serviço apátrida no back-end do Service Fabric com base nos valores obtidos a partir do pedido HTTP de entrada. Os pedidos a um serviço são enviados para uma instância aleatória do serviço.

**Exemplo**

Neste exemplo, é criada uma nova instância de serviço apátrida para cada utilizador de uma aplicação com um nome gerado dinamicamente utilizando a seguinte fórmula:

- `fabric:/app/users/<username>`

  Cada serviço tem um nome único, mas os nomes não são conhecidos adiantado porque os serviços são criados em resposta à entrada de utilizador ou administração e, portanto, não podem ser codificados em políticas apim ou regras de encaminhamento. Em vez disso, o nome do serviço para o qual enviar um pedido é gerado na definição de política de back-end a partir do `name` valor fornecido na trajetória de pedido de URL. Por exemplo:

  - Um pedido `/api/users/foo` é encaminhado para a instância de serviço `fabric:/app/users/foo`
  - Um pedido `/api/users/bar` é encaminhado para a instância de serviço `fabric:/app/users/bar`

![Diagrama que mostra um exemplo onde uma nova instância de serviço apátrida é criada para cada utilizador de uma aplicação com um nome gerado dinamicamente.][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Enviar tráfego para vários serviços estatais

À semelhança do exemplo do serviço apátrida, uma operação de Gestão da API pode mapear pedidos para mais de uma instância de serviço **estatal,** caso em que também poderá ter de executar a resolução de partição para cada instância de serviço.

Para tal, uma operação de Gestão da API contém uma política de processamento de entrada com um Service Fabric back-end que mapeia para uma instância de serviço estatal no back-end do Service Fabric com base nos valores recuperados do pedido HTTP de entrada. Além de mapear um pedido para uma instância de serviço específica, o pedido também pode ser mapeado para uma partição específica dentro da instância de serviço, e opcionalmente para a réplica primária ou uma réplica secundária aleatória dentro da partição.

**Exemplo**

Neste exemplo, é criada uma nova instância de serviço estatal para cada utilizador da aplicação com um nome gerado dinamicamente utilizando a seguinte fórmula:

- `fabric:/app/users/<username>`

  Cada serviço tem um nome único, mas os nomes não são conhecidos adiantado porque os serviços são criados em resposta à entrada de utilizador ou administração e, portanto, não podem ser codificados em políticas apim ou regras de encaminhamento. Em vez disso, o nome do serviço para o qual enviar um pedido é gerado na definição de política de back-end a partir do `name` valor fornecido pela trajetória de pedido de URL. Por exemplo:

  - Um pedido `/api/users/foo` é encaminhado para a instância de serviço `fabric:/app/users/foo`
  - Um pedido `/api/users/bar` é encaminhado para a instância de serviço `fabric:/app/users/bar`

Cada instância de serviço também é dividida usando o esquema de partição Int64 com duas divisórias e uma gama chave que se estende `Int64.MinValue` até `Int64.MaxValue` . A política de back-end calcula uma chave de partição dentro desse intervalo, convertendo o `id` valor fornecido no caminho de pedido de URL para um inteiro de 64 bits, embora qualquer algoritmo possa ser usado aqui para calcular a chave de partição. 

![Diagrama que mostra que cada instância de serviço também é dividida usando o esquema de partição Int64 com duas divisórias e uma gama chave que abrange Int64.MinValue a Int64.MaxValue.][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Passos seguintes

Acompanhe o [tutorial](service-fabric-tutorial-deploy-api-management.md) para configurar o seu primeiro cluster de Tecidos de Serviço com API Management e pedidos de fluxo através da API Management para os seus serviços.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
