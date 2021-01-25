---
title: Isentar uma recomendação do Azure Security Center de um recurso, subscrição, grupo de gestão e pontuação segura
description: Saiba como criar regras para isentar as recomendações de segurança de subscrições ou grupos de gestão e evitar que impactem a sua pontuação segura
author: memildin
ms.author: memildin
ms.date: 01/22/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 4012c7417345678717800f4fdede95947e00b828
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756733"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Isenção de recursos e recomendações da sua pontuação segura 

Uma prioridade central de todas as equipas de segurança é garantir que os analistas se concentrem nas tarefas e incidentes que interessam à organização. O Security Center tem muitas funcionalidades para personalizar a experiência e garantir que a sua pontuação segura reflete as prioridades de segurança da sua organização. A opção **isenta** é uma dessas características.

Quando investiga as suas recomendações de segurança no Azure Security Center, uma das primeiras informações que analisa é a lista de recursos afetados.

Ocasionalmente, um recurso será listado que não deve ser incluído. Ou uma recomendação mostrará num âmbito onde se sente que não pertence. O recurso pode ter sido remediado por um processo não rastreado pelo Centro de Segurança. A recomendação pode ser inadequada para uma subscrição específica. Ou talvez a sua organização tenha simplesmente decidido aceitar os riscos relacionados com o recurso ou recomendação específico.

Nesses casos, pode criar uma isenção para uma recomendação para:

- **Isentar um recurso** para garantir que não está listado com os recursos insalubres no futuro, e não afeta a sua pontuação segura. O recurso será listado como não aplicável e a razão será mostrada como "isenta" com a justificação específica que seleciona.

- **Isentar um grupo de subscrição ou gestão** para garantir que a recomendação não tenha impacto na sua pontuação segura e não será mostrada para o grupo de subscrição ou gestão no futuro. Isto diz respeito aos recursos existentes e a qualquer um que crie no futuro. A recomendação será marcada com a justificação específica que seleciona para o âmbito que selecionou.

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Pré-visualizar<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preços:|Esta é uma capacidade de política premium Azure que é oferecida para os clientes Azure Defender sem custos adicionais. Para outros utilizadores, os encargos podem ser aplicados no futuro.|
|Funções e permissões necessárias:|**Proprietário de assinatura** ou **colaborador de política** para criar uma isenção<br>Para criar uma regra, precisa de permissões para editar políticas na Política Azure.<br>Saiba mais nas [permissões Azure RBAC na Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||

## <a name="define-an-exemption"></a>Definir uma isenção

Para afinar as recomendações de segurança que o Security Center faz para as suas assinaturas, grupo de gestão ou recursos, pode criar uma regra de isenção para:

- Marque uma **recomendação** específica ou como "atenuado" ou "risco aceite". Pode criar isenções de recomendação para uma subscrição, subscrições múltiplas ou um grupo de gestão inteiro.
- Marque **um ou mais recursos** como "atenuados" ou "riscos aceites" para uma recomendação específica.

> [!TIP]
> Também pode criar isenções usando a API. Por exemplo, JSON, e uma explicação das estruturas relevantes ver estrutura de [isenção da Política Azure.](../governance/policy/concepts/exemption-structure.md)

Para criar uma regra de isenção:

1. Abra a página de detalhes das recomendações para a recomendação específica.

1. A partir da barra de ferramentas no topo da página, **selecione Isento**.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Criar uma regra de isenção para que uma recomendação seja isenta de um grupo de subscrição ou gestão.":::

1. No painel **isento:**
    1. Selecione o âmbito para esta regra de isenção:
        - Se selecionar um grupo de gestão, a recomendação será isenta de todas as subscrições dentro desse grupo
        - Se estiver a criar esta regra para isentar um ou mais recursos da recomendação, escolha "Recursos Selecionados" e selecione os relevantes da lista

    1. Insira um nome para esta regra de isenção.
    1. Opcionalmente, estabeleça uma data de validade.
    1. Selecione a categoria para a isenção:
        - **Resolvido através de terceiros (atenuado)** – se estiver a utilizar um serviço de terceiros que o Security Center não identificou. 

            > [!NOTE]
            > Quando isentas uma recomendação como atenuada, não te são dados pontos para a tua pontuação segura. Mas como os pontos não são *removidos* para os recursos insalubres, o resultado é que a sua pontuação vai aumentar.

        - **Risco aceite (renúncia)** – se decidiu aceitar o risco de não atenuar esta recomendação
    1. Opcionalmente, insira uma descrição.
    1. Selecione **Criar**.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Medidas para criar uma regra de isenção para isentar uma recomendação do seu grupo de subscrição ou gestão":::

    Quando a isenção entrar em vigor (pode demorar até 30 minutos):
    - A recomendação ou os recursos não afetarão a sua pontuação segura.
    - Se isentou recursos específicos, eles serão listados no separador **Não aplicável** da página de detalhes da recomendação.
    - Se isentou uma recomendação, será escondida por padrão na página de recomendações do Centro de Segurança. Isto porque as opções predefinidos do filtro de **estado de Recomendação** nessa página são para excluir recomendações **não aplicáveis.** O mesmo acontece se isentar todas as recomendações num controlo de segurança.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Filtros predefinidos na página de recomendações do Azure Security Center ocultam as recomendações e controlos de segurança não aplicáveis":::

    - A tira de informação no topo da página de detalhes da recomendação atualiza o número de recursos isentos:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Número de recursos isentos":::

1. Para rever os seus recursos isentos, abra o **separador Não aplicável:**

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Modificação de uma isenção":::

    A razão de cada isenção está incluída na tabela (1).

    Para modificar ou eliminar uma isenção, selecione o menu de elipses ("...") como mostrado (2).

1. Para rever todas as regras de isenção da sua subscrição, **selecione Ver isenções** da tira de informação:

    > [!IMPORTANT]
    > Para ver as isenções específicas relevantes para uma recomendação, filtre a lista de acordo com o âmbito e o nome de recomendação relevantes.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Página de isenção da Azure Policy":::

    > [!TIP]
    > Em alternativa, [utilize o Azure Resource Graph para encontrar recomendações com isenções](#find-recommendations-with-exemptions-using-azure-resource-graph).

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Monitorizar isenções criadas nas suas subscrições

Como explicado anteriormente nesta página, as regras de isenção são uma ferramenta poderosa que fornece controlo granular sobre as recomendações que afetam recursos nas suas subscrições e grupos de gestão. 

Para acompanhar como os seus utilizadores estão a exercer esta capacidade, criámos um modelo Azure Resource Manager (ARM) que implementa um Playbook de Aplicação lógica e todas as ligações API necessárias para o notificar quando uma isenção foi criada.

- Para saber mais sobre o livro de jogadas, consulte este post nos [blogs](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580) da comunidade tecnológica
- Você encontrará o modelo ARM no [repositório do Centro de Segurança Azure GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)
- Pode clicar [aqui](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json) para implementar todos os componentes necessários 


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Encontre recomendações com isenções usando o Gráfico de Recursos Azure

O Azure Resource Graph (ARG) fornece acesso instantâneo à informação de recursos em todos os seus ambientes em nuvem com capacidades robustas de filtragem, agrupamento e triagem. É uma forma rápida e eficiente de consultar informações através de subscrições Azure programáticas ou dentro do portal Azure.

Para visualizar todas as recomendações que tenham regras de isenção:

1. Abrir **o Explorador de Gráficos de Recurso Azure**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Página de recomendação do Azure Resource Graph Explorer**" :::

1. Introduza a seguinte consulta e selecione **consulta de execução**.

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


Saiba mais nas seguintes páginas:
- [Saiba mais sobre o Azure Resource Graph](../governance/resource-graph/index.yml).
- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)
- [Linguagem de Consulta Kusto (KQL)](/azure/data-explorer/kusto/query/)





## <a name="exemption-rule-faq"></a>Regra de isenção FAQ

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>O que acontece quando uma recomendação está em múltiplas iniciativas políticas?

Por vezes, uma recomendação de segurança aparece em mais do que uma iniciativa política. Se tiver várias instâncias da mesma recomendação atribuídas à mesma subscrição, e criar uma isenção para a recomendação, irá afetar todas as iniciativas que tem permissão para editar. 

Por exemplo, a recomendação **** faz parte da iniciativa política predefinida atribuída a todas as subscrições da Azure pelo Azure Security Center. Também em XXXXX.

Se tentar criar uma isenção para esta recomendação, verá uma das duas mensagens seguintes:

- Se tiver as permissões necessárias para editar ambas as iniciativas, verá:

    *Esta recomendação está incluída em várias iniciativas políticas: [nomes de iniciativa separados pela vírgula]. Serão criadas isenções em todos eles.*  

- Se não tiver permissões suficientes em ambas as iniciativas, verá esta mensagem em vez disso:

    *Tem permissões limitadas para aplicar a isenção em todas as iniciativas políticas, as isenções serão criadas apenas com autorizações suficientes.*


## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu a isentar um recurso de uma recomendação para que não tenha impacto na sua pontuação segura. Para obter mais informações sobre a pontuação segura, consulte:

- [Pontuação de segurança no Centro de Segurança do Azure](secure-score-security-controls.md)