---
title: Limitação de pedidos avançada com a Gestão de API do Azure
description: Aprenda a criar e aplicar políticas flexíveis de quotas e limitações de taxas com a Azure API Management.
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
ms.openlocfilehash: 467d9cee74567fc0d19031773415675ae7c51818
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71066758"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitação de pedidos avançada com a Gestão de API do Azure
Ser capaz de acelerar os pedidos de entrada é um papel fundamental da Azure API Management. Quer controlando a taxa de pedidos ou o total de pedidos/dados transferidos, a API Management permite que os fornecedores de API protejam as suas APIs contra abusos e criem valor para diferentes níveis de produto API.

## <a name="product-based-throttling"></a>Estrangulamento à base de produtos
Até à data, as capacidades de estrangulamento da taxa limitaram-se a ser vias para uma determinada subscrição do Produto, definida no portal Azure. Isto é útil para o fornecedor de API aplicar limites aos desenvolvedores que se inscreveram para utilizar a sua API, no entanto, não ajuda, por exemplo, a estrangular utilizadores finais individuais da API. É possível que, para um único utilizador da aplicação do desenvolvedor, consuma toda a quota e, em seguida, impeça que outros clientes do desenvolvedor possam utilizar a aplicação. Além disso, vários clientes que possam gerar um elevado volume de pedidos podem limitar o acesso a utilizadores ocasionais.

## <a name="custom-key-based-throttling"></a>Estrangulamento baseado em chaves personalizadas

> [!NOTE]
> As `rate-limit-by-key` `quota-by-key` e as políticas não estão disponíveis quando no escalão de consumo da Gestão aPI Azure. 

As novas políticas de limite de taxas por chave e [quota-por-chave](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) proporcionam uma solução mais flexível para o controlo do tráfego. [rate-limit-by-key](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) Estas novas políticas permitem definir expressões para identificar as teclas que são usadas para rastrear o uso do tráfego. A forma como isto funciona é mais fácil ilustrado com um exemplo. 

## <a name="ip-address-throttling"></a>Estrangulamento do endereço IP
As seguintes políticas restringem um único endereço IP do cliente a apenas 10 chamadas por minuto, com um total de 1.000.000 chamadas e 10.000 quilobytes de largura de banda por mês. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Se todos os clientes na Internet utilizarem um endereço IP único, esta pode ser uma forma eficaz de limitar o uso por parte do utilizador. No entanto, é provável que vários utilizadores estejam a partilhar um único endereço IP público devido ao acesso à Internet através de um dispositivo NAT. Apesar disso, para APIs que permitem `IpAddress` acesso não autenticado, pode ser a melhor opção.

## <a name="user-identity-throttling"></a>Estrangulamento da identidade do utilizador
Se um utilizador final for autenticado, então uma chave de estrangulamento pode ser gerada com base em informações que identifiquem exclusivamente esse utilizador.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Este exemplo mostra como extrair o cabeçalho de Autorização, convertê-lo em `JWT` objeto e usar o objeto do símbolo para identificar o utilizador e usá-lo como chave de limitação da taxa. Se a identidade do utilizador `JWT` for armazenada como uma das outras reclamações, esse valor poderá ser utilizado no seu lugar.

## <a name="combined-policies"></a>Políticas combinadas
Embora as novas políticas de estrangulamento ofereçam mais controlo do que as políticas de estrangulamento existentes, ainda há valor combinando ambas as capacidades. Estrangular por chave de subscrição do produto ( Taxa de[chamada limite por subscrição](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) e quota de [utilização definida por subscrição](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) é uma ótima maneira de permitir a rentabilização de um API cobrando com base nos níveis de utilização. O controlo mais fino de ser capaz de estrangular pelo utilizador é complementar e impede que o comportamento de um utilizador desgrade a experiência de outro. 

## <a name="client-driven-throttling"></a>Estrangulamento conduzido pelo cliente
Quando a chave de estrangulamento é definida usando uma [expressão de política](/azure/api-management/api-management-policy-expressions), então é o fornecedor de API que está a escolher como o estrangulamento é traçado. No entanto, um desenvolvedor pode querer controlar a forma como eles limitam os seus próprios clientes. Isto poderia ser ativado pelo fornecedor da API introduzindo um cabeçalho personalizado para permitir que a aplicação do cliente do desenvolvedor comunicasse a chave para a API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Isto permite que a aplicação do cliente do desenvolvedor escolha como pretende criar a chave de limitação da taxa. Os desenvolvedores de clientes poderiam criar os seus próprios níveis de tarifa, alocando conjuntos de chaves aos utilizadores e girando a utilização da chave.

## <a name="summary"></a>Resumo
A Azure API Management fornece velocidade e estrangulamento de cotação para proteger e acrescentar valor ao seu serviço API. As novas políticas de estrangulamento com regras de digitalização personalizadas permitem-lhe um controlo mais fino sobre essas políticas para permitir aos seus clientes construir aplicações ainda melhores. Os exemplos deste artigo demonstram o uso destas novas políticas através da produção de chaves de limitação da taxa com endereços IP do cliente, identidade do utilizador e valores gerados pelo cliente. No entanto, existem muitas outras partes da mensagem que podem ser usadas, tais como agente de utilizador, fragmentos de caminho de URL, tamanho da mensagem.

## <a name="next-steps"></a>Passos seguintes
Por favor, dê-nos o seu feedback como um problema GitHub para este tópico. Seria ótimo ouvir falar de outros valores-chave potenciais que têm sido uma escolha lógica nos seus cenários.

