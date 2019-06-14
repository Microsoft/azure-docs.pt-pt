---
title: Autenticar em inquilinos - Azure Resource Manager
description: Descreve como o Azure Resource Manager processa pedidos de autenticação em inquilinos.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 6554c05f40f580a6d7ae086e1d09834298f86621
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550772"
---
# <a name="authenticate-requests-across-tenants"></a>Autenticar pedidos em inquilinos

Ao criar uma aplicação multi-inquilino, terá de processar pedidos de autenticação para os recursos que estão em inquilinos diferentes. Um cenário comum é quando uma máquina virtual num inquilino tem de associar uma rede virtual no outro inquilino. O Azure Resource Manager fornece um valor de cabeçalho para armazenar tokens auxiliares para autenticar os pedidos para inquilinos diferentes.

## <a name="header-values-for-authentication"></a>Valores de cabeçalho para autenticação

O pedido tem os seguintes valores de cabeçalho de autenticação:

| Nome do cabeçalho | Descrição | Valor de exemplo |
| ----------- | ----------- | ------------ |
| Autorização | Token primário | Portador &lt;tokens primário&gt; |
| x-ms-authorization-auxiliary | Tokens auxiliares | Bearer &lt;auxiliary-token1&gt;; EncryptedBearer &lt;auxiliary-token2&gt;; Bearer &lt;auxiliary-token3&gt; |

O cabeçalho auxiliar pode conter até três tokens auxiliares. 

No código da sua aplicação multi-inquilino, obter a autenticação de token para os outros inquilinos e armazená-las nos cabeçalhos de auxiliares. Todos os tokens devem pertencer ao mesmo utilizador ou aplicação. O utilizador ou aplicação tem foi convidada a como um convidado para os outros inquilinos.

## <a name="processing-the-request"></a>Processamento do pedido

Quando a aplicação envia um pedido para o Resource Manager, o pedido é executado sob a identidade do token do principal. O token primário tem de ser válido e existe. Este token tem de ser de um inquilino que pode gerir a subscrição.

Quando o pedido referencia um recurso de inquilino diferente, o Resource Manager verifica os tokens auxiliares para determinar se a solicitação pode ser processada. Todos os tokens auxiliares no cabeçalho tem de ser válido e existe. Se qualquer token tiver expirado, o Resource Manager devolve um código de 401 resposta. A resposta inclui o cliente ID e o ID de inquilino do token que não é válido. Se o cabeçalho de auxiliar contém um token válido para o inquilino, o pedido de inquilino cruzada é processado.

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre o envio de pedidos de autenticação com as APIs do Azure Resource Manager, veja [utilize o Gestor de recursos autenticação API para aceder a subscrições](resource-manager-api-authentication.md).
* Para obter mais informações sobre tokens, consulte [tokens de acesso do Azure Active Directory](/azure/active-directory/develop/access-tokens).
