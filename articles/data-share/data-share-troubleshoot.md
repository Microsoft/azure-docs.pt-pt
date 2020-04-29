---
title: Resolver Problemas do Azure Data Share
description: Saiba como resolver problemas com convites e erros ao criar ou receber partilhas de dados com a Azure Data Share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 901f2b56bc045dc9a9837dd18b2e6ce7169aa3b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76964231"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Problemas de resolução de problemas em Partilha de Dados do Azure 

Este artigo mostra como resolver problemas comum para a Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Convites azure data share 

Em alguns casos, quando um novo utilizador clica **em Aceitar convite** a partir do convite de e-mail que foi enviado, podem ser apresentados com uma lista vazia de convites. 

![Sem convites](media/no-invites.png)

Isto pode dever-se às seguintes razões:

* **O serviço Azure Data Share não está registado como fornecedor de recursos de qualquer subscrição azure no inquilino Azure.** Você vai experimentar este problema se não houver recurso Data Share no seu inquilino Azure. Quando cria um recurso Azure Data Share, regista automaticamente o fornecedor de recursos na sua subscrição Azure. Também pode registar manualmente o serviço Data Share seguindo estes passos. Terá de ter o papel de Colaborador Azure para completar estes passos.

    1. No portal Azure, navegue para **assinaturas**
    1. Selecione a subscrição que pretende utilizar para criar recurso Azure Data Share
    1. Clique em **Fornecedores de Recursos**
    1. Pesquisa por **Microsoft.DataShare**
    1. Clique **no Registo** 

    Terá de ter o papel rBAC do [Colaborador Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) para completar estes passos. 

* **O convite é enviado para o seu pseudónimo de e-mail em vez do seu e-mail de login Azure.** Se registou o serviço De partilha de dados do Azure ou já criou um recurso Data Share no inquilino Azure, mas ainda não consegue ver o convite, talvez porque o fornecedor tenha introduzido o seu pseudónimo de e-mail como destinatário em vez do seu endereço de e-mail de login Azure. Contacte o seu fornecedor de dados e certifique-se de que enviou o convite para o seu endereço de e-mail Azure e não para o seu pseudónimo de e-mail.

* **O convite já foi aceite.** O link no e-mail leva-o à página data Share Invitation no portal Azure, que apenas lista convites pendentes. Se já aceitou o convite, deixará de aparecer na página de Convite de Partilha de Dados. Dirija-se ao seu recurso Data Share, que usou para aceitar o convite para visualizar as ações recebidas e configurar a definição de cluster do Azure Data Explorer.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Erro ao criar ou receber uma nova parte

"Falhou em adicionar conjuntos de dados"

"Falha no mapa dos conjuntos de dados"

"Incapaz de conceder recurso data Share x acesso a y"

"Você não tem permissões adequadas para x"

"Não pudemos adicionar permissões de escrita para a conta Azure Data Share a um ou mais dos seus recursos selecionados"

Se receber algum dos erros acima referidos ao criar uma nova partilha ou mapear conjuntos de dados, pode ser devido a permissões insuficientes para a loja de dados Azure. Consulte [as funções e os requisitos](concepts-roles-permissions.md) para as permissões necessárias. 

Precisa de uma permissão de escrita para partilhar ou receber dados de uma loja de dados Azure, que normalmente existe na função Contributiva. 

Se esta for a primeira vez que partilha ou recebe dados da loja de dados Azure, também precisa da *Microsoft.Authorization/role assignments/write* permission, que normalmente existe na função Proprietário. Mesmo que tenha criado o recurso da loja de dados Azure, não o torna automaticamente o proprietário do recurso. Com a permissão adequada, o serviço Azure Data Share concede automaticamente o acesso de identidade gerido pelo recurso de dados à loja de dados. Este processo pode levar alguns minutos a fazer efeito. Se sentir falhas devido a este atraso, tente novamente em alguns minutos.

A partilha baseada em SQL requer permissões adicionais. Consulte a partilha baseada em Problemas baseado em SQL para obter detalhes.

## <a name="troubleshooting-sql-based-sharing"></a>Resolução de problemas com a partilha baseada em SQL

"O utilizador x não existe na base de dados SQL"

Se receber este erro ao adicionar um conjunto de dados a partir de uma fonte baseada em SQL, pode ser porque não criou um utilizador para a identidade gerida pela Azure Data Share no seu Servidor SQL.  Para resolver este problema, execute o seguinte script:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Se receber este erro ao mapear dataset para um alvo baseado em SQL, pode ser porque não criou um utilizador para a identidade gerida pela Azure Data Share no seu Servidor SQL.  Para resolver este problema, execute o seguinte script:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Note que o *<share_acc_name>* é o nome do seu recurso Data Share.      

Certifique-se de que seguiu todos os pré-requisitos listados em [Partilhar os seus dados](share-your-data.md) e aceite e receba o tutorial de [dados.](subscribe-to-data-share.md)

## <a name="snapshot-failed"></a>Instantâneo falhado
O instantâneo pode falhar devido a uma variedade de razões. Pode encontrar uma mensagem de erro detalhada clicando na hora de início do instantâneo e, em seguida, no estado de cada conjunto de dados. 

Se a mensagem de erro estiver relacionada com a permissão, verifique se o serviço Data Share tem a permissão necessária. Consulte [papéis e requisitos](concepts-roles-permissions.md) para detalhes. Se esta for a primeira vez que está a tirar uma fotografia, pode levar alguns minutos para que o recurso Data Share tenha acesso à loja de dados Azure. Espere alguns minutos e tente de novo.

## <a name="next-steps"></a>Passos seguintes

Para aprender a começar a partilhar dados, continue a partilhar o seu tutorial de [dados.](share-your-data.md) 

Para aprender a receber dados, continue a aceitar e receber o tutorial de [dados.](subscribe-to-data-share.md)

