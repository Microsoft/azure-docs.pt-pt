---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 617608f382d3331e59ae92c9eb272347c736b768
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828817"
---
As funções permitem-lhe utilizar as teclas para dificultar o acesso aos pontos finais da função HTTP durante o desenvolvimento. A menos que o nível de acesso HTTP numa função httpizada esteja definido `anonymous` para, os pedidos devem incluir uma chave de acesso API no pedido. 

Embora as teclas forneçam um mecanismo de segurança predefinido, pode considerar opções adicionais para garantir um ponto final HTTP na produção. Por exemplo, geralmente não é uma boa prática distribuir segredo partilhado em aplicações públicas. Se a sua função estiver a ser chamada de um cliente público, talvez deva considerar a implementação de outro mecanismo de segurança. Para saber mais, consulte [Secure a HTTP endpoint na produção.](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production)

Ao renovar os valores-chave da sua função, deve redistribuir manualmente os valores-chave atualizados para todos os clientes que liguem para a sua função.  

#### <a name="authorization-scopes-function-level"></a>Âmbitos de autorização (nível de função)

Existem dois âmbitos de acesso para as teclas de nível de função:

* **Função**: Estas teclas aplicam-se apenas às funções específicas sob as quais são definidas. Quando usados como uma chave API, estes apenas permitem o acesso a essa função.

* **Anfitrião**: As teclas com um âmbito de anfitrião podem ser utilizadas para aceder a todas as funções dentro da aplicação de funções. Quando utilizados como uma chave API, estes permitem o acesso a qualquer função dentro da aplicação de função. 

Cada tecla é nomeada para referência, e há uma chave padrão (chamada "predefinido") no nível de função e anfitrião. As teclas de função têm precedência sobre as teclas do anfitrião. Quando duas teclas são definidas com o mesmo nome, a tecla de função é sempre utilizada.

#### <a name="master-key-admin-level"></a>Chave-mestra (nível de administração) 

Cada aplicação de função também tem uma chave de anfitrião de nível de administração chamada `_master` . Além de fornecer acesso ao nível do anfitrião a todas as funções da app, a chave principal também fornece acesso administrativo às APIs de rest em tempo de execução. Esta chave não pode ser revogada. Quando definir um nível de acesso de `admin` , os pedidos devem usar a chave principal; qualquer outra chave resulta na falha de acesso.

> [!CAUTION]  
> Devido às permissões elevadas na sua aplicação de função concedida pela chave principal, não deve partilhar esta chave com terceiros ou distribuí-la em aplicações de clientes nativos. Tenha cuidado ao escolher o nível de acesso a administração.
