---
title: Funções e permissões do Azure Data Factory | Documentos da Microsoft
description: Descreve as funções e as permissões necessárias para criar fábricas de dados e trabalhar com recursos subordinados.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 19666eb668dd120c1705c6a62a8ba1abd2321026
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575720"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Funções e permissões do Azure Data Factory

Este artigo descreve as funções necessárias para criar e gerir recursos do Azure Data Factory e as permissões concedidas por essas funções.

## <a name="roles-and-requirements"></a>Funções e os requisitos

Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser membro das funções *contribuidor* ou *proprietário* ou *administrador* da subscrição do Azure. Para ver as permissões que tem na subscrição, no portal do Azure, selecione o seu nome de utilizador no canto superior direito e, em seguida, selecione **Permissões**. Se tiver acesso a várias subscrições, selecione a subscrição apropriada. 

Para criar e gerir recursos subordinados do Data Factory - incluindo conjuntos de dados, serviços ligados, pipelines, acionadores e runtimes de integração - os requisitos seguintes são aplicáveis:
- Para criar e gerir recursos subordinados no portal do Azure, tem de pertencer à função **Contribuidor do Data Factory** ao nível do grupo de recursos ou superior.
- Para criar e gerir recursos subordinados com o PowerShell ou o SDK, a função **contribuidor** ao nível do grupo de recursos ou superior é suficiente.

Para obter instruções de exemplo sobre como adicionar um utilizador a uma função, veja o artigo [Adicionar funções](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="set-up-permissions"></a>Configurar permissões

Depois de criar uma fábrica de dados, pode querer permitir que outros utilizadores de trabalhar com a fábrica de dados. Para atribuir este acesso a outros utilizadores, tem de adicioná-los incorporada **contribuinte do Data Factory** função no grupo de recursos que contém a fábrica de dados.

### <a name="scope-of-the-data-factory-contributor-role"></a>Âmbito da função de contribuinte do Data Factory

A associação a **contribuinte do Data Factory** função permite aos utilizadores que efetue os seguintes procedimentos:
- Criar, editar e eliminar fábricas de dados e recursos subordinados, incluindo conjuntos de dados, serviços ligados, pipelines, acionadores e runtimes de integração.
- Implemente modelos do Resource Manager. Implementação do Resource Manager é o método de implantação utilizado pela fábrica de dados no portal do Azure.
- Gerir alertas do App Insights para uma fábrica de dados.
- Crie pedidos de suporte.

Para mais informações sobre esta função, veja [função de contribuinte do Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Implementação de modelo do Resource Manager

O **contribuinte do Data Factory** função, ao nível do grupo de recursos ou superior, permite aos utilizadores implementar modelos do Resource Manager. Como resultado, os membros da função podem utilizar modelos do Resource Manager para implementar fábricas de dados e respetivos recursos subordinados, incluindo conjuntos de dados, serviços ligados, pipelines, acionadores e runtimes de integração. Membros desta função não permitir que o utilizador crie outros recursos, no entanto.

Permissões em repositórios do Azure e do GitHub são independentes de permissões do Data Factory. Como resultado, um utilizador com permissões de repositório que seja apenas um membro da função leitor pode editar recursos subordinados de fábrica de dados e consolidar as alterações ao repositório, mas não é possível publicar estas alterações.

> [!IMPORTANT]
> Implementação de modelo do Resource Manager com o **contribuinte do Data Factory** função não elevar as suas permissões. Por exemplo, se implementar um modelo que cria uma máquina virtual do Azure, e não tem permissão para criar máquinas virtuais, a implementação falhar com um erro de autorização.

### <a name="custom-scenarios-and-custom-roles"></a>Cenários personalizados e funções personalizadas

Por vezes, terá de conceder diferentes níveis de acesso para utilizadores de fábrica de dados diferentes. Por exemplo:
- Poderá ter um grupo em que os utilizadores só têm permissões numa fábrica de dados específico.
- Ou poderá ter um grupo em que os utilizadores só podem monitorizar uma fábrica de dados (ou fábricas), mas não é possível modificá-lo.

Pode obter estes cenários personalizados ao criar funções personalizadas e atribuir utilizadores a essas funções. Para obter mais informações sobre funções personalizadas, consulte [funções personalizadas no Azure](..//role-based-access-control/custom-roles.md).

Aqui estão alguns exemplos que demonstram o que pode conseguir com funções personalizadas:

- Permitir que um usuário criar, editar ou eliminar qualquer fábrica de dados num grupo de recursos do portal do Azure.

  Atribuir o incorporado **contribuinte do Data Factory** função ao nível do grupo de recursos para o utilizador. Se pretender permitir o acesso a qualquer fábrica de dados numa subscrição, atribua a função ao nível da subscrição.

- Permitir que um modo de exibição do usuário (leitura) e monitorizar uma fábrica de dados, mas não editar ou alterá-la.

  Atribuir o incorporado **leitor** função no recurso de fábrica de dados para o utilizador.

- Permitir que um utilizador editar uma fábrica de dados individual no portal do Azure.

  Este cenário requer duas atribuições de funções.

  1. Atribuir o incorporado **contribuinte** função ao nível da fábrica de dados.
  2. Criar uma função personalizada com a permissão **Microsoft.Resources/deployments/**. Atribua esta função personalizada para o utilizador ao nível do grupo de recursos.

- Permitir que um utilizador Atualize uma fábrica de dados do PowerShell ou o SDK, mas não no portal do Azure.

  Atribuir o incorporado **contribuinte** função no recurso de fábrica de dados para o utilizador. Esta função permite que o usuário consulte os recursos no portal do Azure, mas o utilizador não é possível aceder a **Publish** e **publicar tudo** botões.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre as funções no Azure - [compreender as definições de função](../role-based-access-control/role-definitions.md)

- Saiba mais sobre o **contribuinte do Data Factory** função - [função de contribuinte do Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).
