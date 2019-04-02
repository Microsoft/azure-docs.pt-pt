---
title: As políticas de gestão de API do Azure | Documentos da Microsoft
description: Saiba como criar, editar e configurar políticas de gestão de API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 99f756b5415811b3d4c2ee0167f98b31c905df1a
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793685"
---
# <a name="policies-in-azure-api-management"></a>Políticas de gestão de API do Azure

Na gestão de API do Azure (APIM), as políticas são uma funcionalidade poderosa do sistema que permite ao publicador alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou a resposta de uma API. As instruções populares incluem a conversão de formato de XML para JSON e limitação para restringir a quantidade de chamadas recebidas de um desenvolvedor de taxa de chamadas. Muitos mais políticas estão disponíveis de imediato.

As políticas são aplicadas dentro do gateway que fica entre o consumidor de API e a API gerenciada. O gateway recebe todos os pedidos e normalmente reencaminha-os inalterada à API subjacente. No entanto uma política pode aplicar as alterações para a solicitação de entrada e a resposta de saída.

As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como o [controlar o fluxo] [ Control flow] e [definir variável] [ Set variable] políticas baseiam-se em expressões de política. Para obter mais informações, consulte [políticas avançadas] [ Advanced policies] e [expressões de política][Policy expressions].

## <a name="sections"> </a>Configuração da política de compreensão

A definição de política é um documento XML simple, que descreve uma seqüência de instruções de entrada e saídas. O XML pode ser editado diretamente na janela de definição. É fornecida uma lista de instruções para a direita e instruções aplicáveis ao âmbito atual forem ativadas e realçadas.

Clicar numa instrução ativada, irá adicionar o XML adequado no local do cursor na vista de definição. 

> [!NOTE]
> Se a política que pretende adicionar não estiver ativada, certifique-se de que está no âmbito correto para essa política. Cada declaração de política foi concebida para utilização em determinados escopos e seções de política. Para rever as secções de política e os âmbitos para uma política, consulte a **utilização** secção para essa política no [referência de política][Policy Reference].
> 
> 

A configuração está dividida nas `inbound`, `backend`, `outbound`, e `on-error`. A série de instruções de política especificado é executado para que um pedido e uma resposta.

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

Se existir um erro durante o processamento de um pedido, todos os restantes passos no `inbound`, `backend`, ou `outbound` secções são ignoradas e a execução saltará para as instruções no `on-error` secção. Ao colocar as declarações de política no `on-error` secção, pode rever o erro, utilizando o `context.LastError` propriedade, inspecionar e personalizar a utilização de resposta de erro a `set-body` política e configurar o que acontece se ocorrer um erro. Existem códigos de erro para obter os passos incorporados e de erros que possam ocorrer durante o processamento de declarações de política. Para obter mais informações, consulte [tratamento de erros em políticas de gestão de API](/azure/api-management/api-management-error-handling-policies).

## <a name="scopes"> </a>Como configurar políticas

Para obter informações sobre como configurar políticas, consulte [definir ou editar políticas](set-edit-policies.md).

## <a name="policy-reference"></a>Referência de política

Consulte a [referência de política](api-management-policy-reference.md) para uma lista completa das declarações de política e suas configurações.

## <a name="policy-samples"></a>Exemplos de política

Ver [exemplos de política](policy-samples.md) para obter mais exemplos de código.

## <a name="examples"></a>Exemplos

### <a name="apply-policies-specified-at-different-scopes"></a>Aplicar políticas especificadas em escopos diferentes

Se tiver uma política de nível global e uma política configurada para uma API, em seguida, sempre que essa API específica é utilizado ambas as políticas serão aplicadas. Gestão de API permite a ordenação determinística de declarações de política combinado por meio do elemento base. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

Na definição de política de exemplo acima, o `cross-domain` instrução seria executado antes de ser seguida de todas as políticas mais elevadas que seriam, por sua vez, o `find-and-replace` política. 

### <a name="restrict-incoming-requests"></a>Restringir os pedidos recebidos

Para adicionar uma nova declaração para restringir os pedidos recebidos para endereços IP especificados, coloque o cursor apenas entre o conteúdo do `inbound` elemento XML e clique nas **chamador de restringir IPs** instrução.

![Políticas de restrição][policies-restrict]

Isto irá adicionar um fragmento XML para o `inbound` elemento que fornece orientações sobre como configurar a instrução.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Para limitar os pedidos de entrada e aceitar que apenas aqueles de um endereço IP de 1.2.3.4 modificar o XML da seguinte forma:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações para trabalhar com políticas, consulte:

+ [Transforme as APIs](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa das declarações de política e suas configurações
+ [Exemplos de política](policy-samples.md)   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
