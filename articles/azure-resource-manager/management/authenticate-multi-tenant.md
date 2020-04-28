---
title: Autenticar entre inquilinos
description: Descreve como o Gestor de Recursos Azure lida com pedidos de autenticação entre os inquilinos.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75478828"
---
# <a name="authenticate-requests-across-tenants"></a>Autenticar pedidos entre inquilinos

Ao criar uma aplicação multi-arrendatária, poderá ser necessário lidar com pedidos de autenticação de recursos que se encontram em diferentes inquilinos. Um cenário comum é quando uma máquina virtual de um inquilino deve se juntar a uma rede virtual em outro inquilino. O Azure Resource Manager fornece um valor de cabeçalho para armazenar fichas auxiliares para autenticar os pedidos a diferentes inquilinos.

## <a name="header-values-for-authentication"></a>Valores cabeçalhopara autenticação

O pedido tem os seguintes valores cabeçalhode de autenticação:

| Nome do cabeçalho | Descrição | Valor de exemplo |
| ----------- | ----------- | ------------ |
| Autorização | Ficha primária | Token primário do portador &lt;&gt; |
| x-ms-autorização-auxiliar | Fichas auxiliares | Bearer &lt;auxiliar-token1&gt;, EncryptedBearer &lt;auxiliar-token2 &lt;&gt;, Bearer auxiliar-token3&gt; |

O cabeceamento auxiliar pode conter até três fichas auxiliares. 

No código da sua aplicação multi-inquilino, obtenha o símbolo de autenticação para outros inquilinos e guarde-os nos cabeçalhos auxiliares. Todas as fichas devem ser do mesmo utilizador ou aplicação. O utilizador ou aplicação deve ter sido convidado como convidado para os outros inquilinos.

## <a name="processing-the-request"></a>Processamento do pedido

Quando a sua aplicação envia um pedido ao Gestor de Recursos, o pedido é executado sob a identidade do símbolo principal. O símbolo principal deve ser válido e não expirado. Este símbolo deve ser de um inquilino que possa gerir a subscrição.

Quando o pedido refere um recurso de diferentes inquilinos, o Gestor de Recursos verifica as fichas auxiliares para determinar se o pedido pode ser processado. Todas as fichas auxiliares do cabeçalho devem ser válidas e não expiradas. Se alguma ficha estiver expirada, o Gestor de Recursos devolve um código de resposta 401. A resposta inclui a identificação do cliente e identificação do inquilino do símbolo que não é válido. Se o cabeçalho auxiliar contiver um símbolo válido para o inquilino, o pedido de inquilino cruzado é processado.

## <a name="next-steps"></a>Passos seguintes

* Para conhecer pedidos de autenticação, consulte [fluxos de autenticação e cenários de aplicação.](../../active-directory/develop/authentication-flows-app-scenarios.md)
* Para obter mais informações sobre fichas, consulte os tokens de acesso ao [Diretório Ativo azure.](../../active-directory/develop/access-tokens.md)
