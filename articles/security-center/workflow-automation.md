---
title: Automação de fluxo de trabalho em Azure Security Center | Microsoft Docs
description: Saiba como criar e automatizar fluxos de trabalho no Centro de Segurança Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 6268ff6cfb3d3e856edcd8f84af930d52f4cf9d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096178"
---
# <a name="automate-responses-to-security-center-triggers"></a>Automatizar respostas aos gatilhos do Centro de Segurança

Todos os programas de segurança incluem vários fluxos de trabalho para resposta a incidentes. Estes processos podem incluir notificar as partes interessadas relevantes, lançar um processo de gestão de mudanças e aplicar medidas de reparação específicas. Os peritos em segurança recomendam que automatize o máximo de passos possível. A automação reduz as despesas gerais. Também pode melhorar a sua segurança garantindo que os passos do processo são feitos de forma rápida, consistente e de acordo com os seus requisitos predefinidos.

Este artigo descreve a funcionalidade de automatização do fluxo de trabalho do Azure Security Center. Esta funcionalidade pode desencadear aplicações lógicas em alertas de segurança, recomendações e alterações à conformidade regulamentar. Por exemplo, pode querer que o Centro de Segurança envie um e-mail a um utilizador específico quando ocorre um alerta. Também aprenderá a criar Aplicações Lógicas usando [Apps Azure Logic.](../logic-apps/logic-apps-overview.md)


## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Disponibilidade Geral (GA)|
|Preços:|Gratuito|
|Funções e permissões necessárias:|**Papel de administrador de segurança** ou **Proprietário** no grupo de recursos<br>Também deve ter permissões de escrita para o recurso alvo<br><br>Para trabalhar com fluxos de trabalho Azure Logic Apps, também deve ter as seguintes funções/permissões de Aplicações Lógicas:<br> - As permissões [do Operador de Aplicações Lógicas](../role-based-access-control/built-in-roles.md#logic-app-operator) são necessárias ou o acesso à Aplicação Lógica /gatilho (esta função não pode criar ou editar aplicações lógicas; apenas *executar* as existentes)<br> - As permissões [de Contribuidores de Aplicações Lógicas](../role-based-access-control/built-in-roles.md#logic-app-contributor) são necessárias para a criação e modificação de Aplicações Lógicas<br>Se quiser utilizar conectores Logic App, poderá precisar de credenciais adicionais para iniciar sôm nos respetivos serviços (por exemplo, as suas instâncias Outlook/Teams/Slack)|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![Yes](./media/icons/yes-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Crie uma aplicação lógica e defina quando deve ser executado automaticamente 

1. A partir da barra lateral do Centro de Segurança, selecione **a automatização do fluxo de trabalho**.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Lista de automatizações de fluxos de trabalho":::

    A partir desta página pode criar novas regras de automatização, bem como ativar, desativar ou eliminar as existentes.

1. Para definir um novo fluxo de trabalho, clique em **Adicionar automatização de fluxo de trabalho**. 

    Um painel aparece com as opções para a sua nova automatização. Aqui pode introduzir:
    1. Um nome e descrição para a automação.
    1. Os gatilhos que iniciarão este fluxo de trabalho automático. Por exemplo, pode querer que a sua App Lógica seja executada quando for gerado um alerta de segurança que contenha "SQL".

        > [!NOTE]
        > Se o seu gatilho for uma recomendação que tenha "sub-recomendações", por exemplo, **as conclusões de avaliação de vulnerabilidade nas suas bases de dados SQL devem ser remediadas,** a aplicação lógica não irá desencadear cada nova descoberta de segurança; apenas quando o estado da recomendação dos pais mudar.

    1. A Aplicação Lógica que será executada quando as condições do gatilho estiverem reunidas. 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Adicione painel de automatizações de fluxo de trabalho":::

1. A partir da secção Ações, clique em **Criar um novo** para iniciar o processo de criação da App Lógica.

    Você será levado para Azure Logic Apps.

    [![Criação de uma nova App Lógica](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Insira um nome, grupo de recursos e localização, e clique em **Criar**.

1. Na sua nova aplicação lógica, pode escolher entre modelos incorporados e predefinidos da categoria de segurança. Ou pode definir um fluxo personalizado de eventos que ocorram quando este processo é desencadeado.

    > [!TIP]
    > Por vezes, numa aplicação lógica, os parâmetros são incluídos no conector como parte de uma corda e não no seu próprio campo. Para um exemplo de como extrair parâmetros, consulte passo #14 de [trabalhar com parâmetros de aplicações lógicas enquanto constrói automatizações de fluxo de trabalho do Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121).

    O designer de aplicativos de lógica suporta estes gatilhos do Security Center:

    - **Quando uma recomendação do Centro de Segurança Azure for criada ou ativada** - Se a sua aplicação lógica se basear numa recomendação que seja depreciada ou substituída, a sua automatização deixará de funcionar e terá de atualizar o gatilho. Para acompanhar as alterações às recomendações, consulte [as notas de lançamento do Azure Security Center](release-notes.md).

    - **Quando um Alerta do Centro de Segurança Azure é criado ou acionado** - Pode personalizar o gatilho de modo a que se relacione apenas com os níveis de gravidade que lhe interessam.
    
    - **Quando uma avaliação de conformidade regulamentar do Centro de Segurança é criada ou desencadeada** - Desencadeie as automatizações com base em atualizações para avaliações de conformidade regulamentar.

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


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>Configure a automatização do fluxo de trabalho em escala utilizando as políticas fornecidas

Automatizar os processos de monitorização e resposta a incidentes da sua organização pode melhorar consideravelmente o tempo necessário para investigar e mitigar incidentes de segurança.

Para implementar as configurações de automação em toda a sua organização, utilize as políticas fornecidas da Azure Policy 'DeployIfNotExist' descritas abaixo para criar e configurar procedimentos de automatização do fluxo de trabalho.

Começa com [modelos de automatização de fluxos de trabalho.](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)

Implementar estas políticas:

1. A partir da tabela abaixo, selecione a política que pretende aplicar:

    |Objetivo  |Política  |ID de política  |
    |---------|---------|---------|
    |Automatização do fluxo de trabalho para alertas de segurança|[Implementar a Automatização do Fluxo de Trabalho para os alertas do Centro de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |Automatização do fluxo de trabalho para recomendações de segurança|[Implementar a Automatização do Fluxo de Trabalho para as recomendações do Centro de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    |Automatização do fluxo de trabalho para alterações de conformidade regulamentar|[Implementar automatização de fluxo de trabalho para conformidade regulamentar do Centro de Segurança Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
    ||||

    > [!TIP]
    > Também pode encontrá-las pesquisando a Política de Azure:
    > 1. Política aberta do Azure.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Aceder à Política de Azure":::
    > 2. A partir do menu Azure Policy, selecione **Definições** e procure-as pelo nome. 

1. Na página política Azure relevante, selecione **Atribuir**.
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="Atribuição da Política Azure":::

1. Abra cada separador e desabrohe os parâmetros conforme desejado:
    1. No separador **Básico,** estabeleça a margem de manobra para a apólice. Para utilizar a gestão centralizada, atribua a política ao Grupo de Gestão contendo as subscrições que utilizarão a configuração de automatização do fluxo de trabalho. 
    1. No **separador Parâmetros,** desenhe o grupo de recursos e os detalhes do tipo de dados. 
        > [!TIP]
        > Cada parâmetro tem uma ponta de ferramenta explicando as opções disponíveis para si.
        >
        > O separador de parâmetros da Azure Policy (1) fornece acesso a opções de configuração semelhantes à página de automação de fluxo de trabalho do Security Center (2).
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="Comparar os parâmetros na automatização do fluxo de trabalho com a Política Azure" lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. Opcionalmente, para aplicar esta atribuição às subscrições existentes, abrir o separador **Remediação** e selecionar a opção de criar uma tarefa de remediação.

1. Reveja a página de resumo e **selecione Criar**.


## <a name="data-types-schemas"></a>Esquemas de tipos de dados

Para ver os esquemas de eventos brutos dos alertas de segurança ou eventos de recomendações passados para a instância da Aplicação Lógica, visite os [esquemas de tipos de dados de automatização](https://aka.ms/ASCAutomationSchemas)de fluxo de trabalho. Isto pode ser útil nos casos em que não esteja a utilizar os conectores logic app incorporados do Security Center acima mencionados, mas em vez disso está a usar o conector HTTP genérico da Logic App - pode utilizar o esquema JSON do evento para analisar manualmente o que entender.


## <a name="faq-for-workflow-automation"></a>FAQ para automatização do fluxo de trabalho

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>A automatização do fluxo de trabalho suporta qualquer cenário de continuidade de negócios ou recuperação de desastres (BCDR) ?

Ao preparar o seu ambiente para cenários BCDR, onde o recurso-alvo está a sofrer uma paragem ou outro desastre, é da responsabilidade da organização prevenir a perda de dados, estabelecendo backups de acordo com as diretrizes dos Azure Event Hubs, Log Analytics e Logic App.

Para cada automatização ativa, recomendamos que crie uma automatização idêntica (desativada) e a guarde num local diferente. Quando houver uma paragem, pode ativar estas automatizações de backup e manter operações normais.

Saiba mais sobre [a continuidade do Negócio e recuperação de desastres para a Azure Logic Apps.](../logic-apps/business-continuity-disaster-recovery-guidance.md)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar Aplicações Lógicas, automatizando a sua execução no Centro de Segurança e executando-as manualmente.

Para obter material relacionado, consulte: 

- [O módulo Microsoft Learn sobre como utilizar a automatização do fluxo de trabalho para automatizar uma resposta de segurança](/learn/modules/resolve-threats-with-azure-security-center/)
- [Recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md)
- [Alertas de segurança no Centro de Segurança do Azure](security-center-alerts-overview.md)
- [Acerca do Azure Logic Apps](../logic-apps/logic-apps-overview.md)
- [Conectores do Azure Logic Apps](../connectors/apis-list.md)
- [Esquemas de tipos de dados de automatização do fluxo de trabalho](https://aka.ms/ASCAutomationSchemas)