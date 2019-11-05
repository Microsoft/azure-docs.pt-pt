---
title: Resolver Problemas do Azure Data Share
description: Saiba como solucionar problemas com convites e erros ao criar ou receber compartilhamentos de dados com o compartilhamento de dados do Azure.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 6ad612d56b25da9e092070198e321e7fca8ad96b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490555"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Solucionar problemas comuns no compartilhamento de dados do Azure 

Este artigo mostra como solucionar problemas comuns do compartilhamento de dados do Azure. 

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

"Erro: a operação retornou um código de status inválido ' BadRequest '"

"Erro: AuthorizationFailed"

"Erro: atribuição de função à conta de armazenamento"

![Erro de privilégio](media/error-write-privilege.png)

Se receber algum dos erros acima ao criar ou receber uma nova partilha de dados, não existem permissões suficientes para a conta de armazenamento. A permissão necessária é *Microsoft. Authorization/Roles/Write*, que existe na função de proprietário de armazenamento ou pode ser atribuída a uma função personalizada. Mesmo se tiver criado a Conta de armazenamento, tal NÃO o torna automaticamente proprietário da mesma. Siga estes passos para conceder a si próprio a propriedade da conta de armazenamento. Como alternativa, uma função personalizada pode ser criada com essa permissão que você pode adicionar a si mesmo no.  

1. Navegue até Conta de armazenamento no portal do Azure
1. Selecione o **controle de acesso (iam)**
1. Clique em **Adicionar**
1. Adicione você mesmo como proprietário.

## <a name="troubleshooting-sql-based-sharing"></a>Solucionando problemas de compartilhamento baseado em SQL

"Erro: os conjuntos de valores x não foram adicionados porque você não tem as permissões necessárias para compartilhar".

Se você receber esse erro ao adicionar um conjunto de dados de uma fonte baseada em SQL, talvez seja porque você não criou um usuário para o Azure data share MSI no seu SQL Server.  Para resolver esse problema, execute o seguinte script:

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Observe que o *< share_acc_name >* é o nome da sua conta de compartilhamento de dados. Se você ainda não criou uma conta de compartilhamento de dados, poderá voltar para esse pré-requisito posteriormente.         

Verifique se você seguiu todos os pré-requisitos listados no tutorial [compartilhar seus dados](share-your-data.md) .

## <a name="next-steps"></a>Passos seguintes

Para saber como começar a compartilhar dados, continue no tutorial [compartilhar seus dados](share-your-data.md) .

