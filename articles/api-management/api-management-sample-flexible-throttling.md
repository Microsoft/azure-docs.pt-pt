---
title: Limitação de solicitação avançada com o gerenciamento de API do Azure
description: Saiba como criar e aplicar políticas flexíveis de limitação de cota e de taxa com o gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 0e7c6fe10467bb68417172dc95fef874d37fc97b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696246"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitação de solicitação avançada com o gerenciamento de API do Azure
Ser capaz de limitar as solicitações de entrada é uma função fundamental do gerenciamento de API do Azure. Controlando a taxa de solicitações ou o total de solicitações/dados transferidos, o gerenciamento de API permite que os provedores de API protejam suas APIs contra abuso e criem valor para diferentes camadas de produto de API.

## <a name="product-based-throttling"></a>Limitação baseada em produto
Até o momento, os recursos de limitação de taxa foram limitados ao escopo de uma assinatura de produto específica, definida no portal do Azure. Isso é útil para que o provedor de API aplique limites aos desenvolvedores que se inscreveram para usar sua API, no entanto, ele não ajuda, por exemplo, na limitação de usuários finais individuais da API. É possível que, para o usuário único do aplicativo do desenvolvedor, consuma toda a cota e, em seguida, impeça que outros clientes do desenvolvedor possam usar o aplicativo. Além disso, vários clientes que podem gerar um alto volume de solicitações podem limitar o acesso a usuários ocasionais.

## <a name="custom-key-based-throttling"></a>Limitação baseada em chave personalizada

> NOTA: A `rate-limit-by-key` política não estará disponível quando estiver na camada de consumo do gerenciamento de API do Azure. 

A nova política [taxa-limite-por-chave](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) e [cota por chave](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) fornecem uma solução mais flexível para o controle de tráfego. Essas novas políticas permitem que você defina expressões para identificar as chaves que são usadas para rastrear o uso do tráfego. A maneira como isso funciona é ilustrada mais fácil com um exemplo. 

## <a name="ip-address-throttling"></a>Limitação de endereço IP
As políticas a seguir restringem um único endereço IP de cliente a apenas 10 chamadas a cada minuto, com um total de 1 milhão chamadas e 10.000 quilobytes de largura de banda por mês. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Se todos os clientes na Internet usavam um endereço IP exclusivo, isso pode ser uma maneira eficaz de limitar o uso pelo usuário. No entanto, é provável que vários usuários compartilhem um único endereço IP público, pois eles acessam a Internet por meio de um dispositivo NAT. Apesar disso, para APIs que permitem acesso não autenticado, `IpAddress` o pode ser a melhor opção.

## <a name="user-identity-throttling"></a>Limitação de identidade do usuário
Se um usuário final for autenticado, uma chave de limitação poderá ser gerada com base nas informações que identificam exclusivamente esse usuário.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Este exemplo mostra como extrair o cabeçalho Authorization, convertê-lo `JWT` em Object e usar o assunto do token para identificar o usuário e usá-lo como a chave de limitação de taxa. Se a identidade do usuário for armazenada `JWT` no como uma das outras declarações, esse valor poderá ser usado em seu lugar.

## <a name="combined-policies"></a>Políticas combinadas
Embora as novas políticas de limitação forneçam mais controle do que as políticas de limitação existentes, ainda há um valor que combina os dois recursos. A limitação por chave de assinatura do produto ([limite a taxa de chamada por assinatura](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) e [definir a cota de uso por assinatura](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) é uma ótima maneira de habilitar a monetização de uma API carregando com base nos níveis de uso. O controle mais detalhado da capacidade de limitação por usuário é complementar e impede que o comportamento de um usuário prejudique a experiência de outro. 

## <a name="client-driven-throttling"></a>Limitação controlada pelo cliente
Quando a chave de limitação é definida usando uma [expressão de política](/azure/api-management/api-management-policy-expressions), ela é o provedor de API que está escolhendo como a limitação tem o escopo. No entanto, um desenvolvedor pode querer controlar como eles limitam seus próprios clientes. Isso pode ser habilitado pelo provedor de API introduzindo um cabeçalho personalizado para permitir que o aplicativo cliente do desenvolvedor comunique a chave à API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Isso permite que o aplicativo cliente do desenvolvedor escolha como deseja criar a chave de limitação de taxa. Os desenvolvedores de cliente podiam criar suas próprias camadas de taxa alocando conjuntos de chaves para usuários e girando o uso da chave.

## <a name="summary"></a>Resumo
O gerenciamento de API do Azure fornece limitação de taxa e cotação para proteger e agregar valor ao serviço de API. As novas políticas de limitação com regras de escopo personalizado permitem um controle mais preciso sobre essas políticas para permitir que seus clientes criem aplicativos ainda melhores. Os exemplos neste artigo demonstram o uso dessas novas políticas pela taxa de fabricação limitando chaves com endereços IP do cliente, identidade do usuário e valores gerados pelo cliente. No entanto, há muitas outras partes da mensagem que poderiam ser usadas como agente do usuário, fragmentos de caminho de URL, tamanho da mensagem.

## <a name="next-steps"></a>Passos Seguintes
Envie-nos seus comentários no thread do Disqus para este tópico. Seria ótimo saber mais sobre outros possíveis valores de chave que foram uma escolha lógica em seus cenários.

