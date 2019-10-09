---
title: Solucionar problemas de visualização do compartilhamento de dados do Azure
description: Saiba como solucionar problemas com convites e erros ao criar ou receber compartilhamentos de dados com a visualização do compartilhamento de dados do Azure.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 592a2d464aed8c39dfd11734beccbd0399d75fd9
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169231"
---
# <a name="troubleshoot-common-issues-in-azure-data-share-preview"></a>Solucionar problemas comuns na visualização do compartilhamento de dados do Azure

Este artigo mostra como solucionar problemas comuns de visualização do compartilhamento de dados do Azure. 

## <a name="azure-data-share-invitations"></a>Convites de compartilhamento de dados do Azure 

Em alguns casos, quando um novo usuário clica em **aceitar convite** do convite por email enviado, ele pode receber uma lista vazia de convites. 

![Nenhum convite](media/no-invites.png)

O erro acima é um problema conhecido com o serviço e está sendo abordado no momento. Como alternativa, siga as etapas abaixo. 

1. Na portal do Azure, navegue até **assinaturas**
1. Selecione a assinatura que você está usando para o compartilhamento de dados do Azure
1. Clique em **provedores de recursos**
1. Procurar Microsoft. DataShare
1. Clique em **registrar**

Você precisará ter a [função de RBAC do colaborador do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) para concluir estas etapas. 

Se você ainda não conseguir ver um convite de compartilhamento de dados, entre em contato com seu provedor de dados e verifique se eles enviaram o convite para seu endereço de email de logon do Azure e *não* seu alias de email. 

> [!IMPORTANT]
> Se você já tiver aceitado um convite de compartilhamento de dados do Azure e encerrado o serviço antes de configurar o armazenamento, siga as instruções detalhadas no guia de instruções [configurar um mapeamento de conjunto](how-to-configure-mapping.md) de dados para saber como concluir a configuração de seu compartilhamento de dado recebido e começar a receber dados. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Erro ao criar ou receber um novo compartilhamento de dados

Ao A operação retornou um código de status inválido ' BadRequest ' "

Ao AuthorizationFailed"

"Erro: atribuição de função à conta de armazenamento"

![Erro de privilégio](media/error-write-privilege.png)

Se receber algum dos erros acima ao criar ou receber uma nova partilha de dados, não existem permissões suficientes para a conta de armazenamento. A permissão necessária é *Microsoft. Authorization/Roles/Write*, que existe na função de proprietário de armazenamento ou pode ser atribuída a uma função personalizada. Mesmo se tiver criado a Conta de armazenamento, tal NÃO o torna automaticamente proprietário da mesma. Siga estes passos para conceder a si próprio a propriedade da conta de armazenamento. Como alternativa, uma função personalizada pode ser criada com essa permissão que você pode adicionar a si mesmo no.  

1. Navegue até Conta de armazenamento no portal do Azure
1. Selecione o **controle de acesso (iam)**
1. Clique em **Adicionar**
1. Adicione você mesmo como proprietário.

## <a name="next-steps"></a>Passos seguintes

Para saber como começar a compartilhar dados, continue no tutorial [compartilhar seus dados](share-your-data.md) .

