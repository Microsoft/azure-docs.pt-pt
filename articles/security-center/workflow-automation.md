---
title: Automação de fluxo de trabalho no Centro de Segurança Azure Microsoft Docs
description: Saiba como criar e automatizar fluxos de trabalho no Centro de Segurança Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b713977d811411ea2ccd7dfa22c7757321ecd7aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712294"
---
# <a name="create-automatic-responses-to-alerts-and-recommendations-with-workflow-automation"></a>Criar respostas automáticas a alertas e recomendações com automatização de fluxos de trabalho

Todos os programas de segurança incluem vários fluxos de trabalho para resposta a incidentes. Estes processos podem incluir notificar as partes interessadas relevantes, lançar um processo de gestão de mudanças e aplicar medidas de reparação específicas. Os peritos em segurança recomendam que automatize o máximo de passos possível. A automação reduz as despesas gerais. Também pode melhorar a sua segurança garantindo que os passos do processo são feitos de forma rápida, consistente e de acordo com os seus requisitos predefinidos.

Este artigo descreve a funcionalidade de automatização do fluxo de trabalho do Azure Security Center. Esta funcionalidade pode desencadear Aplicações Lógicas em alertas de segurança e recomendações. Por exemplo, pode querer que o Centro de Segurança envie um e-mail a um utilizador específico quando ocorre um alerta. Também aprenderá a criar Aplicações Lógicas usando [Apps Azure Logic.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)

> [!NOTE]
> Se usou previamente a vista Playbooks (Preview) na barra lateral, encontrará as mesmas funcionalidades juntamente com a funcionalidade expandida na nova página de automação do fluxo de trabalho.



## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|Gratuito|
|Funções e permissões necessárias:|**Papel de administrador de segurança** ou **Proprietário** no grupo de recursos<br>Também deve ter permissões de escrita para o recurso alvo<br><br>Para trabalhar com fluxos de trabalho Azure Logic Apps, também deve ter as seguintes funções/permissões de Aplicações Lógicas:<br> - As permissões [do Operador de Aplicações Lógicas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) são necessárias ou o acesso à Aplicação Lógica /gatilho (esta função não pode criar ou editar aplicações lógicas; apenas *executar* as existentes)<br> - As permissões [de Contribuidores de Aplicações Lógicas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) são necessárias para a criação e modificação de Aplicações Lógicas<br>Se quiser utilizar conectores Logic App, poderá precisar de credenciais adicionais para iniciar sôm nos respetivos serviços (por exemplo, as suas instâncias Outlook/Teams/Slack)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Crie uma aplicação lógica e defina quando deve ser executado automaticamente 

1. A partir da barra lateral do Centro de Segurança, selecione **a automatização do fluxo de trabalho**.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Lista de automatizações de fluxos de trabalho":::

    A partir desta página pode criar novas regras de automatização, bem como ativar, desativar ou eliminar as existentes.

1. Para definir um novo fluxo de trabalho, clique em **Adicionar automatização de fluxo de trabalho**. 

    Um painel aparece com as opções para a sua nova automatização. Aqui pode introduzir:
    1. Um nome e descrição para a automação.
    1. Os gatilhos que iniciarão este fluxo de trabalho automático. Por exemplo, pode querer que a sua App Lógica seja executada quando for gerado um alerta de segurança que contenha "SQL".
    1. A Aplicação Lógica que será executada quando as condições do gatilho estiverem reunidas. 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Lista de automatizações de fluxos de trabalho":::

1. A partir da secção Ações, clique em **Criar um novo** para iniciar o processo de criação da App Lógica.

    Você será levado para Azure Logic Apps.

    [![Criação de uma nova App Lógica](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Insira um nome, grupo de recursos e localização, e clique em **Criar**.

1. Na sua nova aplicação lógica, pode escolher entre modelos incorporados e predefinidos da categoria de segurança. Ou pode definir um fluxo personalizado de eventos que ocorram quando este processo é desencadeado.

    No designer de aplicativos de lógica são suportados os seguintes gatilhos dos conectores do Centro de Segurança:

    * **Quando uma recomendação do Centro de Segurança Azure for criada ou ativada** - Se a sua aplicação lógica se basear numa recomendação que seja depreciada ou substituída, a sua automatização deixará de funcionar e terá de atualizar o gatilho. Para acompanhar as alterações às recomendações, consulte [as notas de lançamento do Azure Security Center](release-notes.md).

    * **Quando um Alerta do Centro de Segurança Azure é criado ou acionado** - Pode personalizar o gatilho de modo a que se relacione apenas com os níveis de gravidade que lhe interessam.
    
    > [!NOTE]
    > Se estiver a utilizar o gatilho legado "Quando for desencadeada uma resposta a um alerta do Centro de Segurança Azure", as suas aplicações lógicas não serão lançadas pela funcionalidade Dem automação do fluxo de trabalho. Em vez disso, utilize qualquer um dos gatilhos acima mencionados. 

    [![App lógica de amostra](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Depois de definir a sua aplicação lógica, volte ao painel de definição de automatização de fluxo de trabalho ("Adicione a automatização do fluxo de trabalho"). Clique **em Refresh** para garantir que a sua nova App Lógica está disponível para seleção.

    ![Atualizar](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Selecione a sua aplicação lógica e guarde a automatização. Note que o dropdown da Aplicação Lógica apenas mostra Aplicações Lógicas com conectores de Centro de Segurança de suporte mencionados acima.


## <a name="manually-trigger-a-logic-app"></a>Desencadear manualmente uma Aplicação Lógica

Também pode executar As Aplicações Lógicas manualmente ao visualizar qualquer alerta de segurança ou recomendação.

Para executar manualmente uma Aplicação Lógica, abra um alerta ou uma recomendação e clique em **Trigger Logic App**:

[![Desencadear manualmente uma Aplicação Lógica](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Esquemas de tipos de dados

Para ver os esquemas de eventos brutos dos alertas de segurança ou eventos de recomendações passados para a instância da Aplicação Lógica, visite os [esquemas de tipos de dados de automatização](https://aka.ms/ASCAutomationSchemas)de fluxo de trabalho. Isto pode ser útil nos casos em que não esteja a utilizar os conectores logic app incorporados do Security Center acima mencionados, mas em vez disso está a usar o conector HTTP genérico da Logic App - pode utilizar o esquema JSON do evento para analisar manualmente o que entender.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar Aplicações Lógicas, automatizando a sua execução no Centro de Segurança e executando-as manualmente. 

Para obter material relacionado, consulte: 

- [O módulo Microsoft Learn sobre como utilizar a automatização do fluxo de trabalho para automatizar uma resposta de segurança](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md)
- [Alertas de segurança no Centro de Segurança do Azure](security-center-alerts-overview.md)
- [Acerca do Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Conectores do Logic Apps](https://docs.microsoft.com/connectors/)
- [Esquemas de tipos de dados de automatização de fluxo de trabalho](https://aka.ms/ASCAutomationSchemas)
