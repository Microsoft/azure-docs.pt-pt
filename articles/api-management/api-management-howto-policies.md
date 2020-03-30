---
title: Políticas na Gestão da API Azure [ Microsoft Docs
description: Saiba como criar, editar e configurar políticas na Gestão da API.
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
ms.openlocfilehash: c10939b50a66cd608d27a71f02d959fbc2380f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072311"
---
# <a name="policies-in-azure-api-management"></a>Políticas na Gestão de API do Azure

Na Azure API Management (APIM), as políticas são uma poderosa capacidade do sistema que permite ao editor alterar o comportamento da API através da configuração. As políticas são uma coleção de Declarações que são executadas sequencialmente a pedido ou resposta de uma API. As Declarações Populares incluem a conversão do formato de XML para JSON e a taxa de chamada limitada para restringir a quantidade de chamadas recebidas de um desenvolvedor. Muitas mais políticas estão disponíveis fora da caixa.

As políticas são aplicadas dentro do portal que se situa entre o consumidor da API e a API gerida. O portal recebe todos os pedidos e geralmente encaminha-os inalterados para a API subjacente. No entanto, uma política pode aplicar alterações tanto ao pedido de entrada como à resposta de saída.

As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como [Fluxo de controlo][Control flow] e [Definir variável][Set variable], baseiam-se em expressões de política. Para obter mais informações, veja [Políticas avançadas][Advanced policies] e [Expressões de política][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Compreender a configuração da política

A definição de política é um simples documento XML que descreve uma sequência de declarações de entrada e saída. O XML pode ser editado diretamente na janela de definição. Uma lista de declarações é fornecida ao direito e as declarações aplicáveis ao âmbito atual são ativadas e destacadas.

Clicar numa declaração ativada adicionará o XML apropriado na localização do cursor na vista de definição. 

> [!NOTE]
> Se a política que pretende acrescentar não estiver ativada, certifique-se de que está no âmbito correto para essa política. Cada declaração política destina-se a ser utilizada em determinados âmbitos e secções políticas. Para rever as secções e âmbitos de política para uma política, verifique a secção **de utilização** dessa política na [Referência Política][Policy Reference].
> 
> 

A configuração é `inbound` `backend`dividida `outbound`em, , e `on-error`. A série de declarações políticas especificadas é executada para um pedido e uma resposta.

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

Se houver um erro durante o processamento de um `inbound` `backend`pedido, `outbound` quaisquer etapas restantes na , `on-error` ou secções são ignoradas e a execução salta para as declarações na secção. Ao colocar declarações `on-error` de política na secção, `context.LastError` pode rever o erro utilizando `set-body` a propriedade, inspecionar e personalizar a resposta de erro utilizando a apólice e configurar o que acontece se ocorrer um erro. Existem códigos de erro para passos incorporados e para erros que podem ocorrer durante o processamento de declarações políticas. Para mais informações, consulte o tratamento de erros nas políticas de [Gestão da API](/azure/api-management/api-management-error-handling-policies).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Como configurar políticas

Para obter informações sobre como configurar políticas, consulte [definir ou editar políticas.](set-edit-policies.md)

## <a name="policy-reference"></a>Referência política

Consulte a [referência política](api-management-policy-reference.md) para obter uma lista completa de declarações políticas e suas configurações.

## <a name="policy-samples"></a>Amostras políticas

Consulte [as amostras de política](policy-samples.md) para obter mais exemplos de código.

## <a name="examples"></a>Exemplos

### <a name="apply-policies-specified-at-different-scopes"></a>Aplicar políticas especificadas em diferentes âmbitos

Se tiver uma política a nível global e uma política configurada para uma API, então sempre que essa API em particular for utilizada, ambas as políticas serão aplicadas. A API Management permite a ordenação determinística de declarações políticas combinadas através do elemento base. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

Na definição de política `cross-domain` de exemplo acima, a declaração executaria `find-and-replace` antes de quaisquer políticas mais elevadas que, por sua vez, seriam seguidas pela política. 

### <a name="restrict-incoming-requests"></a>Restringir os pedidos de entrada

Para adicionar uma nova declaração para restringir os pedidos de entrada a endereços IP `inbound` especificados, coloque o cursor dentro do conteúdo do elemento XML e clique na declaração de IPs de **chamada restrita.**

![Políticas de restrição][policies-restrict]

Isto adicionará um corte XML `inbound` ao elemento que fornece orientações sobre como configurar a declaração.

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

Para mais informações que trabalhem com políticas, consulte:

+ [Transforme APIs](transform-api.md)
+ [Referência política](api-management-policy-reference.md) para uma lista completa de declarações políticas e suas configurações
+ [Amostras políticas](policy-samples.md)   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
