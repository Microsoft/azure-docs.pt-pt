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
ms.date: 10/11/2020
ms.author: memildin
ms.openlocfilehash: bbc36dbb2a17d379d31a9a235898500aea36247d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533915"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Melhorar a conformidade regulamentar

O Azure Security Center ajuda a agilizar o processo de cumprimento dos requisitos de conformidade regulamentar, utilizando o **painel de conformidade regulamentar**. No painel de instrumentos, o Security Center fornece informações sobre a sua postura de conformidade com base em avaliações contínuas do seu ambiente Azure. O Security Center analisa fatores de risco no seu ambiente de nuvem híbrida de acordo com as melhores práticas de segurança. Estas avaliações são mapeadas para controlos de conformidade de um conjunto de normas apoiadas. No painel de conformidade regulamentar, pode ver o estado de todas as avaliações dentro do seu ambiente no contexto de uma determinada norma ou regulamento. À medida que age sobre as recomendações e reduz os fatores de risco no seu ambiente, a sua postura de conformidade melhora.

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

O Security Center avalia continuamente a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. Estas avaliações são apresentadas como recomendações, que se concentram em melhorar a sua higiene de segurança. No painel de conformidade regulamentar, pode ver um conjunto de normas de conformidade com todos os seus requisitos, onde os requisitos suportados são mapeados para avaliações de segurança aplicáveis. Isto permite-lhe visualizar a sua postura de conformidade em relação à norma, com base no estado destas avaliações.

A visão do painel de conformidade regulamentar pode ajudar a concentrar a sua atenção nas lacunas em conformidade com uma norma ou regulamento que é importante para si. Esta visão focada também permite monitorizar continuamente a sua pontuação de conformidade ao longo do tempo dentro de ambientes dinâmicos de nuvem e híbrido.

>[!NOTE]
> Por padrão, o Security Center suporta as seguintes normas regulamentares: Azure CIS, PCI DSS 3.2, ISO 27001 e SOC TSP. 
>
> A funcionalidade [de pacotes de conformidade dinâmica (pré-visualização)](update-regulatory-compliance-packages.md) permite-lhe atualizar as normas apresentadas no seu painel de conformidade regulamentar para os novos pacotes *dinâmicos.* Também pode utilizar a mesma funcionalidade de pré-visualização para adicionar novos pacotes de conformidade e monitorizar o cumprimento de normas adicionais. 

1. A partir do menu do Centro de Segurança, **selecione conformidade regulamentar**. <br>
Na parte superior do ecrã, vê-se um dashboard com uma visão geral do seu estado de conformidade com o conjunto de regulamentos de conformidade suportados. Pode ver a sua pontuação geral de conformidade, e o número de avaliações de passagem vs. falhando associadas a cada padrão.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Painel de conformidade regulamentar":::

1. Selecione um separador para um padrão de conformidade que seja relevante para si (1). Verá em que subscrições o padrão é aplicado (2), e a lista de todos os controlos para esse padrão (3). Para os controlos aplicáveis, pode ver os detalhes das avaliações de passagem e falha associadas a esse controlo (4), bem como o número de recursos afetados (5). Alguns controlos estão acinzentados. Estes controlos não têm avaliações do Centro de Segurança associadas a eles. Verifique os requisitos para estes e avalie-os no seu ambiente por conta própria. Alguns destes podem estar relacionados com processos e não técnicos.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Explorando os detalhes do cumprimento de uma norma específica":::

1. Para gerar e descarregar um relatório PDF que ressoe o seu estado de conformidade atual para uma determinada norma, clique em **Baixar o relatório**.

    O relatório fornece um resumo de alto nível do seu estado de conformidade para a norma selecionada com base nos dados de avaliação do Centro de Segurança, e é organizado de acordo com os controlos dessa norma específica. O relatório pode ser partilhado com as partes interessadas relevantes e pode apresentar provas aos auditores internos e externos.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Relatório de conformidade de descarregamento":::

## <a name="improve-your-compliance-posture"></a>Melhore a sua postura de conformidade

Tendo em conta as informações no painel de conformidade regulamentar, pode melhorar a sua postura de conformidade resolvendo recomendações diretamente dentro do painel de instrumentos.

1.  Clique em qualquer uma das avaliações falhadas que aparecem no painel de instrumentos para ver os detalhes dessa recomendação. Cada recomendação inclui um conjunto de medidas de reparação que devem ser seguidas para resolver a questão.

1.  Pode selecionar um recurso específico para visualizar mais detalhes e resolver a recomendação para esse recurso. <br>Por exemplo, na **norma Azure CIS 1.1.0 (Nova),** pode selecionar a recomendação **A encriptação do disco deve ser aplicada em máquinas virtuais**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Selecionar uma recomendação a partir de uma norma leva diretamente à página de detalhes da recomendação":::

1. Neste exemplo, quando selecionar **Tome medidas** a partir da página de detalhes da recomendação, você chega às páginas Azure Virtual Machine do portal Azure, onde pode abrir o separador **Segurança** e ativar a encriptação:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Tome o botão de ação na página de detalhes da recomendação leva às opções de remediação":::

    Para obter mais informações sobre como aplicar recomendações, consulte [as recomendações de segurança de implementação no Centro de Segurança Azure.](security-center-recommendations.md)

1.  Depois de tomar medidas para resolver recomendações, verá o impacto no relatório do painel de conformidade porque a sua pontuação de conformidade melhora.

    > [!NOTE]
    > As avaliações são executadas aproximadamente a cada 12 horas, pelo que verá o impacto nos seus dados de conformidade apenas após a próxima execução da avaliação relevante.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o painel de conformidade regulamentar do Centro de Segurança para:

-   Veja e monitorize a sua postura de conformidade, em relação aos padrões e regulamentos que são importantes para si.
-   Melhore o seu estado de conformidade, resolvendo recomendações relevantes e vendo a pontuação de conformidade melhorar.

O painel de conformidade regulamentar pode simplificar consideravelmente o processo de conformidade e reduzir significativamente o tempo necessário para recolher provas de conformidade para o seu ambiente Azure e híbrido.

Para saber mais, consulte estes artigos relacionados:

-   [Atualização para pacotes de conformidade dinâmicos no seu painel de conformidade regulamentar (Preview)](update-regulatory-compliance-packages.md) - Saiba mais sobre esta funcionalidade de pré-visualização que lhe permite atualizar as normas apresentadas no seu painel de conformidade regulamentar para os novos pacotes *dinâmicos.* Também pode adicionar novos pacotes de conformidade e monitorizar o cumprimento de normas adicionais. 
-   [Segurança monitorização de saúde no Azure Security Center](security-center-monitoring.md) - Saiba como monitorizar a saúde dos seus recursos Azure.
-   [Gerir recomendações de segurança no Azure Security Center](security-center-recommendations.md) - Saiba como usar recomendações no Azure Security Center para ajudar a proteger os seus recursos Azure.
-   [Melhore a sua Pontuação Segura no Centro de Segurança Azure](secure-score-security-controls.md) - Aprenda a priorizar vulnerabilidades e recomendações de segurança para melhorar a sua postura de segurança.
