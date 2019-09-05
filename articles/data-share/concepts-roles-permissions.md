---
title: Funções e requisitos para a visualização do compartilhamento de dados do Azure
description: Funções e requisitos para a visualização do compartilhamento de dados do Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 7bf98f8774551292574d4f1951eba44657fa7de0
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307344"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Funções e requisitos para a visualização do compartilhamento de dados do Azure

Este artigo descreve as funções necessárias para compartilhar dados usando a visualização do compartilhamento de dados do Azure, bem como para aceitar e receber dados usando a visualização do compartilhamento de dados do Azure. 

## <a name="roles-and-requirements"></a>Funções e requisitos

O compartilhamento de dados do Azure usa identidades gerenciadas para serviços do Azure (anteriormente conhecidos como MSIs) para autenticar em contas de armazenamento subjacentes para poder ler os dados a serem compartilhados por um provedor de dados, bem como receber dados compartilhados como um consumidor de dados. Como resultado, não há nenhuma troca de credenciais entre o provedor de dados e o consumidor de dados. 

O Identidade de Serviço Gerenciada precisa receber acesso às contas de armazenamento subjacentes. O serviço de compartilhamento de dados do Azure usa o Identidade de Serviço Gerenciada do recurso de compartilhamento de dados do Azure para ler e gravar dados. O usuário do compartilhamento de dados do Azure precisa da capacidade de criar uma atribuição de função para o Identidade de Serviço Gerenciada à conta de armazenamento da qual eles estão compartilhando dados de/para. A permissão para criar atribuições de função existe na função **proprietário** , na função Administrador de acesso do usuário ou em uma função personalizada com a permissão Microsoft. Authorization/role assignments/Write atribuída. 

Se você não for um proprietário da conta de armazenamento em questão e não for possível criar uma atribuição de função para a identidade gerenciada do recurso de compartilhamento de dados do Azure por conta própria, poderá solicitar que um administrador do Azure crie uma atribuição de função em seu nome. 

Veja abaixo um resumo das funções atribuídas à identidade gerenciada por recursos de compartilhamento de dados:

| |  |  |
|---|---|---|
|**Tipo de armazenamento**|**Conta de armazenamento de origem Provedor de Dados**|**Conta de armazenamento de destino de consumidor de dados**|
|Armazenamento de Blobs do Azure| Leitor de Dados do Armazenamento de Blobs | Contribuinte de Dados do Armazenamento de Blobs
|Azure Data Lake Gen1 | Owner | Não Suportada
|Azure Data Lake Gen2 | Leitor de Dados do Armazenamento de Blobs | Contribuinte de Dados do Armazenamento de Blobs
|
### <a name="data-providers"></a>Provedores de dados 
Para adicionar um DataSet a um compartilhamento de dados do Azure, a identidade gerenciada por recurso de compartilhamento de dados de provedores precisa ser adicionada à função de leitor de dados de blob de armazenamento. Isso é feito automaticamente pelo serviço de compartilhamento de dados do Azure, se o usuário estiver adicionando DataSets via Azure e for um proprietário da conta de armazenamento, ou for membro de uma função personalizada que tenha a permissão Microsoft. Authorization/role/Write atribuída. 

Como alternativa, o usuário pode ter um administrador do Azure para adicionar a identidade gerenciada por recurso de compartilhamento de dados à função de leitor de dados de blob de armazenamento manualmente. Criar essa atribuição de função manualmente pelo administrador deixará de ser um proprietário da conta de armazenamento ou ter uma atribuição de função personalizada. Isso se aplica aos dados que estão sendo compartilhados do armazenamento do Azure ou Azure Data Lake Gen2. 

Se estiver compartilhando dados de Azure Data Lake Gen1, a atribuição de função deverá ser feita à função de proprietário. 

Para criar uma atribuição de função para a identidade gerenciada do recurso de compartilhamento de dados, siga as etapas abaixo:

1. Navegue até a conta de armazenamento.
1. Selecione **controlo de acesso (IAM)** .
1. Selecione **Adicionar uma atribuição de função**.
1. Em *função*, selecione *leitor de dados de blob de armazenamento*.
1. Em *selecionar*, digite o nome da sua conta de compartilhamento de dados do Azure.
1. Clique em *Guardar*.

### <a name="data-consumers"></a>Consumidores de dados
Para receber dados, a identidade gerenciada por recurso de dados de consumidores de dados precisa ser adicionada à função colaborador de dados de blob de armazenamento. Essa função é necessária para habilitar o serviço de compartilhamento de dados do Azure para poder gravar na conta de armazenamento. Isso é feito automaticamente pelo serviço de compartilhamento de dados do Azure, se o usuário estiver adicionando DataSets via Azure e for um proprietário da conta de armazenamento, ou for um membro de uma função personalizada que tenha a permissão Microsoft. Authorization/role/Write atribuída. 

Como alternativa, o usuário pode ter um administrador do Azure para adicionar a identidade gerenciada por recurso de compartilhamento de dados à função de colaborador de dados de blob de armazenamento manualmente. Criar essa atribuição de função manualmente pelo administrador deixará de ser um proprietário da conta de armazenamento ou ter uma atribuição de função personalizada. Observe que isso se aplica aos dados que estão sendo compartilhados para o armazenamento do Azure ou Azure Data Lake Gen2. Não há suporte para o recebimento de dados para Azure Data Lake Gen1. 

Para criar uma atribuição de função para a identidade gerenciada do recurso de compartilhamento de dados manualmente, siga as etapas abaixo:

1. Navegue até a conta de armazenamento.
1. Selecione **controlo de acesso (IAM)** .
1. Selecione **Adicionar uma atribuição de função**.
1. Em *função*, selecione *colaborador de dados de blob de armazenamento*. 
1. Em *selecionar*, digite o nome da sua conta de compartilhamento de dados do Azure.
1. Clique em *Guardar*.

Se você estiver compartilhando dados usando nossas APIs REST, será necessário criar essas atribuições de função manualmente Adicionando a conta de compartilhamento de dados às funções apropriadas. 

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

