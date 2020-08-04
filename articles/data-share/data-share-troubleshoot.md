---
title: Resolver Problemas do Azure Data Share
description: Saiba como resolver problemas com convites e erros ao criar ou receber partilhas de dados com a Azure Data Share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/30/2020
ms.openlocfilehash: 0fb2f9dd156d18705308b41ef8d6b015b3b6d71b
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534452"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Resolução de problemas problemas comuns na Azure Data Share 

Este artigo mostra como resolver problemas comuns para a Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Convites Azure Data Share 

Em alguns casos, quando um novo utilizador clica em **Aceitar Convite** a partir do convite por e-mail que foi enviado, eles podem ser apresentados com uma lista vazia de convites. 

![Sem convites](media/no-invites.png)

Isto pode dever-se às seguintes razões:

* **O serviço Azure Data Share não está registado como fornecedor de recursos de qualquer subscrição da Azure no inquilino do Azure.** Você vai experimentar este problema se não houver recurso de Partilha de Dados no seu inquilino Azure. Quando cria um recurso Azure Data Share, regista automaticamente o fornecedor de recursos na sua subscrição Azure. Também pode registar manualmente o serviço Partilha de Dados seguindo estes passos. Terá de ter o papel de Contribuidor Azure para completar estes passos.

    1. No portal Azure, navegue para **Subscrições**
    1. Selecione a subscrição que pretende utilizar para criar o recurso Azure Data Share
    1. Clique em **Fornecedores de Recursos**
    1. Pesquisa rumo **ao Microsoft.DataShare**
    1. Clique **em Registar** 

    Terá de ter o [papel de Azure para](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) completar estes passos. 

* **O convite é enviado para o seu pseudónimo de e-mail em vez do seu e-mail de login Azure.** Se registou o serviço Azure Data Share ou já criou um recurso De partilha de dados no inquilino Azure, mas ainda não consegue ver o convite, talvez porque o fornecedor inseriu o seu pseudónimo de e-mail como destinatário em vez do seu endereço de e-mail de login Azure. Contacte o seu fornecedor de dados e certifique-se de que enviaram o convite para o seu endereço de e-mail de login da Azure e não para o seu pseudónimo de e-mail.

* **O convite já foi aceite.** O link no e-mail leva-o à página data share Invitation no portal Azure, que apenas lista convites pendentes. Se já aceitou o convite, deixará de aparecer na página data share Invitation. Proceda ao seu recurso Data Share que usou para aceitar o convite para visualizar as ações recebidas e configurar a definição do cluster target Azure Data Explorer.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Erro ao criar ou receber uma nova parte

"Falhou em adicionar conjuntos de dados"

"Falhou em mapear conjuntos de dados"

"Incapaz de conceder recursos de partilha de dados x acesso a y"

"Você não tem permissões apropriadas para x"

"Não conseguimos adicionar permissões de escrita para a conta Azure Data Share a um ou mais dos seus recursos selecionados"

Se receber algum dos erros acima referidos ao criar uma nova partilha ou mapeamento de conjuntos de dados, pode ser devido a permissões insuficientes para a loja de dados Azure. Consulte [as funções e os requisitos](concepts-roles-permissions.md) para as permissões necessárias. 

Precisa de escrever permissão para partilhar ou receber dados de uma loja de dados Azure, que normalmente existe na função Contribuinte. 

Se esta for a primeira vez que partilha ou recebe dados da loja de dados Azure, também precisa da *Microsoft.Authorization/role assignments/write* permission, que normalmente existe na função Proprietário. Mesmo que tenha criado o recurso Azure data store, não o torna automaticamente o proprietário do recurso. Com a devida permissão, o serviço Azure Data Share concede automaticamente o acesso de identidade gerido do recurso à loja de dados. Este processo pode levar alguns minutos a fazer efeito. Se sentir falhas devido a este atraso, tente novamente em alguns minutos.

A partilha baseada em SQL requer permissões adicionais. Consulte a partilha baseada em SQL para resolução de problemas para obter detalhes.

## <a name="troubleshooting-sql-based-sharing"></a>Resolução de problemas com a partilha baseada em SQL

"User x não existe na Base de Dados SQL"

Se receber este erro ao adicionar um conjunto de dados a partir de uma fonte baseada em SQL, pode ser porque não criou um utilizador para a identidade gerida Azure Data Share na Base de Dados SQL.  Para resolver esta questão, execute o seguinte script:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Se receber este erro ao mapear o conjunto de dados para um alvo baseado em SQL, pode ser porque não criou um utilizador para a identidade gerida Azure Data Share no seu SQL Server.  Para resolver esta questão, execute o seguinte script:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Note que o *<share_acc_name>* é o nome do seu recurso Data Share.      

Certifique-se de que seguiu todos os pré-requisitos listados na [Partilhar os seus dados](share-your-data.md) e aceitar e receber tutorial [de dados.](subscribe-to-data-share.md)

## <a name="snapshot-failed"></a>O instantâneo falhou
O instantâneo pode falhar devido a uma variedade de razões. Pode encontrar uma mensagem de erro detalhada clicando na hora de início do instantâneo e, em seguida, no estado de cada conjunto de dados. 

Se a mensagem de erro estiver relacionada com a permissão, verifique se o serviço de Partilha de Dados tem a permissão necessária. Consulte [funções e requisitos](concepts-roles-permissions.md) para mais detalhes. Se esta for a primeira vez que está a tirar uma fotografia, pode levar alguns minutos para que o recurso Data Share tenha acesso à loja de dados Azure. Espere alguns minutos e tente de novo.

## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md) 

Para aprender a receber dados, continue a aceitar e receber o tutorial [de dados.](subscribe-to-data-share.md)

