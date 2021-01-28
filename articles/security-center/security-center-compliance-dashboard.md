---
title: 'Tutorial: Verificações de conformidade regulamentar - Azure Security Center'
description: 'Tutorial: Saiba como melhorar a sua conformidade regulatória utilizando o Centro de Segurança Azure.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2021
ms.author: memildin
ms.openlocfilehash: f8d92ff0835948637761d7d2a98ec95a1c6dfccd
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944205"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Melhorar a conformidade regulamentar

O Azure Security Center ajuda a agilizar o processo de cumprimento dos requisitos de conformidade regulamentar, utilizando o **painel de conformidade regulamentar**. 

O Security Center realiza avaliações contínuas do seu ambiente em nuvem híbrida para analisar os fatores de risco de acordo com os controlos e boas práticas nos padrões aplicados às suas subscrições. O painel de instrumentos reflete o estado da sua conformidade com estas normas. 

Quando ativa o Centro de Segurança numa subscrição Azure, é automaticamente atribuído o [Benchmark de Segurança Azure](../security/benchmarks/introduction.md). Este referencial amplamente respeitado baseia-se nos controlos do [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e do Instituto Nacional de [Normalização e Tecnologia (NIST)](https://www.nist.gov/) com foco na segurança centrada na nuvem.

No painel de conformidade regulamentar, pode ver o estado de todas as avaliações dentro do seu ambiente no contexto de uma determinada norma ou regulamento. À medida que age sobre as recomendações e reduz os fatores de risco no seu ambiente, a sua postura de conformidade melhora.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Avalie a sua conformidade regulamentar utilizando o painel de conformidade regulamentar
> * Melhore a sua postura de conformidade tomando medidas sobre recomendações

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para passar pelas características abrangidas por este tutorial:

- [O Azure Defender](azure-defender.md) tem de estar ativado. Pode experimentar gratuitamente o Azure Defender durante 30 dias.
- Tem de ser inscrito com uma conta que tenha acesso ao leitor aos dados de conformidade da política **(O Leitor** de Segurança é insuficiente). O papel do **leitor global** para a subscrição funcionará. No mínimo, terá de ter funções **de Contribuidor de Política de Recursos** e **Administração de Segurança** atribuídas.

##  <a name="assess-your-regulatory-compliance"></a>Avalie a sua conformidade regulamentar

O painel de conformidade regulamentar mostra as suas normas de conformidade selecionadas com todos os seus requisitos, onde os requisitos suportados são mapeados para avaliações de segurança aplicáveis. O estado destas avaliações reflete o seu cumprimento da norma.

Utilize o painel de conformidade regulamentar para ajudar a concentrar a sua atenção nas lacunas de acordo com as normas e regulamentos que lhe interessam. Esta visão focada também permite monitorizar continuamente a sua conformidade ao longo do tempo dentro de ambientes dinâmicos de nuvem e híbrido.

1. A partir do menu do Centro de Segurança, **selecione conformidade regulamentar**.

    Na parte superior do ecrã encontra-se um dashboard com uma visão geral do seu estado de conformidade com o conjunto de regulamentos de conformidade suportados. Pode ver a sua pontuação geral de conformidade, e o número de avaliações de passagem vs. falhando associadas a cada padrão.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Painel de conformidade regulamentar":::

1. Selecione um separador para um padrão de conformidade que seja relevante para si (1). Verá em que subscrições o padrão é aplicado (2), e a lista de todos os controlos para esse padrão (3). Para os controlos aplicáveis, pode ver os detalhes das avaliações de passagem e falha associadas a esse controlo (4), bem como o número de recursos afetados (5). Alguns controlos estão acinzentados. Estes controlos não têm avaliações do Centro de Segurança associadas a eles. Verifique os requisitos para estes e avalie-os no seu ambiente por conta própria. Alguns destes podem estar relacionados com processos e não técnicos.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Explorando os detalhes do cumprimento de uma norma específica":::

1. Para gerar um relatório PDF com um resumo do seu estado de conformidade atual para uma determinada norma, selecione **Relatório de Descarregamento**.

    O relatório fornece um resumo de alto nível do seu estado de conformidade para a norma selecionada com base nos dados de avaliação do Centro de Segurança, e é organizado de acordo com os controlos dessa norma específica. O relatório pode ser partilhado com as partes interessadas relevantes e pode apresentar provas aos auditores internos e externos.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Relatório de conformidade de descarregamento":::

## <a name="improve-your-compliance-posture"></a>Melhore a sua postura de conformidade

Tendo em conta as informações no painel de conformidade regulamentar, pode melhorar a sua postura de conformidade resolvendo recomendações diretamente dentro do painel de instrumentos.

1.  Clique em qualquer uma das avaliações falhadas que aparecem no painel de instrumentos para ver os detalhes dessa recomendação. Cada recomendação inclui um conjunto de medidas de reparação que devem ser seguidas para resolver a questão.

1.  Pode selecionar um recurso específico para visualizar mais detalhes e resolver a recomendação para esse recurso. <br>Por exemplo, na norma **Azure CIS 1.1.0,** pode selecionar a recomendação **A encriptação do disco deve ser aplicada em máquinas virtuais**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Selecionar uma recomendação a partir de uma norma leva diretamente à página de detalhes da recomendação":::

1. Neste exemplo, quando selecionar **Tome medidas** a partir da página de detalhes da recomendação, você chega às páginas Azure Virtual Machine do portal Azure, onde pode abrir o separador **Segurança** e ativar a encriptação:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Tome o botão de ação na página de detalhes da recomendação leva às opções de remediação":::

    Para obter mais informações sobre como aplicar recomendações, consulte [as recomendações de segurança de implementação no Centro de Segurança Azure.](security-center-recommendations.md)

1.  Depois de tomar medidas para resolver recomendações, verá o impacto no relatório do painel de conformidade porque a sua pontuação de conformidade melhora.

    > [!NOTE]
    > As avaliações são executadas aproximadamente a cada 12 horas, pelo que verá o impacto nos seus dados de conformidade apenas após a próxima execução da avaliação relevante.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu a usar o painel de conformidade regulamentar do Centro de Segurança para:

-   Veja e monitorize a sua postura de conformidade, em relação aos padrões e regulamentos que são importantes para si.
-   Melhore o seu estado de conformidade, resolvendo recomendações relevantes e vendo a pontuação de conformidade melhorar.

O painel de conformidade regulamentar pode simplificar consideravelmente o processo de conformidade e reduzir significativamente o tempo necessário para recolher provas de conformidade para o seu ambiente Azure e híbrido.

Para saber mais, consulte estes artigos relacionados:

-   [Atualização para pacotes de conformidade dinâmicos no seu painel de conformidade regulamentar (Preview)](update-regulatory-compliance-packages.md) - Saiba mais sobre esta funcionalidade de pré-visualização que lhe permite atualizar as normas apresentadas no seu painel de conformidade regulamentar para os novos pacotes *dinâmicos.* Também pode adicionar novos pacotes de conformidade e monitorizar o cumprimento de normas adicionais. 
-   [Segurança monitorização de saúde no Azure Security Center](security-center-monitoring.md) - Saiba como monitorizar a saúde dos seus recursos Azure.
-   [Gerir recomendações de segurança no Azure Security Center](security-center-recommendations.md) - Saiba como usar recomendações no Azure Security Center para ajudar a proteger os seus recursos Azure.
-   [Melhore a sua Pontuação Segura no Centro de Segurança Azure](secure-score-security-controls.md) - Aprenda a priorizar vulnerabilidades e recomendações de segurança para melhorar a sua postura de segurança.
