---
title: Automação de fluxo de trabalho no Centro de Segurança Azure Microsoft Docs
description: Saiba como criar e automatizar fluxos de trabalho no Centro de Segurança Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5d947cf41e13abdea9a2fd29f8a740d0c101dc6f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80397903"
---
# <a name="workflow-automation"></a>Automatização de fluxos de trabalho

Todos os programas de segurança incluem múltiplos fluxos de trabalho para resposta a incidentes. Estes processos podem incluir notificar as partes interessadas relevantes, lançar um processo de gestão de alterações e aplicar medidas específicas de reparação. Os peritos em segurança recomendam que automatizar o máximo de passos possível desses procedimentos. A automação reduz a sobrecarga. Também pode melhorar a sua segurança garantindo que os passos do processo são feitos de forma rápida, consistente e de acordo com os seus requisitos predefinidos.

Este artigo descreve a funcionalidade de automatização de fluxos de trabalho do Azure Security Center. Esta funcionalidade pode desencadear Aplicações Lógicas em alertas de segurança e recomendações. Por exemplo, pode querer que o Security Center envie um e-mail a um utilizador específico quando ocorrer um alerta. Também aprenderá si a criar aplicações lógicas usando [aplicações lógicas do Azure.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)

> [!NOTE]
> Se usou previamente a vista Playbooks (Preview) na barra lateral, encontrará as mesmas funcionalidades juntamente com a funcionalidade expandida na nova página de automatização de fluxos de trabalho.


## <a name="requirements"></a>Requisitos

* Para trabalhar com os fluxos de trabalho de Aplicações Lógicas Azure, deve ter as seguintes aplicações lógicas/permissões:

    * São necessárias permissões do Operador de [Aplicações Lógicas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) ou acesso de leitura/gatilho da Aplicação Lógica (esta função não pode criar ou editar aplicações lógicas; apenas *executar* as existentes)

    * São necessárias permissões do Colaborador da [Aplicação Lógica](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) para a criação e modificação de Aplicações Lógicas

* Se pretender utilizar conectores de Aplicações Lógicas, poderá necessitar de credenciais adicionais para iniciar sessão nos respetivos serviços (por exemplo, as suas instâncias Outlook/Teams/Slack)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Crie uma App Lógica e defina quando deve ser executada automaticamente 

1. A partir da barra lateral do Security Center, selecione **Workflow automation**.

    [![Lista de automatizações de fluxos de trabalho](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    A partir desta página pode criar novas regras de automatização, bem como ativar, desativar ou eliminar as existentes.  
1. Para definir um novo fluxo de trabalho, clique **em Adicionar automatização de fluxo de trabalho.** 

    Um painel aparece com as opções para a sua nova automatização. Aqui pode entrar:
    1. Um nome e descrição para a automação.
    1. Os gatilhos que iniciarão este fluxo de trabalho automático. Por exemplo, pode querer que a sua Aplicação Lógica seja executada quando for gerado um alerta de segurança que contenha "SQL".
    1. A Aplicação Lógica que funcionará quando as condições do gatilho forem satisfeitas. 

        [![Lista de automatizações de fluxos de trabalho](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. A partir da secção Ações, clique **em Criar um novo** para iniciar o processo de criação de Aplicações Lógicas.

    Será levado para as Aplicações Lógicas Azure.

    [![Criação de uma nova App Lógica](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Introduza um nome, grupo de recursos e localização, e clique em **Criar**.

1. Na sua nova App Lógica, pode escolher entre modelos incorporados e predefinidos da categoria de segurança. Ou pode definir um fluxo personalizado de eventos a ocorrer quando este processo é desencadeado.

    No designer de aplicações lógicas são suportados os seguintes gatilhos dos conectores do Centro de Segurança:

    * **Quando uma recomendação do Centro de Segurança Azure é criada ou desencadeada**
    * **Quando um alerta do Centro de Segurança Azure é criado ou desencadeado** 
    
    > [!TIP]
    > Pode personalizar o gatilho de modo a que se relacione apenas com alertas com os níveis de gravidade que lhe interessam.
    
    > [!NOTE]
    > Se estiver a utilizar o gatilho do legado "Quando uma resposta a um alerta do Azure Security Center for desencadeada", as suas Aplicações Lógicas não serão lançadas pela funcionalidade Workflow Automation. Em vez disso, utilize qualquer um dos gatilhos acima mencionados. 

    [![App lógica da amostra](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Depois de definir a sua App Lógica, volte ao painel de definição de automatização de fluxos de trabalho ("Adicionar automatização de fluxo de trabalho"). Clique em **Refresh** para garantir que a sua nova Aplicação Lógica está disponível para seleção.

    ![Atualizar](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Selecione a sua App Lógica e guarde a automatização. Note que a aplicação lógica dropdown apenas mostra Aplicações Lógicas com conectores de Centro de Segurança de suporte mencionados acima.


## <a name="manually-trigger-a-logic-app"></a>Desencadear manualmente uma Aplicação Lógica

Também pode executar aplicações lógicas manualmente ao visualizar um alerta de segurança ou qualquer recomendação que ofereça [remediação quick Fix](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation).

Para executar manualmente uma Aplicação Lógica, abra um alerta ou uma recomendação que suporte a reparação quick Fix e clique na **App Lógica de Gatilho:**

[![Desencadear manualmente uma Aplicação Lógica](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Tipos de dados schemas

Para ver os eventos brutos dos alertas de segurança ou eventos de recomendações passados para a instância da App Lógica, visite os tipos de dados de [automação de fluxo de trabalho](https://aka.ms/ASCAutomationSchemas). Isto pode ser útil em casos em que não está a usar os conectores de aplicações lógicas incorporados do Security Center mencionados acima, mas sim usar o conector genérico HTTP da Logic App - você poderia usar o evento JSON schema para analisá-lo manualmente como você entender.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar Apps Lógicas, automatizando a sua execução no Centro de Segurança e executando-as manualmente. 

Para outros materiais relacionados, consulte: 

- [O módulo Microsoft Learn sobre como usar a automatização do fluxo de trabalho para automatizar uma resposta de segurança](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md)
- [Alertas de segurança no Centro de Segurança do Azure](security-center-alerts-overview.md)
- [Acerca do Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Conectores do Logic Apps](https://docs.microsoft.com/connectors/)
- [Tipos de dados de automatização de fluxo schemas](https://aka.ms/ASCAutomationSchemas)
