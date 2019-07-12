---
title: Resolver problemas de pré-visualização de partilha de dados do Azure
description: Saiba como resolver problemas com a pré-visualização de partilha de dados do Azure
services: data-share
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: c02f72d6a327c4dcb94ac8844005613cfe316986
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838387"
---
# <a name="troubleshoot-common-issues-in-azure-data-share-preview"></a>Resolver problemas comuns na pré-visualização de partilha de dados do Azure

Este artigo mostra como resolver problemas comuns de pré-visualização de partilha de dados do Azure. 

## <a name="azure-data-share-invitations"></a>Convites de partilha de dados do Azure 

Em alguns casos, quando um novo utilizador clica **aceitar o convite** do convite de e-mail que lhe foi enviado, poderá apresentados com uma lista vazia de convites. 

![Não existem convites](media/no-invites.png)

O erro acima é um problema conhecido com o serviço e está atualmente a ser resolvido. Como solução, siga os passos abaixo. 

1. No portal do Azure, navegue para **subscrições**
1. Selecione a subscrição que está a utilizar para a partilha de dados do Azure
1. Clique em **fornecedores de recursos**
1. Procure Microsoft.DataShare
1. Clique em **registar**

Precisará ter a [função Azure RBAC de contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) para concluir estes passos. 

Se ainda não é possível ver um convite de partilha de dados, contacte o seu fornecedor de dados e certifique-se de que enviou o convite para o seu endereço de email de início de sessão do Azure e *não* seu alias de email. 

> [!IMPORTANT]
> Se já tiver aceite um convite de partilha de dados do Azure e tiver terminado o serviço antes de configurar o armazenamento, siga as instruções detalhadas no [configurar o mapeamento de um conjunto de dados](how-to-configure-mapping.md) guia de procedimentos para saber como concluir a configuração de seu dados recebidos partilham e começar a receber dados. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Erro ao criar ou receber uma nova partilha de dados

"Erro: Operação de devolveu um código de estado inválido 'BadRequest' "

"Erro: AuthorizationFailed"

"Erro: atribuição de função para a conta de armazenamento"

![Erro de privilégio](media/error-write-privilege.png)

Se recebe qualquer um dos erros anteriores ao criar uma nova partilha de dados ou receber uma nova partilha de dados, é porque não existem permissões suficientes para a conta de armazenamento. A permissão necessária é *Microsoft.Authorization/role atribuições/gravação*, que existe na função de proprietário de armazenamento ou podem ser atribuído a uma função personalizada. Mesmo se tiver criado a conta de armazenamento, não automaticamente faz é o proprietário da conta de armazenamento. Siga estes passos para conceder si mesmo proprietário da conta de armazenamento. Em alternativa, uma função personalizada pode ser criada com esta permissão que pode adicionar-se para.  

1. Navegue para a conta de armazenamento no portal do Azure
1. Selecione **controlo de acesso (IAM)**
1. Clique em **adicionar**
1. Adicione-se em como proprietário.

## <a name="next-steps"></a>Passos Seguintes

Para saber como começar a partilha de dados, avance para o [partilhar os seus dados](share-your-data.md) tutorial.

