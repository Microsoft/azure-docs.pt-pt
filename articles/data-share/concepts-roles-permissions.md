---
title: Funções e condições do Azure Data Share
description: Saiba mais sobre as permissões necessárias para partilhar e receber dados usando a Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 85b680aafd822b80edf543ca39787848129f1930
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322056"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Funções e condições do Azure Data Share 

Este artigo descreve funções e permissões necessárias para partilhar e receber dados usando o serviço Azure Data Share. 

## <a name="roles-and-requirements"></a>Funções e requisitos

Com o serviço Azure Data Share, pode partilhar dados sem trocar credenciais entre o fornecedor de dados e o consumidor. O serviço Azure Data Share utiliza identidades geridas (anteriormente conhecidas como MSIs) para autenticar na loja de dados Azure. 

A identidade gerida do recurso Azure Data Share tem de ter acesso à loja de dados Azure. O serviço Azure Data Share utiliza então esta identidade gerida para ler e escrever dados para partilha baseada em instantâneos e para estabelecer um link simbólico para a partilha no local. 

Para partilhar ou receber dados de uma loja de dados Azure, o utilizador precisa, pelo menos, das seguintes permissões. São necessárias permissões adicionais para a partilha baseada em SQL.

* Permissão para escrever na loja de dados Azure. Tipicamente, esta permissão existe no papel **de Contribuinte.**
* Permissão para criar atribuição de funções na loja de dados Azure. Normalmente, existe permissão para criar atribuições de funções na função **Proprietário,** função de Administrador de Acesso ao Utilizador ou uma função personalizada com a Microsoft.Authorization/role assignments/write permission assigned. Esta permissão não é necessária se a identidade gerida do recurso de partilha de dados já tiver acesso à loja de dados Azure. Consulte a tabela abaixo para obter o papel necessário.

Abaixo está um resumo das funções atribuídas à identidade gerida do recurso Data Share:

| |  |  |
|---|---|---|
|**Tipo de Loja de Dados**|**Loja de dados do fornecedor de dados**|**Data Consumer Target Data Store**|
|Armazenamento de Blobs do Azure| Leitor de dados blob de armazenamento | Colaborador de dados blob de armazenamento
|Azure Data Lake Gen1 | Proprietário | Não suportado
|Azure Data Lake Gen2 | Leitor de dados blob de armazenamento | Colaborador de dados blob de armazenamento
|Azure SQL Server | Colaborador do SQL DB | Colaborador do SQL DB
|Cluster do Azure Data Explorer | Contribuidor | Contribuidor
|

Para a partilha baseada em SQL, um utilizador SQL precisa de ser criado a partir de um fornecedor externo na Base de Dados Azure SQL com o mesmo nome que o recurso Azure Data Share. Abaixo está um resumo da permissão exigida pelo utilizador SQL.

| |  |  |
|---|---|---|
|**Tipo de base de dados SQL**|**Permissão do utilizador do fornecedor de dados SQL**|**Autorização do utilizador sql do consumidor de dados**|
|Base de Dados SQL do Azure | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics (anteriormente SQL DW) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|

### <a name="data-provider"></a>Fornecedor de dados

Para adicionar um conjunto de dados no Azure Data Share, os dados do fornecedor partilham a identidade gerida do recurso, devendo ser concedido acesso à loja de dados Azure. Por exemplo, no caso da conta de armazenamento, a identidade gerida do recurso de partilha de dados é concedida à função de Leitor de Dados blob de armazenamento. 

Isto é feito automaticamente pelo serviço Azure Data Share quando o utilizador está a adicionar conjunto de dados através do portal Azure e o utilizador tem a permissão adequada. Por exemplo, o utilizador é proprietário da loja de dados Azure, ou é membro de uma função personalizada que tem a microsoft.Autorização/atribuições/designação de função/permissão de escrita atribuída. 

Em alternativa, o utilizador pode ter o proprietário da loja de dados Azure adicionar manualmente a identidade gerida do recurso de partilha de dados à loja de dados Azure. Esta ação só precisa de ser executada uma vez por recurso de partilha de dados.

Para criar uma atribuição de papel para a identidade gerida do recurso de partilha de dados, siga os passos abaixo:

1. Navegue para a loja de dados Azure.
1. Selecione **Controlo de Acesso (IAM)**.
1. **Selecione Adicionar uma atribuição de função**.
1. Under *Role*, selecione a função na tabela de atribuição de funções acima (por exemplo, para conta de armazenamento, selecione *Storage Blob Data Reader*).
1. Em *Select*, digite o nome do seu recurso Azure Data Share.
1. Clique em *Guardar*.

Para fontes baseadas em SQL, para além dos passos acima, um utilizador SQL precisa de ser criado a partir de um fornecedor externo na Base de Dados SQL com o mesmo nome que o recurso Azure Data Share. Este utilizador tem de ser autorizado *db_datareader.* Um script de amostra juntamente com outros pré-requisitos para a partilha baseada em SQL pode ser encontrado na partilha do seu tutorial [de dados.](share-your-data.md) 

### <a name="data-consumer"></a>Consumidor de dados
Para receber dados, os dados dos consumidores partilham a identidade gerida dos recursos de recursos necessários para ter acesso à loja de dados target Azure. Por exemplo, no caso da conta de armazenamento, a identidade gerida do recurso de partilha de dados é concedida à função de Contribuinte de Dados de Armazenamento Blob. 

Isto é feito automaticamente pelo serviço Azure Data Share se o utilizador especificar uma loja de dados-alvo através do portal Azure e o utilizador tiver a devida permissão. Por exemplo, o utilizador é proprietário da loja de dados Azure, ou é membro de uma função personalizada que tem a microsoft.Autorização/atribuições/função atribuídas. 

Em alternativa, o utilizador pode ter o proprietário da loja de dados Azure adicionar manualmente a identidade gerida do recurso de partilha de dados à loja de dados Azure. Esta ação só precisa de ser executada uma vez por recurso de partilha de dados.

Para criar uma atribuição de funções para a identidade gerida do recurso de partilha de dados manualmente, siga os passos abaixo:

1. Navegue para a loja de dados Azure.
1. Selecione **Controlo de Acesso (IAM)**.
1. **Selecione Adicionar uma atribuição de função**.
1. Under *Role*, selecione a função na tabela de atribuição de funções acima (por exemplo, para conta de armazenamento, selecione *Storage Blob Data Reader*).
1. Em *Select*, digite o nome do seu recurso Azure Data Share.
1. Clique em *Guardar*.

Para o alvo baseado em SQL, para além dos passos acima, um utilizador SQL precisa de ser criado a partir de um fornecedor externo na Base de Dados SQL com o mesmo nome que o recurso Azure Data Share. Este utilizador precisa de ser autorizado *db_datareader, db_datawriter, db_ddladmin* permissão. Um script de amostra juntamente com outros pré-requisitos para a partilha baseada em SQL pode ser encontrado no tutorial de [aceitação e receber dados.](subscribe-to-data-share.md) 

Se estiver a partilhar dados utilizando APIs REST, tem de criar manualmente estas atribuições de funções. 

Para saber mais sobre como adicionar uma atribuição de funções, consulte [esta documentação,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). 

## <a name="resource-provider-registration"></a>Registo do fornecedor de recursos 

Para ver o convite Azure Data Share pela primeira vez no seu inquilino Azure, poderá ter de registar manualmente o fornecedor de recursos Microsoft.DataShare na sua subscrição Azure. Siga estes passos para registar o fornecedor de recursos Microsoft.DataShare na sua Subscrição Azure. Precisa de acesso *do Contribuinte* à assinatura Azure para registar o fornecedor de recursos.

1. No portal Azure, navegue para **Subscrições.**
1. Selecione a subscrição que está a usar para Azure Data Share.
1. Clique em **Fornecedores de Recursos.**
1. Procure por Microsoft.DataShare.
1. Clique em **Registar**.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre papéis no Azure - [Entenda as definições de funções](../role-based-access-control/role-definitions.md)

