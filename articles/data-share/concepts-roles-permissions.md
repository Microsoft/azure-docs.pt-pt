---
title: Funções e condições do Azure Data Share
description: Saiba mais sobre as permissões necessárias para partilhar e receber dados usando a Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a832c8956f7a3d4f8669209d7ed311e7555e1e75
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644257"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Funções e condições do Azure Data Share 

Este artigo descreve funções e permissões necessárias para partilhar e receber dados usando o serviço Azure Data Share. 

## <a name="roles-and-requirements"></a>Funções e requisitos

Com o serviço Azure Data Share, pode partilhar dados sem trocar credenciais entre o fornecedor de dados e o consumidor. Para a partilha baseada em instantâneos, o serviço Azure Data Share utiliza identidades geridas (anteriormente conhecidas como MSIs) para autenticar na loja de dados Azure. A identidade gerida do recurso Azure Data Share precisa de ter acesso à loja de dados Azure para ler ou escrever dados.

Para partilhar ou receber dados de uma loja de dados Azure, o utilizador precisa, pelo menos, das seguintes permissões. 

* Permissão para escrever na loja de dados Azure. Tipicamente, esta permissão existe no papel **de Contribuinte.**

Para armazenamento e partilha baseada em instantâneos de data lake, você também precisa de permissão para criar uma atribuição de papéis na loja de dados Azure. Normalmente, existe permissão para criar atribuições de funções na função **Proprietário,** função de Administrador de Acesso ao Utilizador ou uma função personalizada com *a Microsoft.Authorization/role assignments/write* permission assigned. Esta permissão não é necessária se a identidade gerida do recurso de partilha de dados já tiver acesso à loja de dados Azure. Abaixo está um resumo das funções atribuídas à identidade gerida do recurso Data Share:

|**Tipo de Loja de Dados**|**Loja de dados do fornecedor de dados**|**Data Consumer Target Data Store**|
|---|---|---|
|Armazenamento de Blobs do Azure| Leitor de Dados do Armazenamento de Blobs | Contribuinte de Dados do Armazenamento de Blobs
|Azure Data Lake Gen1 | Proprietário | Não suportado
|Azure Data Lake Gen2 | Leitor de Dados do Armazenamento de Blobs | Contribuinte de Dados do Armazenamento de Blobs
|

Para a partilha baseada em instantâneos SQL, um utilizador SQL precisa de ser criado a partir de um fornecedor externo na Base de Dados Azure SQL com o mesmo nome que o recurso Azure Data Share. A azure Ative Directy é necessária para criar este utilizador. Abaixo está um resumo da permissão exigida pelo utilizador SQL.

|**Tipo de base de dados SQL**|**Permissão do utilizador do fornecedor de dados SQL**|**Autorização do utilizador sql do consumidor de dados**|
|---|---|---|
|Base de Dados SQL do Azure | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader, db_datawriter, db_ddladmin
|

### <a name="data-provider"></a>Fornecedor de dados
Para armazenamento e partilha baseada em instantâneos de lago de dados, para adicionar um conjunto de dados no Azure Data Share, os dados do fornecedor partilham a identidade gerida do recurso precisa de ter acesso à loja de dados source Azure. Por exemplo, no caso da conta de armazenamento, a identidade gerida do recurso de partilha de dados é concedida à função *de Leitor de Dados blob de armazenamento.* Isto é feito automaticamente pelo serviço Azure Data Share quando o utilizador está a adicionar conjunto de dados através do portal Azure e o utilizador tem a permissão adequada. Por exemplo, o utilizador é proprietário da loja de dados Azure, ou é membro de uma função personalizada que tem a *microsoft.Autorização/atribuições/designação de função/permissão* de escrita atribuída. 

Em alternativa, o utilizador pode ter o proprietário da loja de dados Azure adicionar manualmente a identidade gerida do recurso de partilha de dados à loja de dados Azure. Esta ação só precisa de ser executada uma vez por recurso de partilha de dados. Para criar uma atribuição de funções para a identidade gerida do recurso de partilha de dados manualmente, siga os passos abaixo.  

1. Navegue para a loja de dados Azure.
1. Selecione **Controlo de Acesso (IAM)**.
1. **Selecione Adicionar uma atribuição de função**.
1. Under *Role*, selecione a função na tabela de atribuição de funções acima (por exemplo, para conta de armazenamento, selecione *Storage Blob Data Reader*).
1. Em *Select*, digite o nome do seu recurso Azure Data Share.
1. Clique em *Guardar*.

Para saber mais sobre a atribuição de funções, consulte [as funções de Atribuição de Azure utilizando o portal Azure](../role-based-access-control/role-assignments-portal.md). Se estiver a partilhar dados utilizando APIs REST, pode criar uma atribuição de funções utilizando a API, referindo-se às [funções De Atribuição Azure utilizando a API REST](../role-based-access-control/role-assignments-rest.md). 

Para a partilha baseada em instantâneos SQL, um utilizador SQL precisa de ser criado a partir de um fornecedor externo na Base de Dados SQL com o mesmo nome que o recurso Azure Data Share enquanto se conecta à base de dados SQL utilizando a autenticação do Azure Ative Directory. Este utilizador tem de ser autorizado *db_datareader.* Um script de amostra juntamente com outros pré-requisitos para a partilha baseada em SQL pode ser encontrado na [Partilha da Base de Dados Azure SQL ou do tutorial Azure Synapse Analytics.](how-to-share-from-sql.md) 

### <a name="data-consumer"></a>Consumidor de dados
Para receber dados na conta de armazenamento, os dados dos consumidores partilham a identidade gerida dos recursos de consumo, devendo ser concedido acesso à conta de armazenamento alvo. A identidade gerida do recurso de partilha de dados tem de ser concedida à *função de Contribuinte de Dados do Armazenamento Blob.* Isto é feito automaticamente pelo serviço Azure Data Share se o utilizador especificar uma conta de armazenamento alvo através do portal Azure e o utilizador tiver a devida permissão. Por exemplo, o utilizador é proprietário da conta de armazenamento, ou é membro de uma função personalizada que tem a *microsoft.Autorizações/atribuições de funções/escrever* permissão atribuída. 

Em alternativa, o utilizador pode ter o proprietário da conta de armazenamento adicionar manualmente a identidade gerida do recurso de partilha de dados à conta de armazenamento. Esta ação só precisa de ser executada uma vez por recurso de partilha de dados. Para criar uma atribuição de funções para a identidade gerida do recurso de partilha de dados manualmente, siga os passos abaixo. 

1. Navegue para a loja de dados Azure.
1. Selecione **Controlo de Acesso (IAM)**.
1. **Selecione Adicionar uma atribuição de função**.
1. Under *Role*, selecione a função na tabela de atribuição de funções acima (por exemplo, para conta de armazenamento, selecione *Storage Blob Data Reader*).
1. Em *Select*, digite o nome do seu recurso Azure Data Share.
1. Clique em *Guardar*.

Para saber mais sobre a atribuição de funções, consulte [as funções de Atribuição de Azure utilizando o portal Azure](../role-based-access-control/role-assignments-portal.md). Se estiver a receber dados utilizando APIs REST, pode criar uma atribuição de funções utilizando a API, referindo-se às [funções De Atribuição Azure utilizando a API REST](../role-based-access-control/role-assignments-rest.md). 

Para um alvo baseado em SQL, um utilizador SQL precisa de ser criado a partir de um fornecedor externo na Base de Dados SQL com o mesmo nome que o recurso Azure Data Share enquanto se conecta à base de dados SQL utilizando a autenticação do Azure Ative Directory. Este utilizador precisa de ser autorizado *db_datareader, db_datawriter, db_ddladmin* permissão. Um script de amostra juntamente com outros pré-requisitos para a partilha baseada em SQL pode ser encontrado na [Partilha da Base de Dados Azure SQL ou do tutorial Azure Synapse Analytics.](how-to-share-from-sql.md) 

## <a name="resource-provider-registration"></a>Registo do fornecedor de recursos 

Poderá ter de registar manualmente o fornecedor de recursos Microsoft.DataShare na sua subscrição Azure nos seguintes cenários: 

* Veja o convite da Azure Data Share pela primeira vez no seu inquilino Azure
* Partilhe dados de uma loja de dados Azure numa subscrição Azure diferente do seu recurso Azure Data Share
* Receba dados numa loja de dados Azure numa subscrição Azure diferente do seu recurso Azure Data Share

Siga estes passos para registar o fornecedor de recursos Microsoft.DataShare na sua Subscrição Azure. Precisa de acesso *do Contribuinte* à assinatura Azure para registar o fornecedor de recursos.

1. No portal Azure, navegue para **Subscrições.**
1. Selecione a subscrição que está a usar para Azure Data Share.
1. Clique em **Fornecedores de Recursos.**
1. Procure por Microsoft.DataShare.
1. Clique em **Registar**.
 
Para saber mais sobre o fornecedor de recursos, consulte os [fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre papéis no Azure - [Compreenda as definições de funções do Azure](../role-based-access-control/role-definitions.md)