---
title: Limitação com a API Management do Azure de pedidos avançada
description: Saiba como criar e aplicar quota flexível e a taxa de limitação de políticas de gestão de API do Azure.
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
ms.openlocfilehash: 22c3987121e2ab3479274c89c359c679f5f1135e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087135"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitação com a API Management do Azure de pedidos avançada
A capacidade de limitar os pedidos de entrada é uma função de chave de API Management do Azure. Ou, ao controlar a taxa de pedidos ou os dados/pedidos total transferidos, gestão de API permite que os fornecedores de API proteger suas APIs de abuso e criar valor para diferentes camadas de produto de API.

## <a name="product-based-throttling"></a>Com base em produto limitação
Até o momento, a taxa de capacidades de limitação foram limitadas a que está a ser confinadas a uma subscrição específica do produto, definida no portal do Azure. Isto é útil para o fornecedor de API aplicar limites os desenvolvedores que tem optado por utilizar a sua API, no entanto, ele não ajuda, por exemplo, na limitação de usuários individuais do final da API. É possível que, para o usuário de aplicativo do desenvolvedor para consumir a quota de toda e, em seguida, impedir que outros clientes do desenvolvedor a capacidade de usar o aplicativo único. Além disso, vários clientes que podem gerar um grande volume de pedidos podem limitar o acesso a utilizadores ocasionais.

## <a name="custom-key-based-throttling"></a>Chave personalizada com a base de limitação
A nova [taxa-limit-by-key](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) e [quota-by-key](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) políticas fornecem uma solução mais flexível para controlo de tráfego. Estas novas políticas permitem-lhe definir as expressões para identificar as chaves que são utilizadas para controlar a utilização de tráfego. A maneira como isso funciona é ilustrado mais fácil com um exemplo. 

## <a name="ip-address-throttling"></a>Limitação de endereço IP
As seguintes políticas restringem um endereço IP de cliente único para apenas 10 chamadas a cada minuto, com um total de 1 000 000 chamadas e 10.000 quilobytes de largura de banda por mês. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Se todos os clientes na Internet utilizado um endereço IP exclusivo, isso pode ser uma forma eficaz de limitar a utilização por utilizador. No entanto, é provável que vários utilizadores partilham um único endereço IP público devido a-los a aceder à Internet através de um dispositivo NAT. Apesar disso, para APIs que permitem o acesso não autenticado o `IpAddress` pode ser a melhor opção.

## <a name="user-identity-throttling"></a>Limitação de identidade do utilizador
Se um utilizador final for autenticado, em seguida, uma chave de limitação pode ser gerada com base nas informações que identifica exclusivamente esse utilizador.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Este exemplo mostra como extrair o cabeçalho de autorização, convertê-lo para `JWT` de objeto e usar o assunto do token para identificar o utilizador e utilizá-lo como a taxa de limitação de chave. Se a identidade do usuário é armazenada no `JWT` como uma das outras afirmações, em seguida, esse valor poderia ser usado no seu lugar.

## <a name="combined-policies"></a>Políticas de combinada
Embora as novas políticas de limitação fornecem controle mais do que as políticas de limitação existentes, ainda há a combinação de ambos os recursos de valor. Chave de produto de assinatura de limitação ([limitar taxa de chamadas por subscrição](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) e [definir quota de utilização por subscrição](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) é uma excelente forma de ativar monetizando de uma API através de com base nos níveis de utilização. O melhor controlo detalhado de ser capaz de acelerar por utilizador é complementar e impede que o comportamento de um usuário degradar a experiência de outro. 

## <a name="client-driven-throttling"></a>Cliente controlado por limitação
Quando a chave de limitação é definida usando um [expressão da política](/azure/api-management/api-management-policy-expressions), então, é o fornecedor de API é escolher a forma como a limitação tem um âmbito. No entanto, um desenvolvedor poderá querer controlar a forma como eles limite de velocidade seus próprios clientes. Isso poderia ser habilitado pelo fornecedor de API com a introdução de um cabeçalho personalizado para permitir que a aplicação de cliente do programador comunicar a chave para a API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Isto permite que a aplicação de cliente do programador escolher como pretende criar a taxa de limitação de chave. Os desenvolvedores de cliente podem criar suas próprias camadas de taxa por alocar conjuntos de chaves para os utilizadores e efetuar a rotação de utilização da chave.

## <a name="summary"></a>Resumo
Gestão de API do Azure fornece a taxa de e limitação para proteger e adicionar valor ao seu serviço de API de cotação. As novas políticas de limitação com as regras de escopo personalizadas permitem que melhor controlo detalhado sobre essas políticas para permitir que os clientes podem criar aplicativos ainda melhores. Os exemplos neste artigo demonstram a utilização destas políticas novas por chaves com endereços IP do cliente, a identidade de utilizador e os valores de cliente gerado de limitação de taxas de fabrico. No entanto, existem muitas outras partes da mensagem que poderiam ser usadas como agente de utilizador, fragmentos de caminho de URL, tamanho da mensagem.

## <a name="next-steps"></a>Passos Seguintes
Envie-nos seus comentários no Disqus thread para este tópico. Seria ótimo ouvir outros valores de chave potenciais que tenham sido uma escolha lógica em seus cenários.

