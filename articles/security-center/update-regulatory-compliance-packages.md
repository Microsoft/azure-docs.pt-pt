---
title: Utilização do painel de conformidade regulamentar no Azure Security Center
description: Saiba como adicionar e remover as normas regulamentares do painel de conformidade regulamentar no Centro de Segurança
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: 4ba65517b698896c57ca2254394efcadd6efbb1d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835043"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Personalize o conjunto de padrões no seu painel de conformidade regulamentar

O Azure Security Center compara continuamente a configuração dos seus recursos com requisitos em padrões, regulamentos e referências da indústria. O **painel de conformidade regulamentar** fornece informações sobre a sua postura de conformidade com base na forma como está a cumprir os requisitos específicos de conformidade.

> [!TIP]
> Saiba mais sobre o painel de conformidade regulamentar do Security Center nas [perguntas mais frequentes.](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Como são representadas as normas de conformidade regulamentar no Centro de Segurança?

As normas da indústria, as normas regulamentares e os parâmetros de referência estão representados no painel de conformidade regulamentar do Centro de Segurança. Cada padrão é uma iniciativa definida na Política Azure.

Para ver os dados de conformidade mapeados como avaliações no seu painel de instrumentos, adicione um padrão de conformidade ao seu grupo de gestão ou subscrição dentro da página de política de **Segurança.** Para saber mais sobre a Política e iniciativas do Azure, consulte [Trabalhar com políticas de segurança.](tutorial-security-policy.md)

Quando atribuiu um padrão ou benchmark ao seu âmbito selecionado, a norma aparece no seu painel de conformidade regulamentar com todos os dados de conformidade associados mapeados como avaliações. Também pode baixar relatórios sumários para qualquer uma das normas que foram atribuídas.

A Microsoft acompanha os próprios padrões regulamentares e melhora automaticamente a sua cobertura em alguns dos pacotes ao longo do tempo. Quando a Microsoft lançar novos conteúdos para a iniciativa, aparecerá automaticamente no seu painel de instrumentos como novas políticas mapeadas para controlos na norma.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Que normas de conformidade regulamentar estão disponíveis no Centro de Segurança?

Por padrão, cada subscrição tem o **Benchmark de Segurança Azure** atribuído. Estas são as diretrizes específicas da Microsoft para a segurança e conformidade das melhores práticas com base em quadros comuns de conformidade. [Saiba mais sobre a Referência de Segurança do Azure](https://docs.microsoft.com/security/benchmark/azure/introduction).

Também pode adicionar padrões como:

- NIST SP 800-53
- SWIFT CSP CSCF-v2020
- UK Official and UK NHS
- Canada Federal PBMM
- Azure CIS 1.3.0
- CMMC Nível 3
- Ism da Nova Zelândia restrito

As normas são adicionadas ao painel de instrumentos à medida que ficam disponíveis.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Adicione um padrão regulamentar ao seu painel de instrumentos

Os seguintes passos explicam como adicionar um pacote para monitorizar o seu cumprimento a uma das normas regulamentares suportadas.

> [!NOTE]
> Para adicionar padrões ao seu dashboard, a subscrição deve ter o Azure Defender ativado. Além disso, apenas os utilizadores que são proprietários ou colaboradores de políticas têm as permissões necessárias para adicionar normas de conformidade. 

1. A partir da barra lateral do Security Center, **selecione conformidade regulamentar** para abrir o painel de conformidade regulamentar. Aqui pode ver os padrões de conformidade atualmente atribuídos às subscrições atualmente selecionadas.   

1. A partir do topo da página, **selecione Gerir** as políticas de conformidade . Aparece a página de Gestão de Políticas.

1. Selecione o grupo de subscrição ou gestão para o qual pretende gerir a postura de conformidade regulamentar. 

    > [!TIP]
    > Recomendamos a seleção da mais alta margem para a qual a norma é aplicável para que os dados de conformidade sejam agregados e rastreados para todos os recursos aninhados. 

1. Para adicionar os padrões relevantes à sua organização, clique em **Adicionar mais padrões**. 

1. A partir da página **de normas de conformidade regulamentar Adicionar,** pode procurar qualquer uma das normas disponíveis, incluindo:

    - **NIST SP 800-53**
    - **NIST SP 800 171**
    - **SWIFT CSP-CSCF v2020**
    - **UKO e UK NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.3.0**
    - **CMMC Nível 3**
    - **Ism da Nova Zelândia restrito**
    
    ![Adicionar normas regulamentares ao painel de conformidade regulamentar do Azure Security Center](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. **Selecione Adicionar** e insira todos os detalhes necessários para a iniciativa específica, tais como âmbito, parâmetros e remediação.

1. A partir da barra lateral do Security Center, **selecione conformidade regulamentar** novamente para voltar ao painel de conformidade regulamentar.

    O seu novo padrão aparece na sua lista de normas regulamentares & indústria. 

    > [!NOTE]
    > Pode levar algumas horas para que um padrão recém-adicionado apareça no painel de conformidade.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Painel de conformidade regulamentar" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Remova um padrão do seu painel de instrumentos

Se algum dos padrões regulamentares fornecidos não for relevante para a sua organização, é um processo simples para removê-los da UI. Isto permite-lhe personalizar ainda mais o painel de conformidade regulamentar, e focar-se apenas nos padrões que lhe são aplicáveis.

Para remover uma norma:

1. A partir do menu do Security Center, selecione **a política de segurança.**

1. Selecione a subscrição relevante a partir da qual pretende remover uma norma.

    > [!NOTE]
    > Pode remover um padrão de uma subscrição, mas não de um grupo de gestão. 

    A página da política de segurança abre. Para a subscrição selecionada, mostra a política de incumprimento, a indústria e os padrões regulamentares, e quaisquer iniciativas personalizadas que tenha criado.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Remoção de uma norma regulamentar do seu painel de conformidade regulamentar no Azure Security Center":::

1. Para a norma que pretende remover, selecione **Desativar**. Aparece uma janela de confirmação.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Confirme que realmente quer remover o padrão regulamentar que selecionou":::

1. Selecione **Yes** (Sim). O padrão será removido. 


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a **adicionar padrões de conformidade** para monitorizar o cumprimento das normas regulamentares e industriais.

Para obter material relacionado, consulte as seguintes páginas:

- [Referência de Segurança do Azure](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Painel de conformidade regulamentar do centro de segurança](security-center-compliance-dashboard.md) - Saiba como rastrear e exportar os seus dados de conformidade regulamentar com o Security Center e ferramentas externas
- [Trabalhar com políticas de segurança](tutorial-security-policy.md)