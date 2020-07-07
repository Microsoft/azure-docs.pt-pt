---
title: Limitação de pedidos avançada com a Gestão de API do Azure
description: Saiba como criar e aplicar políticas flexíveis de quota e limitação de taxas com a Azure API Management.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "71066758"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitação de pedidos avançada com a Gestão de API do Azure
Ser capaz de acelerar os pedidos de entrada é um papel fundamental da Azure API Management. Quer controlando a taxa de pedidos ou o total de pedidos/dados transferidos, a API Management permite que os fornecedores de API protejam as suas APIs de abusos e criem valor para diferentes níveis de produtos API.

## <a name="product-based-throttling"></a>Estrangulamento à base de produtos
Até à data, as capacidades de aceleração da taxa limitaram-se a ser definidas para uma determinada subscrição do Produto, definida no portal Azure. Isto é útil para o fornecedor API aplicar limites aos desenvolvedores que se inscreveram para usar a sua API, no entanto, não ajuda, por exemplo, a estrangular os utilizadores finais individuais da API. É possível que para um único utilizador da aplicação do desenvolvedor consuma toda a quota e, em seguida, impeça outros clientes do desenvolvedor de poderem utilizar a aplicação. Além disso, vários clientes que podem gerar um grande volume de pedidos podem limitar o acesso a utilizadores ocasionais.

## <a name="custom-key-based-throttling"></a>Estrangulamento personalizado à base de chaves

> [!NOTE]
> As `rate-limit-by-key` políticas e as políticas não `quota-by-key` estão disponíveis quando estão no nível de Consumo da Azure API Management. 

As novas políticas [de limite de taxas por chave](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) e [quotas-a-chave](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) proporcionam uma solução mais flexível para o controlo do tráfego. Estas novas políticas permitem definir expressões para identificar as chaves que são usadas para rastrear o uso do tráfego. A forma como isto funciona é mais fácil ilustrada com um exemplo. 

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

Se todos os clientes na Internet utilizarem um endereço IP único, esta pode ser uma forma eficaz de limitar o uso pelo utilizador. No entanto, é provável que vários utilizadores estejam a partilhar um único endereço IP público devido ao seu acesso à Internet através de um dispositivo NAT. Apesar disso, para as APIs que permitem o acesso não `IpAddress` autenticado, pode ser a melhor opção.

## <a name="user-identity-throttling"></a>Estrangulamento da identidade do utilizador
Se um utilizador final for autenticado, então uma chave de estrangulamento pode ser gerada com base em informações que identifiquem exclusivamente esse utilizador.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Este exemplo mostra como extrair o cabeçalho de autorização, convertê-lo em `JWT` objeto e usar o objeto do token para identificar o utilizador e usá-lo como a chave limite de taxa. Se a identidade do utilizador for armazenada como `JWT` uma das outras reclamações, esse valor poderá ser utilizado no seu lugar.

## <a name="combined-policies"></a>Políticas combinadas
Embora as novas políticas de estrangulamento proporcionem mais controlo do que as políticas de estrangulamento existentes, ainda há valor combinando ambas as capacidades. O estrangulamento por chave de subscrição de produto[(Taxa de chamada limite por subscrição](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) e [quota de utilização definida por subscrição](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) é uma ótima maneira de permitir a rentabilização de uma API cobrando com base nos níveis de utilização. O controlo mais fino de ser capaz de acelerar pelo utilizador é complementar e impede que o comportamento de um utilizador degrade a experiência de outro. 

## <a name="client-driven-throttling"></a>Estrangulamento conduzido pelo cliente
Quando a chave de estrangulamento é definida usando uma [expressão de política](/azure/api-management/api-management-policy-expressions), então é o provedor da API que está escolhendo como o estrangulamento é definido. No entanto, um desenvolvedor pode querer controlar a forma como classificam limitar os seus próprios clientes. Isto poderia ser ativado pelo provedor da API introduzindo um cabeçalho personalizado para permitir que a aplicação do cliente do desenvolvedor comunicasse a chave à API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Isto permite que a aplicação do cliente do desenvolvedor escolha como eles querem criar a chave de limitação de taxa. Os desenvolvedores de clientes poderiam criar os seus próprios níveis de tarifas, alocando conjuntos de chaves aos utilizadores e rodando a utilização da chave.

## <a name="summary"></a>Resumo
A Azure API Management fornece taxa e cotação para proteger e acrescentar valor ao seu serviço API. As novas políticas de estrangulamento com regras de scoping personalizadas permitem-lhe um controlo mais fino sobre essas políticas para permitir que os seus clientes construam aplicações ainda melhores. Os exemplos deste artigo demonstram a utilização destas novas políticas através da taxa de produção limitando as chaves com endereços IP do cliente, identidade de utilizador e valores gerados pelo cliente. No entanto, existem muitas outras partes da mensagem que poderiam ser usadas como o agente do utilizador, fragmentos de caminhos URL, tamanho da mensagem.

## <a name="next-steps"></a>Passos seguintes
Por favor, dê-nos o seu feedback como uma questão do GitHub para este tópico. Seria ótimo ouvir sobre outros potenciais valores-chave que têm sido uma escolha lógica nos seus cenários.

