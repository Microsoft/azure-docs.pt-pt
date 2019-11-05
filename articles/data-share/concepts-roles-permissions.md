---
title: Funções e condições do Azure Data Share
description: Saiba mais sobre as funções de controle de acesso e os requisitos para provedores de dados e consumidores de dados para compartilhar dados no compartilhamento de dados do Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 34c73a6bd400da076c68f308a2100a0f4569bd04
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490576"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Funções e condições do Azure Data Share 

Este artigo descreve as funções necessárias para compartilhar dados usando o compartilhamento de dados do Azure, bem como para aceitar e receber dados usando o compartilhamento de dados do Azure. 

## <a name="roles-and-requirements"></a>Funções e requisitos

O compartilhamento de dados do Azure usa identidades gerenciadas para serviços do Azure (anteriormente conhecidos como MSIs) para autenticar em contas de armazenamento subjacentes para poder ler os dados a serem compartilhados por um provedor de dados, bem como receber dados compartilhados como um consumidor de dados. Como resultado, não há nenhuma troca de credenciais entre o provedor de dados e o consumidor de dados. 

O Identidade de Serviço Gerenciada precisa receber acesso à conta de armazenamento subjacente ou ao banco de dados SQL. O serviço de compartilhamento de dados do Azure usa o Identidade de Serviço Gerenciada do recurso de compartilhamento de dados do Azure para ler e gravar dados. O usuário do compartilhamento de dados do Azure precisa da capacidade de criar uma atribuição de função para o Identidade de Serviço Gerenciada à conta de armazenamento ou ao banco de dados SQL para o qual eles estão compartilhando o e o. 

No caso do armazenamento, a permissão para criar atribuições de função existe na função **proprietário** , na função Administrador de acesso do usuário ou em uma função personalizada com a permissão Microsoft. Authorization/Roles/Write atribuída. 

Se você não for um proprietário da conta de armazenamento em questão e não for possível criar uma atribuição de função para a identidade gerenciada do recurso de compartilhamento de dados do Azure por conta própria, poderá solicitar que um administrador do Azure crie uma atribuição de função em seu nome. 

Veja abaixo um resumo das funções atribuídas à identidade gerenciada por recursos de compartilhamento de dados:

| |  |  |
|---|---|---|
|**Tipo de armazenamento**|**Provedor de Dados Store**|**Armazenamento de destino de consumidor de dados**|
|Armazenamento de Blobs do Azure| Leitor de dados de blob de armazenamento | Colaborador de dados do blob de armazenamento
|Azure Data Lake Gen1 | Proprietário | Não Suportado
|Azure Data Lake Gen2 | Leitor de dados de blob de armazenamento | Colaborador de dados do blob de armazenamento
|SQL do Azure | dbo | dbo 
|

### <a name="data-providers"></a>Provedores de dados 
Para adicionar um DataSet a um compartilhamento de dados do Azure, a identidade gerenciada por recurso de compartilhamento de dados de provedores precisa ser adicionada à função de leitor de dados de blob de armazenamento. Isso é feito automaticamente pelo serviço de compartilhamento de dados do Azure, se o usuário estiver adicionando DataSets via Azure e for um proprietário da conta de armazenamento, ou for membro de uma função personalizada que tenha a permissão Microsoft. Authorization/role/Write atribuída. 

Como alternativa, o usuário pode ter um administrador do Azure para adicionar a identidade gerenciada por recurso de compartilhamento de dados à função de leitor de dados de blob de armazenamento manualmente. Criar essa atribuição de função manualmente pelo administrador deixará de ser um proprietário da conta de armazenamento ou ter uma atribuição de função personalizada. Isso se aplica aos dados que estão sendo compartilhados do armazenamento do Azure ou Azure Data Lake Gen2. 

Se estiver compartilhando dados de Azure Data Lake Gen1, a atribuição de função deverá ser feita à função de proprietário. 

Para criar uma atribuição de função para a identidade gerenciada do recurso de compartilhamento de dados, siga as etapas abaixo:

1. Navegue até a conta de armazenamento.
1. Selecione **controle de acesso (iam)** .
1. Selecione **Adicionar uma atribuição de função**.
1. Em *função*, selecione *leitor de dados de blob de armazenamento*.
1. Em *selecionar*, digite o nome da sua conta de compartilhamento de dados do Azure.
1. Clique em *Guardar*.

Para fontes baseadas em SQL, um usuário precisa ser criado a partir de um provedor externo no banco de dados SQL no qual os dados estão sendo compartilhados com o mesmo nome que a conta do Azure data share. Um script de exemplo junto com outros pré-requisitos para o compartilhamento baseado em SQL pode ser encontrado no tutorial [compartilhar seus dados](share-your-data.md) . 

### <a name="data-consumers"></a>Consumidores de dados
Para receber dados, a identidade gerenciada por recursos de compartilhamento de dados de consumidores de dados precisa ser adicionada à função de colaborador de dados de armazenamento de BLOB e/ou à função dbo de um banco de dados SQL, se estiver recebendo um dado em um banco de dados SQL. 

No caso do armazenamento, isso é feito automaticamente pelo serviço de compartilhamento de dados do Azure se o usuário estiver adicionando conjuntos de dados por meio do Azure e for um proprietário da conta de armazenamento, ou for um membro de uma função personalizada que tenha a permissão Microsoft. Authorization/role/Write associada. 

Como alternativa, o usuário pode ter um administrador do Azure para adicionar a identidade gerenciada por recurso de compartilhamento de dados à função de colaborador de dados de blob de armazenamento manualmente. Criar essa atribuição de função manualmente pelo administrador deixará de ser um proprietário da conta de armazenamento ou ter uma atribuição de função personalizada. Observe que isso se aplica aos dados que estão sendo compartilhados para o armazenamento do Azure ou Azure Data Lake Gen2. Não há suporte para o recebimento de dados para Azure Data Lake Gen1. 

Para criar uma atribuição de função para a identidade gerenciada do recurso de compartilhamento de dados manualmente, siga as etapas abaixo:

1. Navegue até a conta de armazenamento.
1. Selecione **controle de acesso (iam)** .
1. Selecione **Adicionar uma atribuição de função**.
1. Em *função*, selecione *colaborador de dados de blob de armazenamento*. 
1. Em *selecionar*, digite o nome da sua conta de compartilhamento de dados do Azure.
1. Clique em *Guardar*.

Se você estiver compartilhando dados usando nossas APIs REST, será necessário criar essas atribuições de função manualmente Adicionando a conta de compartilhamento de dados às funções apropriadas. 

Se você estiver recebendo dados em uma fonte baseada em SQL, certifique-se de que um novo usuário seja criado a partir de um provedor externo com o mesmo nome da sua conta de compartilhamento de dados do Azure. Consulte os pré-requisitos no tutorial [aceitar e receber dados](subscribe-to-data-share.md) . 

Para saber mais sobre como adicionar uma atribuição de função, consulte [esta documentação,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) que descreve como adicionar uma atribuição de função a um recurso do Azure. 

## <a name="resource-provider-registration"></a>Registro do provedor de recursos 

Ao aceitar um convite de compartilhamento de dados do Azure, você precisará registrar manualmente o provedor de recursos Microsoft. DataShare em sua assinatura. Siga estas etapas para registrar o provedor de recursos Microsoft. DataShare em sua assinatura do Azure. 

1. No portal do Azure, navegue até **assinaturas**.
1. Selecione a assinatura que você está usando para o compartilhamento de dados do Azure.
1. Clique em **provedores de recursos**.
1. Procure Microsoft. DataShare.
1. Clique em **registrar**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as funções no Azure – [entender as definições de função](../role-based-access-control/role-definitions.md)

