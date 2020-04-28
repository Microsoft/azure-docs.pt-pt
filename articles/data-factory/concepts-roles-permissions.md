---
title: Funções e permissões do Azure Data Factory
description: Descreve as funções e permissões necessárias para criar fábricas de dados e para trabalhar com recursos infantis.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 8b7791c5c04e986b30959d2fcae17142fdd8b7ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418325"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Funções e permissões do Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Este artigo descreve as funções necessárias para criar e gerir os recursos da Azure Data Factory, e as permissões concedidas por essas funções.

## <a name="roles-and-requirements"></a>Funções e requisitos

Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser membro das funções *contribuidor* ou *proprietário* ou *administrador* da subscrição do Azure. Para ver as permissões que tem na subscrição, no portal do Azure, selecione o seu nome de utilizador no canto superior direito e, em seguida, selecione **Permissões**. Se tiver acesso a várias subscrições, selecione a subscrição apropriada. 

Para criar e gerir recursos subordinados do Data Factory - incluindo conjuntos de dados, serviços ligados, pipelines, acionadores e runtimes de integração - os requisitos seguintes são aplicáveis:
- Para criar e gerir recursos subordinados no portal do Azure, tem de pertencer à função **Contribuidor do Data Factory** ao nível do grupo de recursos ou superior.
- Para criar e gerir recursos subordinados com o PowerShell ou o SDK, a função **contribuidor** ao nível do grupo de recursos ou superior é suficiente.

Para obter instruções de exemplo sobre como adicionar um utilizador a uma função, veja o artigo [Adicionar funções](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="set-up-permissions"></a>Configurar permissões

Depois de criar uma Fábrica de Dados, poderá querer deixar que outros utilizadores trabalhem com a fábrica de dados. Para dar este acesso a outros utilizadores, tem de os adicionar ao papel de Colaborador da Fábrica de **Dados** incorporado no grupo de recursos que contém a fábrica de dados.

### <a name="scope-of-the-data-factory-contributor-role"></a>Âmbito do papel de Contribuinte da Fábrica de Dados

A adesão à função **de Colaborador da Fábrica** de Dados permite que os utilizadores façam as seguintes coisas:
- Criar, editar e eliminar fábricas de dados e recursos infantis, incluindo conjuntos de dados, serviços ligados, oleodutos, gatilhos e tempos de execução de integração.
- Implementar modelos de Gestor de Recursos. A implantação do Gestor de Recursos é o método de implantação utilizado pela Data Factory no portal Azure.
- Gerencie os alertas de App Insights para uma fábrica de dados.
- Crie bilhetes de apoio.

Para mais informações sobre este papel, consulte o papel de Colaborador da Fábrica de [Dados.](../role-based-access-control/built-in-roles.md#data-factory-contributor)

### <a name="resource-manager-template-deployment"></a>Implementação do modelo do Gestor de Recursos

A função de Colaborador da Fábrica de **Dados,** ao nível do grupo de recursos ou superior, permite que os utilizadores implementem modelos de Gestor de Recursos. Como resultado, os membros da função podem usar modelos do Gestor de Recursos para implementar tanto as fábricas de dados como os seus recursos infantis, incluindo conjuntos de dados, serviços ligados, oleodutos, gatilhos e tempos de execução de integração. No entanto, a adesão a esta função não permite que o utilizador crie outros recursos.

As permissões no Azure Repos e no GitHub são independentes das permissões da Data Factory. Como resultado, um utilizador com permissões de repo que é apenas um membro da função Reader pode editar recursos infantis da Data Factory e comprometer alterações no repo, mas não pode publicar estas alterações.

> [!IMPORTANT]
> A implantação do modelo do Gestor de Recursos com a função **de Colaborador** de Fábrica de Dados não eleva as suas permissões. Por exemplo, se implementar um modelo que cria uma máquina virtual Azure, e não tiver permissão para criar máquinas virtuais, a implementação falha com um erro de autorização.

### <a name="custom-scenarios-and-custom-roles"></a>Cenários personalizados e papéis personalizados

Por vezes, é necessário conceder diferentes níveis de acesso a diferentes utilizadores de fábricas de dados. Por exemplo:
- Pode precisar de um grupo onde os utilizadores só tenham permissões numa fábrica de dados específica.
- Ou pode precisar de um grupo onde os utilizadores só possam monitorizar uma fábrica de dados (ou fábricas) mas não podem modificá-la.

Pode alcançar estes cenários personalizados criando papéis personalizados e atribuindo os utilizadores a essas funções. Para mais informações sobre papéis personalizados, consulte [papéis personalizados no Azure.](..//role-based-access-control/custom-roles.md)

Aqui estão alguns exemplos que demonstram o que pode conseguir com papéis personalizados:

- Deixe um utilizador criar, editar ou eliminar qualquer fábrica de dados num grupo de recursos do portal Azure.

  Atribuir a função de **colaborador da Fábrica** de Dados incorporada ao nível do grupo de recursos para o utilizador. Se pretender permitir o acesso a qualquer fábrica de dados numa subscrição, atribua a função ao nível da subscrição.

- Deixe um utilizador ver (ler) e monitorizar uma fábrica de dados, mas não editá-la ou alterá-la.

  Atribuir a função de **leitor** incorporado no recurso da fábrica de dados para o utilizador.

- Deixe um utilizador editar uma única fábrica de dados no portal Azure.

  Este cenário requer duas atribuições de funções.

  1. Atribuir o papel de **contribuinte** incorporado ao nível da fábrica de dados.
  2. Crie uma função personalizada com a permissão **Microsoft.Resources/implementações/**. Atribuir esta função personalizada ao utilizador ao nível do grupo de recursos.

- Deixe que um utilizador só possa testar a ligação num serviço ligado

    Crie uma função personalizada com permissões para as seguintes ações: **Microsoft.DataFactory/fábricas/getFeatureValue/read** e **Microsoft.DataFactory/fábricas/getDataPlaneAccess/read**. Atribuir esta função personalizada no recurso de fábrica de dados para o utilizador.

- Deixe um utilizador atualizar uma fábrica de dados da PowerShell ou do SDK, mas não no portal Azure.

  Atribuir a função de **contribuinte** incorporado no recurso da fábrica de dados para o utilizador. Esta função permite ao utilizador ver os recursos no portal Azure, mas o utilizador não pode aceder aos botões **Publicar** e **Publicar Todos.**

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre papéis no Azure - [Compreender definições de papéis](../role-based-access-control/role-definitions.md)

- Saiba mais sobre o papel **de colaborador da Fábrica** de Dados - Papel colaborador da fábrica de [dados.](../role-based-access-control/built-in-roles.md#data-factory-contributor)
