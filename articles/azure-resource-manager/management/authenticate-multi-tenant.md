---
title: Autenticar entre inquilinos
description: Descreve como o Azure Resource Manager lida com pedidos de autenticação entre inquilinos.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75478828"
---
# <a name="authenticate-requests-across-tenants"></a>Autenticar pedidos entre inquilinos

Ao criar uma aplicação multi-arrendatário, poderá ter de lidar com pedidos de autenticação de recursos que se encontram em diferentes inquilinos. Um cenário comum é quando uma máquina virtual em um inquilino deve se juntar a uma rede virtual em outro inquilino. O Azure Resource Manager fornece um valor de cabeçalho para armazenar fichas auxiliares para autenticar os pedidos a diferentes inquilinos.

## <a name="header-values-for-authentication"></a>Valores do cabeçalho para a autenticação

O pedido tem os seguintes valores de cabeçalho de autenticação:

| Nome do cabeçalho | Description | Valor de exemplo |
| ----------- | ----------- | ------------ |
| Autorização | Ficha primária | Token primário do portador &lt;&gt; |
| x-ms-autorização-auxiliar | Fichas auxiliares | Portador &lt; auxiliar-token1 &gt; , CryedBearer &lt; auxiliar-token2, &gt; Bearer &lt; auxiliar-token3&gt; |

O cabeçalho auxiliar pode aguentar até três fichas auxiliares. 

No código da sua app multi-inquilino, obtenha o token de autenticação para outros inquilinos e guarde-os nos cabeçalhos auxiliares. Todos os tokens devem ser do mesmo utilizador ou aplicação. O utilizador ou aplicação deve ter sido convidado como convidado para os outros inquilinos.

## <a name="processing-the-request"></a>Processamento do pedido

Quando a sua aplicação envia um pedido ao Gestor de Recursos, o pedido é executado sob a identidade do token principal. O símbolo primário deve ser válido e não percebido. Este símbolo deve ser de um inquilino que possa gerir a subscrição.

Quando o pedido refere um recurso de um inquilino diferente, o Gestor de Recursos verifica os tokens auxiliares para determinar se o pedido pode ser processado. Todas as fichas auxiliares do cabeçalho devem ser válidas e não úmpidas. Se algum token estiver expirado, o Gestor de Recursos devolve um código de resposta 401. A resposta inclui a identificação do cliente e a identificação do inquilino do token que não é válido. Se o cabeçalho auxiliar contiver um símbolo válido para o inquilino, o pedido de inquilino cruzado é processado.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre pedidos de autenticação, consulte [fluxos de autenticação e cenários de aplicação](../../active-directory/develop/authentication-flows-app-scenarios.md).
* Para obter mais informações sobre fichas, consulte [os tokens de acesso do Azure Ative Directory](../../active-directory/develop/access-tokens.md).
