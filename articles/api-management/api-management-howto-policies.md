---
title: Políticas em gestão AZure API | Microsoft Docs
description: Saiba como criar, editar e configurar políticas na Gestão da API. Consulte exemplos de código e veja recursos disponíveis adicionais.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 37ac6369790ed526fd923819558863ae84432aed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358121"
---
# <a name="policies-in-azure-api-management"></a>Políticas na Gestão de API do Azure

Na Azure API Management (APIM), as políticas são uma poderosa capacidade do sistema que permite ao editor alterar o comportamento da API através da configuração. As políticas são uma coleção de Declarações que são executadas sequencialmente a pedido ou resposta de uma API. As Declarações Populares incluem a conversão de formato de XML para JSON e a taxa de chamada limitando a quantidade de chamadas recebidas de um desenvolvedor. Muitas mais políticas estão disponíveis fora da caixa.

As políticas são aplicadas dentro do portal que se situa entre o consumidor da API e a API gerida. O gateway recebe todos os pedidos e geralmente reencaminha-os sem acordo para a API subjacente. No entanto, uma política pode aplicar alterações tanto ao pedido de entrada como à resposta de saída.

As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como [Fluxo de controlo][Control flow] e [Definir variável][Set variable], baseiam-se em expressões de política. Para obter mais informações, veja [Políticas avançadas][Advanced policies] e [Expressões de política][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Compreensão da configuração da política

A definição de política é um documento XML simples que descreve uma sequência de declarações de entrada e saída. O XML pode ser editado diretamente na janela de definição. Uma lista de declarações é fornecida ao direito e as declarações aplicáveis ao âmbito atual estão ativadas e destacadas.

Clicar numa declaração ativa adicionará o XML apropriado na localização do cursor na vista de definição. 

> [!NOTE]
> Se a política que pretende adicionar não estiver ativada, certifique-se de que está no âmbito correto dessa política. Cada declaração política foi concebida para ser utilizada em determinados âmbitos e secções políticas. Para rever as secções e âmbitos de política de uma política, consulte a secção **de utilização** para essa política na [Referência Política][Policy Reference].
> 
> 

A configuração é dividida em `inbound` `backend` , , e `outbound` `on-error` . A série de declarações políticas especificadas é executada para um pedido e uma resposta.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Se houver um erro durante o processamento de um pedido, quaisquer `inbound` `backend` passos restantes na secção , ou `outbound` secções são ignoradas e a execução salta para as declarações na `on-error` secção. Ao colocar declarações de política na `on-error` secção, pode rever o erro utilizando a `context.LastError` propriedade, inspecionar e personalizar a resposta de erro utilizando a `set-body` apólice e configurar o que acontece se ocorrer um erro. Existem códigos de erro para etapas incorporadas e para erros que podem ocorrer durante o processamento de declarações políticas. Para obter mais informações, consulte [o tratamento de erros nas políticas de Gestão da API](./api-management-error-handling-policies.md).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Como configurar políticas

Para obter informações sobre como configurar políticas, consulte [políticas definidas ou editar](set-edit-policies.md).

## <a name="policy-reference"></a>Referência política

Consulte a [referência Política](./api-management-policies.md) para obter uma lista completa de declarações políticas e suas definições.

## <a name="policy-samples"></a>Exemplos de Políticas

Consulte [as amostras de política](./policy-reference.md) para mais exemplos de código.

## <a name="examples"></a>Exemplos

### <a name="apply-policies-specified-at-different-scopes"></a>Aplicar políticas especificadas em diferentes âmbitos

Se tiver uma política a nível global e uma política configurada para uma API, então sempre que essa API específica for utilizada, ambas as políticas serão aplicadas. A API Management permite a ordenação determinística de declarações de políticas combinadas através do elemento base. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

Na definição de política de exemplo acima referida, a `cross-domain` declaração seria executada antes de quaisquer políticas mais elevadas que, por sua vez, seriam seguidas pela `find-and-replace` política. 

### <a name="restrict-incoming-requests"></a>Restringir os pedidos de entrada

Para adicionar uma nova declaração para restringir os pedidos de entrada a endereços IP especificados, coloque o cursor dentro do conteúdo do `inbound` elemento XML e clique na declaração **de IPs do chamador Restrict.**

![Políticas de restrição][policies-restrict]

Isto irá adicionar um corte XML ao `inbound` elemento que fornece orientação sobre como configurar a declaração.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Para limitar os pedidos de entrada e aceitar apenas os de um endereço IP de 1.2.3.4 modificar o XML da seguinte forma:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as políticas, consulte:

+ [Transformar APIs](transform-api.md)
+ [Referência política](./api-management-policies.md) para uma lista completa de declarações políticas e suas definições
+ [Exemplos de Políticas](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
