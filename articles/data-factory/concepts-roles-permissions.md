---
title: Funções e permissões para Azure Data Factory | Microsoft Docs
description: Descreve as funções e permissões necessárias para criar fábricas de dados e trabalhar com recursos filho.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: 9df65322958bffd3182aaa8d734e8b29717d939d
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142531"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Funções e permissões para Azure Data Factory

Este artigo descreve as funções necessárias para criar e gerenciar Azure Data Factory recursos e as permissões concedidas por essas funções.

## <a name="roles-and-requirements"></a>Funções e requisitos

Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser membro das funções *contribuidor* ou *proprietário* ou *administrador* da subscrição do Azure. Para ver as permissões que tem na subscrição, no portal do Azure, selecione o seu nome de utilizador no canto superior direito e, em seguida, selecione **Permissões**. Se tiver acesso a várias subscrições, selecione a subscrição apropriada. 

Para criar e gerir recursos subordinados do Data Factory - incluindo conjuntos de dados, serviços ligados, pipelines, acionadores e runtimes de integração - os requisitos seguintes são aplicáveis:
- Para criar e gerir recursos subordinados no portal do Azure, tem de pertencer à função **Contribuidor do Data Factory** ao nível do grupo de recursos ou superior.
- Para criar e gerir recursos subordinados com o PowerShell ou o SDK, a função **contribuidor** ao nível do grupo de recursos ou superior é suficiente.

Para obter instruções de exemplo sobre como adicionar um utilizador a uma função, veja o artigo [Adicionar funções](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="set-up-permissions"></a>Configurar permissões

Depois de criar um Data Factory, talvez você queira permitir que outros usuários trabalhem com o data factory. Para conceder esse acesso a outros usuários, você precisa adicioná-los à função de colaborador de **Data Factory** interna no grupo de recursos que contém o data Factory.

### <a name="scope-of-the-data-factory-contributor-role"></a>Escopo da função colaborador de Data Factory

A associação na função **colaborador de data Factory** permite que os usuários realizem as seguintes ações:
- Crie, edite e exclua data factories e recursos filho, incluindo conjuntos de dados, serviços vinculados, pipelines, gatilhos e tempos de execução de integração.
- Implantar modelos do Resource Manager. A implantação do Gerenciador de recursos é o método de implantação usado por Data Factory no portal do Azure.
- Gerencie alertas do App insights para um data factory.
- Criar tíquetes de suporte.

Para obter mais informações sobre essa função, consulte [Data Factory colaborador role](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Implementação de modelo do Resource Manager

A função **colaborador do data Factory** , no nível do grupo de recursos ou acima, permite que os usuários implantem modelos do Resource Manager. Como resultado, os membros da função podem usar modelos do Resource Manager para implantar data factories e seus recursos filho, incluindo conjuntos de dados, serviços vinculados, pipelines, gatilhos e tempos de execução de integração. No entanto, a associação nessa função não permite que o usuário crie outros recursos.

As permissões no Azure Repos e no GitHub são independentes de permissões de Data Factory. Como resultado, um usuário com permissões de repositório que é apenas um membro da função leitor pode editar Data Factory recursos filho e confirmar as alterações no repositório, mas não pode publicar essas alterações.

> [!IMPORTANT]
> A implantação do modelo do Resource Manager com a função **colaborador do data Factory** não eleva suas permissões. Por exemplo, se você implantar um modelo que cria uma máquina virtual do Azure e não tiver permissão para criar máquinas virtuais, a implantação falhará com um erro de autorização.

### <a name="custom-scenarios-and-custom-roles"></a>Cenários personalizados e funções personalizadas

Às vezes, talvez seja necessário conceder diferentes níveis de acesso para diferentes data factory usuários. Por exemplo:
- Talvez seja necessário um grupo no qual os usuários só tenham permissões em um data factory específico.
- Ou talvez você precise de um grupo no qual os usuários possam monitorar apenas um data factory (ou fábricas), mas não podem modificá-lo.

Você pode obter esses cenários personalizados criando funções personalizadas e atribuindo usuários a essas funções. Para obter mais informações sobre funções personalizadas, consulte [funções personalizadas no Azure](..//role-based-access-control/custom-roles.md).

Aqui estão alguns exemplos que demonstram o que você pode obter com funções personalizadas:

- Permita que um usuário crie, edite ou exclua qualquer data factory em um grupo de recursos do portal do Azure.

  Atribua a função de **colaborador de data Factory** interna no nível do grupo de recursos para o usuário. Se você quiser permitir o acesso a qualquer data factory em uma assinatura, atribua a função no nível de assinatura.

- Permitir que um usuário exiba (leia) e monitore um data factory, mas não edite ou altere-o.

  Atribua a função de **leitor** interna no recurso de data Factory para o usuário.

- Permitir que um usuário edite um único data factory no portal do Azure.

  Esse cenário requer duas atribuições de função.

  1. Atribua a função **colaborador** interna no nível de data Factory.
  2. Crie uma função personalizada com a permissão **Microsoft. Resources/Implantations/** . Atribua essa função personalizada ao usuário no nível do grupo de recursos.

- Permitir que um usuário só possa testar a conexão em um serviço vinculado

    Crie uma função de função personalizada com permissões para as seguintes ações: **Microsoft. datafactory/fábricas/Getfeaturevalue/Read** e **Microsoft. datafactory/factories/getDataPlaneAccess/Read**. Atribua essa função personalizada no recurso de data factory para o usuário.

- Permitir que um usuário atualize uma data factory do PowerShell ou do SDK, mas não no portal do Azure.

  Atribua a função **colaborador** interna no recurso de data Factory para o usuário. Essa função permite que o usuário veja os recursos na portal do Azure, mas o usuário não pode acessar os botões **publicar** e **publicar todos** .

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre as funções no Azure – [entender as definições de função](../role-based-access-control/role-definitions.md)

- Saiba mais sobre a função colaborador do **Data Factory** - [Data Factory colaborador](../role-based-access-control/built-in-roles.md#data-factory-contributor).
