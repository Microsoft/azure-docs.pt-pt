---
title: Permissões em Azure Sentinel | Microsoft Docs
description: Este artigo explica como o Azure Sentinel usa o controlo de acesso baseado em funções Azure para atribuir permissões aos utilizadores, e identifica as ações permitidas para cada papel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: yelevin
ms.openlocfilehash: 77a8e208e463b8ab20d563421d447813b1ce84ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772557"
---
# <a name="permissions-in-azure-sentinel"></a>Permissões no Azure Sentinel

O Azure Sentinel usa [o controlo de acesso baseado em funções (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) para fornecer [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços em Azure.

Use o Azure RBAC para criar e atribuir funções dentro da sua equipa de operações de segurança para conceder acesso adequado ao Azure Sentinel. As diferentes funções dão-lhe um controlo fino sobre o que os utilizadores do Azure Sentinel podem ver e fazer. As funções Azure podem ser atribuídas diretamente no espaço de trabalho do Azure Sentinel (ver nota abaixo), ou num grupo de subscrição ou recursos a que o espaço de trabalho pertence, ao qual o Azure Sentinel herdará.

## <a name="roles-for-working-in-azure-sentinel"></a>Papéis para trabalhar em Azure Sentinel

### <a name="azure-sentinel-specific-roles"></a>Funções específicas do Azure Sentinel

Há três funções dedicadas ao Azure Sentinel.

**Todas as funções incorporadas Azure Sentinel concedem acesso aos dados no seu espaço de trabalho Azure Sentinel.**

- O [Leitor do Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) permite ver dados, incidentes, livros e outros recursos do Azure Sentinel.

- O [Dispositivo de Resposta do Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) pode, além do acima referido, gerir incidentes (atribuir, dispensar, etc.)

- O [Contribuidor do Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) pode, além do acima referido, criar e editar livros, regras de análise e outros recursos do Azure Sentinel.

- [AZure Sentinel Automation Contributor](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) permite ao Azure Sentinel adicionar playbooks às regras de automação. Não se destina a contas de utilizador.

> [!NOTE]
>
> - Para obter melhores resultados, estas funções devem ser atribuídas no **grupo de recursos** que contém o espaço de trabalho Azure Sentinel. Desta forma, as funções aplicar-se-ão a todos os recursos que são utilizados para apoiar o Azure Sentinel, uma vez que esses recursos também deverão ser colocados nesse mesmo grupo de recursos.
>
> - Outra opção é atribuir as funções diretamente no **espaço de trabalho** Azure Sentinel. Se o fizer, também deve atribuir as mesmas funções no **recurso de solução** SecurityInsights nesse espaço de trabalho. Você pode precisar atribuí-los em outros recursos também, e você precisará estar constantemente gerindo atribuições de funções em recursos.

### <a name="additional-roles-and-permissions"></a>Papéis e permissões adicionais

Os utilizadores com requisitos de trabalho específicos podem ter de ser atribuídos a funções adicionais ou permissões específicas para realizarem as suas tarefas.

- Trabalhar com livros de jogadas para automatizar respostas a ameaças

    Azure Sentinel usa **livros para** resposta automatizada de ameaças. Os playbooks são construídos em **Azure Logic Apps,** e são um recurso Azure separado. É melhor atribuir a membros específicos da sua equipa de operações de segurança a capacidade de utilizar aplicações lógicas para operações de orquestração, automação e resposta (SOAR). Pode utilizar o papel [de Contribuinte de Aplicação Lógica](../role-based-access-control/built-in-roles.md#logic-app-contributor) para atribuir permissão explícita para a utilização de playbooks.

- Ligação de fontes de dados ao Azure Sentinel

    Para que um utilizador adicione **conectores de dados,** tem de atribuir permissões de escrita ao utilizador no espaço de trabalho do Azure Sentinel. Além disso, note as permissões adicionais necessárias para cada conector, conforme listado na página de conector relevante.

- Utilizadores convidados que atribuem incidentes

    Se um utilizador convidado precisar de ser capaz de atribuir incidentes, então, para além da função Azure Sentinel Responder, o utilizador também terá de ser designado para o papel de Leitor de [Diretórios](../active-directory/roles/permissions-reference.md#directory-readers). Note que este papel *não* é um papel de Azure, mas um papel **de Diretório Ativo Azure,** e que os utilizadores regulares (não convidados) têm esta função atribuída por padrão. 

Para uma comparação lado a lado, consulte a [tabela abaixo](#roles-and-allowed-actions).

### <a name="other-roles-you-might-see-assigned"></a>Outros papéis que pode ver atribuídos

Ao atribuir funções Azure Sentinel específicas do Azure, poderá encontrar outras funções Azure e Log Analytics Azure que possam ter sido atribuídas aos utilizadores para outros fins. Deve estar ciente de que estas funções concedem um conjunto mais alargado de permissões que incluem acesso ao seu espaço de trabalho Azure Sentinel e outros recursos:

- **Funções Azure:** [Proprietário,](../role-based-access-control/built-in-roles.md#owner) [Contribuinte](../role-based-access-control/built-in-roles.md#contributor)e [Leitor.](../role-based-access-control/built-in-roles.md#reader) As funções Azure concedem acesso a todos os seus recursos Azure, incluindo espaços de trabalho log analytics e recursos Azure Sentinel.

- **Funções de Log Analytics:** [Log Analytics Contributor](../role-based-access-control/built-in-roles.md#log-analytics-contributor) e Log Analytics [Reader](../role-based-access-control/built-in-roles.md#log-analytics-reader). As funções de Log Analytics concedem acesso aos seus espaços de trabalho Log Analytics. 

Por exemplo, um utilizador a quem é atribuído o papel **de Azure Sentinel Reader,** mas não o papel **de Contribuinte Azure Sentinel,** ainda poderá editar itens em Azure Sentinel se for atribuído o papel **de Contribuinte** ao nível de Azure. Portanto, se pretender conceder permissões a um utilizador apenas no Azure Sentinel, deve remover cuidadosamente as permissões prévias deste utilizador, certificando-se de que não quebra qualquer acesso necessário a outro recurso.

## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela seguinte resume as funções e ações permitidas no Azure Sentinel. 

| Função | Criar e executar manuais de procedimentos| Criar e editar livros, regras analíticas e outros recursos do Azure Sentinel | Gerir incidentes (dispensar, atribuir, etc.) | Ver dados, incidentes, livros e outros recursos do Azure Sentinel |
|---|---|---|---|---|
| Leitor do Azure Sentinel | -- | -- | -- | &#10003; |
| Dispositivo de Resposta do Azure Sentinel | -- | -- | &#10003; | &#10003; |
| Contribuidor do Azure Sentinel | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel Contributor + Colaborador de Aplicação Lógica | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-azure-rbac"></a>Funções personalizadas e RBAC do Azure avançadas

- **Funções personalizadas**. Além disso, ou em vez de usar funções incorporadas do Azure, pode criar papéis personalizados Azure para O Azure Sentinel. Os papéis personalizados Azure para O Azure Sentinel são criados da mesma forma que cria outros [papéis personalizados Azure](../role-based-access-control/custom-roles-rest.md#create-a-custom-role), baseados em [permissões específicas para Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) e para [recursos Azure Log Analytics](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

- **Log Analytics RBAC**. Pode utilizar o controle avançado de acesso baseado em funções do Log Analytics através dos dados do seu espaço de trabalho Azure Sentinel. Isto inclui tanto o Azure RBAC baseado em dados como o Azure RBAC de contexto de recursos. Para obter mais informações, consulte:

    - [Gerir dados de registo e espaços de trabalho no Azure Monitor](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [RBAC de contexto de recursos para Azure Sentinel](resource-context-rbac.md)
    - [RBAC ao nível de tabelas](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    O RBAC de contexto de recursos e de nível de mesa são dois métodos para fornecer acesso a dados específicos no seu espaço de trabalho Azure Sentinel sem permitir o acesso a toda a experiência do Azure Sentinel.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a trabalhar com papéis para os utilizadores do Azure Sentinel e o que cada papel permite que os utilizadores façam.

Encontre posts de blog sobre segurança e conformidade da [Azure no Azure Sentinel Blog](https://aka.ms/azuresentinelblog).
