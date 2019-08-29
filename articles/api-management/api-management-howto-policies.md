---
title: Políticas no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como criar, editar e configurar políticas no gerenciamento de API.
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
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072311"
---
# <a name="policies-in-azure-api-management"></a>Políticas no gerenciamento de API do Azure

No gerenciamento de API do Azure (APIM), as políticas são um recurso poderoso do sistema que permite que o editor altere o comportamento da API por meio da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente na solicitação ou resposta de uma API. As instruções populares incluem conversão de formato de XML para JSON e limitação de taxa de chamada para restringir a quantidade de chamadas de entrada de um desenvolvedor. Muitas outras políticas estão disponíveis prontamente.

As políticas são aplicadas dentro do gateway, que reside entre o consumidor da API e a API gerenciada. O gateway recebe todas as solicitações e geralmente as encaminha de forma inalterável para a API subjacente. No entanto, uma política pode aplicar alterações tanto à solicitação de entrada quanto à resposta de saída.

As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como [Fluxo de controlo][Control flow] e [Definir variável][Set variable], baseiam-se em expressões de política. Para obter mais informações, veja [Políticas avançadas][Advanced policies] e [Expressões de política][Policy expressions].

## <a name="sections"> </a>Compreendendo a configuração da política

A definição de política é um documento XML simples que descreve uma sequência de instruções de entrada e saída. O XML pode ser editado diretamente na janela de definição. Uma lista de instruções é fornecida à direita e as instruções aplicáveis ao escopo atual são habilitadas e realçadas.

Clicar em uma instrução habilitada adicionará o XML apropriado no local do cursor na exibição de definição. 

> [!NOTE]
> Se a política que você deseja adicionar não estiver habilitada, verifique se você está no escopo correto para essa política. Cada declaração de política foi projetada para uso em determinados escopos e seções de política. Para examinar as seções de política e os escopos de uma política, verifique a seção **uso** dessa política na [referência de política][Policy Reference].
> 
> 

A configuração é dividida em `inbound`, `backend`, `outbound`e `on-error`. A série de instruções de política especificadas é executada para uma solicitação e uma resposta.

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

Se houver um erro durante o processamento de uma solicitação, as `inbound`etapas restantes nas seções, `backend`ou `outbound` serão ignoradas `on-error` e a execução saltará para as instruções na seção. Ao colocar instruções de política na `on-error` seção, você pode examinar o erro usando a `context.LastError` Propriedade, inspecionar e personalizar a resposta de erro `set-body` usando a política e configurar o que acontece se ocorrer um erro. Há códigos de erro para etapas internas e erros que podem ocorrer durante o processamento de instruções de política. Para obter mais informações, consulte [tratamento de erros em políticas de gerenciamento de API](/azure/api-management/api-management-error-handling-policies).

## <a name="scopes"> </a>Como configurar políticas

Para obter informações sobre como configurar políticas, consulte [definir ou editar políticas](set-edit-policies.md).

## <a name="policy-reference"></a>Referência de política

Consulte a [referência de política](api-management-policy-reference.md) para obter uma lista completa de instruções de política e suas configurações.

## <a name="policy-samples"></a>Exemplos de política

Consulte [exemplos de política](policy-samples.md) para obter mais exemplos de código.

## <a name="examples"></a>Exemplos

### <a name="apply-policies-specified-at-different-scopes"></a>Aplicar políticas especificadas em escopos diferentes

Se você tiver uma política no nível global e uma política configurada para uma API, sempre que essa API específica for usada, ambas as políticas serão aplicadas. O gerenciamento de API permite a ordenação determinística de instruções de política combinadas por meio do elemento base. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

Na definição de política de exemplo acima, `cross-domain` a instrução seria executada antes de `find-and-replace` quaisquer políticas mais altas que, por sua vez, seriam seguidas pela política. 

### <a name="restrict-incoming-requests"></a>Restringir solicitações de entrada

Para adicionar uma nova instrução para restringir as solicitações de entrada a endereços IP especificados, coloque o cursor apenas dentro do conteúdo `inbound` do elemento XML e clique na instrução **restringir IPS de chamador** .

![Políticas de restrição][policies-restrict]

Isso adicionará um trecho XML ao `inbound` elemento que fornece orientação sobre como configurar a instrução.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Para limitar as solicitações de entrada e aceitar somente aquelas de um endereço IP 1.2.3.4, modifique o XML da seguinte maneira:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [APIs de transformação](transform-api.md)
+ [Referência de política](api-management-policy-reference.md) para uma lista completa de instruções de política e suas configurações
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
