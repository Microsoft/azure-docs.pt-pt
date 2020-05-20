---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 926434d7110877e234888682cb6c946afe3ae685
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648923"
---
As funções permitem-lhe utilizar chaves para dificultar o acesso aos pontos finais da função HTTP durante o desenvolvimento. A menos que o nível de acesso HTTP numa função ativada em HTTP esteja definido, os pedidos devem incluir uma chave de `anonymous` acesso API no pedido. 

#### <a name="authorization-scopes-function-level"></a>Âmbitos de autorização (nível de função)

Existem dois âmbitos de acesso para teclas de nível de função:

* **Função**: Estas teclas aplicam-se apenas às funções específicas em que são definidas. Quando utilizados como chave API, estas apenas permitem o acesso a essa função.

* **Anfitrião**: As teclas com um âmbito de anfitrião podem ser utilizadas para aceder a todas as funções dentro da aplicação de função. Quando utilizados como uma chave API, estes permitem o acesso a qualquer função dentro da aplicação de função. 

Cada chave é nomeada para referência, e há uma chave padrão (chamada "padrão") na função e nível de anfitrião. As teclas de função têm precedência sobre as teclas do hospedeiro. Quando duas teclas são definidas com o mesmo nome, a chave de função é sempre utilizada.

#### <a name="master-key-admin-level"></a>Chave-mesonás (nível de administração) 

Cada aplicação de função também tem uma chave de hospedagem de nível de administração chamada `_master` . Além de proporcionar acesso ao nível do anfitrião a todas as funções da aplicação, a chave principal também fornece acesso administrativo às APIs de repouso de tempo de execução. Esta chave não pode ser revogada. Quando definir um nível de acesso de `admin` , os pedidos devem usar a chave principal; qualquer outra chave resulta em falha de acesso.

> [!CAUTION]  
> Devido às permissões elevadas na sua app de funções concedidas pela chave principal, não deve partilhar esta chave com terceiros ou distribuí-la em aplicações de clientes nativos. Tenha cuidado ao escolher o nível de acesso à administração.
