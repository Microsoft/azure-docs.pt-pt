---
title: Autenticar entre locatários-Azure Resource Manager
description: Descreve como o Azure Resource Manager trata as solicitações de autenticação entre locatários.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 625a17156eaf199af0d51151c6fd37769b8f7b4a
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848759"
---
# <a name="authenticate-requests-across-tenants"></a>Autenticar solicitações entre locatários

Ao criar um aplicativo multilocatário, talvez seja necessário manipular solicitações de autenticação para recursos que estão em locatários diferentes. Um cenário comum é quando uma máquina virtual em um locatário deve ingressar em uma rede virtual em outro locatário. Azure Resource Manager fornece um valor de cabeçalho para armazenar tokens auxiliares para autenticar as solicitações para diferentes locatários.

## <a name="header-values-for-authentication"></a>Valores de cabeçalho para autenticação

A solicitação tem os seguintes valores de cabeçalho de autenticação:

| Nome do cabeçalho | Descrição | Valor de exemplo |
| ----------- | ----------- | ------------ |
| Autorização | Token primário | &lt;Token primário do portador&gt; |
| x-ms-authorization-auxiliary | Tokens auxiliares | &lt;&gt; &lt;Portador&gt;auxiliar-token1, EncryptedBearer auxiliar-token2, portador auxiliar-token3 &lt;&gt; |

O cabeçalho auxiliar pode conter até três tokens auxiliares. 

No código do aplicativo multilocatário, obtenha o token de autenticação para outros locatários e armazene-os nos cabeçalhos auxiliares. Todos os tokens devem ser do mesmo usuário ou aplicativo. O usuário ou aplicativo deve ter sido convidado como convidado para os outros locatários.

## <a name="processing-the-request"></a>Processando a solicitação

Quando seu aplicativo envia uma solicitação ao Gerenciador de recursos, a solicitação é executada sob a identidade do token primário. O token primário deve ser válido e não expirado. Esse token deve ser de um locatário que possa gerenciar a assinatura.

Quando a solicitação faz referência a um recurso de um locatário diferente, o Gerenciador de recursos verifica os tokens auxiliares para determinar se a solicitação pode ser processada. Todos os tokens auxiliares no cabeçalho devem ser válidos e não expirados. Se qualquer token tiver expirado, o Resource Manager retornará um código de resposta 401. A resposta inclui a ID do cliente e a ID do locatário do token que não é válido. Se o cabeçalho auxiliar contiver um token válido para o locatário, a solicitação de locatário cruzado será processada.

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre como enviar solicitações de autenticação com as APIs de Azure Resource Manager, confira [usar a API de autenticação do Resource Manager para acessar assinaturas](resource-manager-api-authentication.md).
* Para obter mais informações sobre tokens, consulte [Azure Active Directory tokens de acesso](/azure/active-directory/develop/access-tokens).
