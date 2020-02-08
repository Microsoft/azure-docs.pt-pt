---
title: Funções e condições do Azure Data Share
description: Conheça as permissões necessárias para partilhar e receber dados usando a Partilha de Dados do Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087170"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Funções e condições do Azure Data Share 

Este artigo descreve funções e permissões necessárias para partilhar e receber dados através do serviço Azure Data Share. 

## <a name="roles-and-requirements"></a>Funções e requisitos

Com o serviço Azure Data Share, pode partilhar dados sem trocar credenciais entre o fornecedor de dados e o consumidor. O serviço Azure Data Share utiliza identidades geridas (anteriormente conhecidas como MSIs) para autenticar na loja de dados Azure. 

A identidade gerida do recurso Azure Data Share tem de ter acesso à loja de dados Azure. O serviço Azure Data Share utiliza então esta identidade gerida para ler e escrever dados para partilha baseada em instantâneos e estabelecer um link simbólico para a partilha no local. 

Para partilhar ou receber dados de uma loja de dados Azure, o utilizador precisa de pelo menos as seguintes permissões. São necessárias permissões adicionais para a partilha baseada em SQL.
* Permissão para escrever na loja de dados Azure. Tipicamente, esta permissão existe no papel **de Contribuinte.**
* Permissão para criar atribuição de funções na loja de dados Azure. Normalmente, a permissão para criar atribuições de funções existe na função **Proprietário,** função de Administrador de Acesso ao Utilizador, ou uma função personalizada com a Microsoft.Autorizações/atribuições de funções/permissão de escrita atribuídas. Esta permissão não é necessária se a identidade gerida do recurso de partilha de dados já tiver acesso à loja de dados Azure. Consulte a tabela abaixo para obter o papel necessário.

Abaixo está um resumo das funções atribuídas à identidade gerida do recurso Data Share:

| |  |  |
|---|---|---|
|**Tipo de loja de dados**|**Loja de Dados de Fonte do Fornecedor de Dados**|**Data Consumer Target Data Store**|
|Armazenamento de Blobs do Azure| Leitor de dados de blob de armazenamento | Contribuinte de dados blob de armazenamento
|Lago de Dados Azure Gen1 | Proprietário | Não Suportado
|Lago de Dados Azure Gen2 | Leitor de dados de blob de armazenamento | Contribuinte de dados blob de armazenamento
|Servidor Azure SQL | Colaborador do BD SQL | Colaborador do BD SQL
|Azure Data Explorer Cluster | Contribuinte | Contribuinte
|

Para a partilha baseada em SQL, um utilizador SQL precisa de ser criado a partir de um fornecedor externo na base de dados SQL com o mesmo nome do recurso Azure Data Share. Abaixo está um resumo da permissão exigida pelo utilizador SQL.

| |  |  |
|---|---|---|
|**Tipo de base de dados SQL**|**Permissão do utilizador SQL do fornecedor de dados**|**Permissão do utilizador sql do consumidor de dados**|
|Base de Dados SQL do Azure | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics (anteriormente SQL DW) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|


### <a name="data-provider"></a>Fornecedor de dados 
Para adicionar um conjunto de dados na Azure Data Share, a identidade gerida do recurso de dados do fornecedor tem de ter acesso à loja de dados Azure fonte. Por exemplo, no caso da conta de armazenamento, a identidade gerida do recurso de partilha de dados é concedida a função de Leitor de Dados Blob de Armazenamento. 

Isto é feito automaticamente pelo serviço Azure Data Share quando o utilizador está a adicionar dataset via portal Azure e o utilizador tem a permissão adequada. Por exemplo, o utilizador é proprietário da loja de dados Azure, ou é membro de uma função personalizada que tem a Microsoft.Autorizações/atribuições/permissão de escrita atribuídas. 

Em alternativa, o utilizador pode ter o proprietário da loja de dados Azure adicionar manualmente a identidade gerida do recurso de partilha de dados à loja de dados Azure. Esta ação só tem de ser executada uma vez por recurso de partilha de dados.

Para criar uma atribuição de funções para a identidade gerida do recurso de partilha de dados, siga os passos seguintes:

1. Navegue para a loja de dados Azure.
1. Selecione **Controlo de Acesso (IAM)** .
1. Selecione **Adicionar uma atribuição de funções**.
1. Em *Funções*, selecione a função na tabela de atribuição de funções acima (por exemplo, para conta de armazenamento, *selecione Storage Blob Data Reader).*
1. Em *Select*, escreva em nome do seu recurso Azure Data Share.
1. Clique em *Guardar*.

Para fontes baseadas em SQL, para além das etapas acima referidas, um utilizador SQL precisa de ser criado a partir de um fornecedor externo na base de dados SQL com o mesmo nome do recurso Azure Data Share. Este utilizador precisa de ser autorizado *db_datareader.* Um script de amostra juntamente com outros pré-requisitos para a partilha baseado em SQL pode ser encontrado na partilha do seu tutorial de [dados.](share-your-data.md) 

### <a name="data-consumer"></a>Consumidor de dados
Para receber dados, a identidade gerida do recurso de dados dos consumidores tem de ter acesso à loja de dados-alvo Azure. Por exemplo, no caso da conta de armazenamento, a identidade gerida do recurso de partilha de dados é concedida a função de Contribuinte de Dados blob de armazenamento. 

Isto é feito automaticamente pelo serviço Azure Data Share se o utilizador especificar uma loja de dados alvo através do portal Azure e o utilizador tiver a permissão adequada. Por exemplo, o utilizador é proprietário da loja de dados Azure, ou é membro de uma função personalizada que tem a Microsoft.Autorizações/atribuições/permissão de escrita atribuídas. 

Em alternativa, o utilizador pode ter o proprietário da loja de dados Azure adicionar manualmente a identidade gerida do recurso de partilha de dados à loja de dados Azure. Esta ação só tem de ser executada uma vez por recurso de partilha de dados.

Para criar manualmente uma atribuição de funções para a identidade gerida pelo recurso de partilha de dados, siga os passos seguintes:

1. Navegue para a loja de dados Azure.
1. Selecione **Controlo de Acesso (IAM)** .
1. Selecione **Adicionar uma atribuição de funções**.
1. Em *Funções*, selecione a função na tabela de atribuição de funções acima (por exemplo, para conta de armazenamento, *selecione Storage Blob Data Reader).*
1. Em *Select*, escreva em nome do seu recurso Azure Data Share.
1. Clique em *Guardar*.

Para o objetivo baseado em SQL, para além dos passos acima referidos, um utilizador SQL precisa de ser criado a partir de um fornecedor externo na base de dados SQL com o mesmo nome do recurso Azure Data Share. Este utilizador tem de ser autorizado *db_datareader, db_datawriter, db_ddladmin.* Um script de amostra juntamente com outros pré-requisitos para a partilha baseado em SQL pode ser encontrado no tutorial de [dados aceite e receber.](subscribe-to-data-share.md) 

Se estiver a partilhar dados usando APIs REST, precisa de criar manualmente estas atribuições de funções. 

Para saber mais sobre como adicionar uma atribuição de funções, consulte [esta documentação,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). 

## <a name="resource-provider-registration"></a>Registo do fornecedor de recursos 

Para ver o convite da Azure Data Share pela primeira vez no seu inquilino Azure, poderá ter de registar manualmente o fornecedor de recursos Microsoft.DataShare na sua subscrição Azure. Siga estes passos para registar o fornecedor de recursos Microsoft.DataShare na sua Subscrição Azure. Precisa de acesso *do Colaborador* à subscrição do Azure para registar o fornecedor de recursos.

1. No portal Azure, navegue para **assinaturas.**
1. Selecione a subscrição que está a utilizar para a Partilha de Dados do Azure.
1. Clique em **Fornecedores de Recursos**.
1. Pesquisa por Microsoft.DataShare.
1. Clique no **Registo**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre papéis no Azure - [Compreender definições de papéis](../role-based-access-control/role-definitions.md)

