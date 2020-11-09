---
title: Isentar um recurso das recomendações de segurança do Azure Security Center e a pontuação segura
description: Saiba como isentar um recurso das recomendações de segurança e da pontuação segura
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 347d5ac9de164f2e96340df71fd3b1b908e607c1
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372749"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>Isentar um recurso de recomendações e da pontuação de segurança

Uma prioridade central de todas as equipas de segurança é tentar garantir que os analistas se concentrem nas tarefas e incidentes que interessam à organização. O Security Center tem muitas funcionalidades para personalizar a informação que mais prioriza e garantir que a sua pontuação segura é um reflexo válido das decisões de segurança da sua organização. A isenção de recursos é uma dessas características.

Quando investiga uma recomendação de segurança no Azure Security Center, uma das primeiras informações que analisa é a lista de recursos afetados.

Ocasionalmente, um recurso será listado que não deve ser incluído. Pode ter sido remediado por um processo não rastreado pelo Centro de Segurança. Ou talvez a sua organização tenha decidido aceitar o risco para esse recurso específico. 

Nesses casos, pode criar uma regra de isenção e garantir que o recurso não está listado com os recursos insalubres no futuro, e não afeta a sua pontuação segura. 

O recurso será listado como não aplicável e a razão será mostrada como "isenta" com a justificação que selecionar.

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Pré-visualizar<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preços:|Esta é uma capacidade de política premium Azure que é oferecida para os clientes Azure Defender sem custos adicionais. Para outros utilizadores, os encargos podem ser aplicados no futuro.|
|Funções e permissões necessárias:|**Proprietário de assinatura** ou **colaborador de política** para criar uma isenção<br>Para criar uma regra, precisa de permissões para editar políticas na Política Azure.<br>Saiba mais nas [permissões Azure RBAC na Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![No](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||


## <a name="create-an-exemption-rule"></a>Criar uma regra de isenção

1. Na lista de recursos pouco saudáveis, selecione o menu de elipses ("...") para o recurso que pretende isentar.

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="Criar opção de isenção do menu de contexto":::

    O painel de isenção de criação abre.

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="Criar painel de isenção":::

1. Insira os seus critérios e selecione um critério para que este recurso seja isento:
    - **Mitigado** - Este problema não é relevante para o recurso porque foi tratado por uma ferramenta ou processo diferente daquele que está sendo sugerido
    - **Renúncia** - Aceitando o risco para este recurso
1. Selecione **Save** (Guardar).
1. Depois de um tempo (pode levar até 24 horas):
    - O recurso não afeta a sua pontuação segura.
    - O recurso está listado no separador **Não aplicável** da página de detalhes da recomendação
    - A tira de informação no topo da página de detalhes da recomendação lista o número de recursos isentos:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Número de recursos isentos":::

1. Para rever os seus recursos isentos, abra o **separador Não aplicável.**

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Modificação de uma isenção":::

    A razão de cada isenção está incluída na tabela (1).

    Para modificar ou eliminar uma isenção, selecione o menu de elipses ("...") como mostrado (2).


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>Reveja todas as regras de isenção da sua subscrição

As regras de isenção utilizam a política Azure para criar uma isenção para o recurso na atribuição de políticas.

Pode utilizar a Política Azure para acompanhar toda a sua isenção na página **isenção:**

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Página de isenção da Azure Policy":::



## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a isentar um recurso de uma recomendação para que não tenha impacto na sua pontuação segura. Para obter mais informações sobre a pontuação segura, consulte:

- [Pontuação de segurança no Centro de Segurança do Azure](secure-score-security-controls.md)