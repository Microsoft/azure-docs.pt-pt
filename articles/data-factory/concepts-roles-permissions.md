---
title: Funções e permissões do Azure Data Factory
description: Descreve as funções e permissões necessárias para criar Fábricas de Dados e trabalhar com recursos infantis.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.openlocfilehash: cec5df9a5046e912ab8542c91bde4344affa0925
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364482"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Funções e permissões do Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Este artigo descreve as funções necessárias para criar e gerir os recursos da Azure Data Factory, bem como as permissões concedidas por essas funções.

## <a name="roles-and-requirements"></a>Funções e requisitos

Para criar instâncias data factory, a conta de utilizador que utiliza para iniciar sessão no Azure deve ser membro da função *de contribuinte,* da função *de proprietário* ou *de* administrador da subscrição Azure. Para ver as permissões que tem na subscrição, no portal do Azure, selecione o seu nome de utilizador no canto superior direito e, em seguida, selecione **Permissões**. Se tiver acesso a várias subscrições, selecione a subscrição apropriada. 

Para criar e gerir recursos subordinados do Data Factory - incluindo conjuntos de dados, serviços ligados, pipelines, acionadores e runtimes de integração - os requisitos seguintes são aplicáveis:
- Para criar e gerir recursos infantis no portal Azure, deve pertencer à função de Contribuinte de **Fábrica de Dados** ao nível do **Grupo de Recursos** ou superior.
- Para criar e gerir recursos subordinados com o PowerShell ou o SDK, a função **contribuidor** ao nível do grupo de recursos ou superior é suficiente.

Para obter instruções de exemplo sobre como adicionar um utilizador a uma função, veja o artigo [Adicionar funções](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="set-up-permissions"></a>Configurar permissões

Depois de criar uma Fábrica de Dados, poderá querer que outros utilizadores trabalhem com a fábrica de dados. Para dar este acesso a outros utilizadores, tem de os adicionar à função de Contribuinte de Fábrica de **Dados** incorporada no **Grupo de Recursos** que contém a Fábrica de Dados.

### <a name="scope-of-the-data-factory-contributor-role"></a>Âmbito da função de contribuinte da fábrica de dados

A adesão à função contribuinte da **Data Factory** permite que os utilizadores façam as seguintes coisas:
- Criar, editar e eliminar fábricas de dados e recursos infantis, incluindo conjuntos de dados, serviços ligados, oleodutos, gatilhos e tempos de integração.
- Implementar modelos de Gestor de Recursos. A implementação do Gestor de Recursos é o método de implantação utilizado pela Data Factory no portal Azure.
- Gerir os alertas de App Insights para uma fábrica de dados.
- Crie bilhetes de apoio.

Para obter mais informações sobre esta função, consulte o [papel de Contribuinte da Data Factory.](../role-based-access-control/built-in-roles.md#data-factory-contributor)

### <a name="resource-manager-template-deployment"></a>Implementação do modelo do gestor de recursos

A função **de Contribuinte de Fábrica de Dados,** ao nível do grupo de recursos ou acima, permite que os utilizadores implementem modelos de Gestor de Recursos. Como resultado, os membros da função podem usar modelos de Gestor de Recursos para implementar ambas as fábricas de dados e seus recursos infantis, incluindo conjuntos de dados, serviços ligados, oleodutos, gatilhos e tempos de integração. A adesão a este papel não permite que o utilizador crie outros recursos.

As permissões em Azure Repos e GitHub são independentes das permissões da Data Factory. Como resultado, um utilizador com permissões de repouso que seja apenas um membro da função Reader pode editar recursos infantis data Factory e cometer alterações no repo, mas não pode publicar estas alterações.


> [!IMPORTANT]
> A implementação do modelo do Gestor de Recursos com a função **de Contribuinte de Fábrica de Dados** não eleva as suas permissões. Por exemplo, se implementar um modelo que cria uma máquina virtual Azure e não tiver permissão para criar máquinas virtuais, a implementação falha com um erro de autorização.

   No contexto de publicação, a permissão **Microsoft.DataFactory/fábricas/write** aplica-se aos seguintes modos.
- Essa permissão só é necessária no modo Live quando o cliente modifica os parâmetros globais.
- Essa permissão é sempre necessária no modo Git uma vez que sempre após a publicação do cliente, o objeto de fábrica com o último ID de compromisso precisa de ser atualizado.

### <a name="custom-scenarios-and-custom-roles"></a>Cenários personalizados e funções personalizadas

Por vezes, poderá ser necessário conceder diferentes níveis de acesso para diferentes utilizadores da fábrica de dados. Por exemplo:
- Pode precisar de um grupo onde os utilizadores só tenham permissões numa fábrica de dados específica.
- Ou pode precisar de um grupo onde os utilizadores só possam monitorizar uma fábrica de dados (ou fábricas), mas não possam modificá-la.

Pode alcançar estes cenários personalizados criando funções personalizadas e atribuindo os utilizadores a essas funções. Para obter mais informações sobre funções personalizadas, consulte [as funções personalizadas no Azure.](..//role-based-access-control/custom-roles.md)

Aqui estão alguns exemplos que demonstram o que pode conseguir com funções personalizadas:

- Deixe que um utilizador crie, edite ou elimine qualquer fábrica de dados de um grupo de recursos a partir do portal Azure.

  Atribua o papel de contribuinte incorporado da **Data Factory** ao nível do grupo de recursos para o utilizador. Se pretender permitir o acesso a qualquer fábrica de dados numa subscrição, atribua a função ao nível da subscrição.

- Deixe um utilizador ver (ler) e monitorizar uma fábrica de dados, mas não editá-lo ou alterá-lo.

  Atribua o papel de **leitor** incorporado no recurso de fábrica de dados para o utilizador.

- Deixe um utilizador editar uma única fábrica de dados no portal Azure.

  Este cenário requer duas atribuições de papéis.

  1. Atribua o papel **de contribuinte** incorporado ao nível da fábrica de dados.
  2. Crie um papel personalizado com a permissão  **Microsoft.Resources/implementações/**. Atribua esta função personalizada ao utilizador a nível de grupo de recursos.

- Deixe que um utilizador seja capaz de testar a ligação num serviço ligado ou dados de pré-visualização num conjunto de dados

    Crie uma função personalizada com permissões para as seguintes ações: **Microsoft.DataFactory/factorys/getFeatureValue/read** e **Microsoft.DataFactory/factorys/getDataPlaneAccess/action**. Atribua esta função personalizada no recurso de fábrica de dados para o utilizador.

- Deixe um utilizador atualizar uma fábrica de dados da PowerShell ou da SDK, mas não no portal Azure.

  Atribua o papel **de contribuinte** incorporado no recurso de fábrica de dados para o utilizador. Esta função permite ao utilizador ver os recursos no portal Azure, mas o utilizador não consegue aceder aos botões **Publicar** e **Publicar Todos.**


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre papéis no Azure - [Entenda as definições de funções](../role-based-access-control/role-definitions.md)

- Saiba mais sobre a **função de contribuinte** da Data Factory - [Função contribuidora da Data Factory.](../role-based-access-control/built-in-roles.md#data-factory-contributor)
