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
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 20a464011e5a8d37a6215b222323ca989e02ac04
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550932"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Melhorar a conformidade regulamentar

O Azure Security Center ajuda a agilizar o processo de cumprimento dos requisitos de conformidade regulamentar, utilizando o **painel de conformidade regulamentar**. 

O Security Center avalia continuamente o seu ambiente de nuvem híbrida para analisar os fatores de risco de acordo com os controlos e boas práticas nos padrões aplicados às suas subscrições. O painel de instrumentos reflete o estado da sua conformidade com estas normas. 

Quando ativa o Centro de Segurança numa subscrição do Azure, é automaticamente atribuído o [Benchmark de Segurança Azure](../security/benchmarks/introduction.md). Este referencial amplamente respeitado baseia-se nos controlos do [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e do Instituto Nacional de [Normalização e Tecnologia (NIST)](https://www.nist.gov/) com foco na segurança centrada na nuvem.

O painel de conformidade regulamentar mostra o estado de todas as avaliações dentro do seu ambiente para as normas e regulamentos escolhidos. À medida que age sobre as recomendações e reduz os fatores de risco no seu ambiente, a sua postura de conformidade melhora.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Avalie a sua conformidade regulamentar utilizando o painel de conformidade regulamentar
> * Melhore a sua postura de conformidade tomando medidas sobre recomendações
> * Alertas de configuração sobre alterações à sua postura de conformidade
> * Exporte os seus dados de conformidade como um fluxo contínuo e como instantâneos semanais

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para passar pelas características abrangidas por este tutorial:

- [O Azure Defender](azure-defender.md) tem de estar ativado. Pode experimentar gratuitamente o Azure Defender durante 30 dias.
- Tem de ser inscrito com uma conta que tenha acesso ao leitor aos dados de conformidade da política **(O Leitor** de Segurança é insuficiente). O papel do **leitor global** para a subscrição funcionará. No mínimo, terá de ter funções **de Contribuidor de Política de Recursos** e **Administração de Segurança** atribuídas.

##  <a name="assess-your-regulatory-compliance"></a>Avalie a sua conformidade regulamentar

O painel de conformidade regulamentar mostra as suas normas de conformidade selecionadas com todos os seus requisitos, onde os requisitos suportados são mapeados para avaliações de segurança aplicáveis. O estado destas avaliações reflete o seu cumprimento da norma.

Utilize o painel de conformidade regulamentar para ajudar a concentrar a sua atenção nas lacunas de acordo com as normas e regulamentos escolhidos. Esta visão focada também permite monitorizar continuamente a sua conformidade ao longo do tempo dentro de ambientes dinâmicos de nuvem e híbrido.

1. A partir do menu do Centro de Segurança, **selecione conformidade regulamentar**.

    Na parte superior do ecrã encontra-se um dashboard com uma visão geral do seu estado de conformidade com o conjunto de regulamentos de conformidade suportados. Você verá a sua pontuação geral de conformidade, e o número de avaliações de passagem vs. falhando associadas a cada padrão.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Painel de conformidade regulamentar" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Selecione um separador para um padrão de conformidade que seja relevante para si (1). Verá em que subscrições o padrão é aplicado (2), e a lista de todos os controlos para esse padrão (3). Para os controlos aplicáveis, pode ver os detalhes das avaliações de passagem e falha associadas a esse controlo (4), bem como o número de recursos afetados (5). Alguns controlos estão acinzentados. Estes controlos não têm avaliações do Centro de Segurança associadas a eles. Verifique os requisitos para estes e avalie-os no seu ambiente por conta própria. Alguns destes podem estar relacionados com processos e não técnicos.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Explorando os detalhes do cumprimento de uma norma específica":::

1. Para gerar um relatório PDF com um resumo do seu estado de conformidade atual para uma determinada norma, selecione **Relatório de Descarregamento**.

    O relatório fornece um resumo de alto nível do seu estado de conformidade para a norma selecionada com base nos dados de avaliação do Centro de Segurança, e é organizado de acordo com os controlos dessa norma específica. O relatório pode ser partilhado com as partes interessadas relevantes e pode apresentar provas aos auditores internos e externos.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Relatório de conformidade de descarregamento":::

## <a name="improve-your-compliance-posture"></a>Melhore a sua postura de conformidade

Utilizando a informação no painel de conformidade regulamentar, melhore a sua postura de conformidade resolvendo recomendações diretamente dentro do painel de instrumentos.

1.  Clique em qualquer uma das avaliações falhadas que aparecem no painel de instrumentos para ver os detalhes dessa recomendação. Cada recomendação inclui um conjunto de medidas de reparação que devem ser seguidas para resolver a questão.

1.  Selecione um recurso específico para ver mais detalhes e resolva a recomendação para esse recurso. <br>Por exemplo, na norma **Azure CIS 1.1.0,** selecione a recomendação **A encriptação do disco deve ser aplicada em máquinas virtuais**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Selecionar uma recomendação a partir de uma norma leva diretamente à página de detalhes da recomendação":::

1. Neste exemplo, quando selecionar **Tome medidas** a partir da página de detalhes da recomendação, você chega às páginas Azure Virtual Machine do portal Azure, onde pode ativar a encriptação a partir do separador **Segurança:**

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Tome o botão de ação na página de detalhes da recomendação leva às opções de remediação":::

    Para obter mais informações sobre como aplicar recomendações, consulte [as recomendações de segurança de implementação no Centro de Segurança Azure.](security-center-recommendations.md)

1.  Depois de tomar medidas para resolver recomendações, verá o impacto no relatório do painel de conformidade porque a sua pontuação de conformidade melhora.

    > [!NOTE]
    > As avaliações são executadas aproximadamente a cada 12 horas, pelo que verá o impacto nos seus dados de conformidade apenas após a próxima execução da avaliação relevante.


## <a name="export-your-compliance-status-data"></a>Exporte os seus dados de estado de conformidade

Se pretender acompanhar o seu estado de conformidade com outras ferramentas de monitorização no seu ambiente, o Security Center inclui um mecanismo de exportação para tornar isto simples. Configure **a exportação contínua** para enviar dados selecionados para um Azure Event Hub ou um espaço de trabalho Log Analytics.

Utilize dados de exportação contínuos para um Azure Event Hub ou um espaço de trabalho Log Analytics:

- Exportar todos os dados de conformidade regulamentar num **fluxo contínuo:**

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Exportar continuamente um fluxo de dados de conformidade regulamentar" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Exportar **instantâneos semanais** dos seus dados de conformidade regulamentar:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Exporte continuamente um instantâneo semanal de dados de conformidade regulamentar" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

Também pode exportar um **relatório PDF/CSV** dos seus dados de conformidade diretamente do painel de conformidade regulamentar:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Exporte os seus dados de conformidade regulamentar como um relatório PDF ou CSV" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Saiba mais sobre [os dados do Centro de Segurança de exportação contínua.](continuous-export.md)


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Executar automatizações de fluxo de trabalho quando houver alterações na sua conformidade

A funcionalidade de automatização de fluxos de trabalho do Security Center pode desencadear aplicações lógicas sempre que uma das suas avaliações de conformidade regulamentar mudar de estado.

Por exemplo, pode querer que o Centro de Segurança envie um e-mail a um utilizador específico quando uma avaliação de conformidade falha. Você precisará criar a aplicação lógica (usando [Azure Logic Apps](../logic-apps/logic-apps-overview.md)) primeiro e, em seguida, configurar o gatilho em uma nova automatização de fluxo de trabalho, como explicado nas [respostas do Automamate para o Centro de Segurança desencadeia](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Utilização de alterações nas avaliações de conformidade regulamentar para desencadear uma automatização do fluxo de trabalho" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o painel de conformidade regulamentar do Centro de Segurança para:

- Veja e monitorize a sua postura de conformidade relativamente aos padrões e regulamentos que são importantes para si.
- Melhore o seu estado de conformidade, resolvendo recomendações relevantes e vendo a pontuação de conformidade melhorar.

O painel de conformidade regulamentar pode simplificar consideravelmente o processo de conformidade e reduzir significativamente o tempo necessário para recolher provas de conformidade para o seu ambiente Azure, híbrido e multi-nuvem.

Para saber mais, consulte estas páginas relacionadas:

- [Personalize o conjunto de padrões no seu painel de conformidade regulamentar](update-regulatory-compliance-packages.md) - Saiba como selecionar quais as normas que aparecem no seu painel de conformidade regulamentar. 
- [Segurança monitorização de saúde no Azure Security Center](security-center-monitoring.md) - Saiba como monitorizar a saúde dos seus recursos Azure.
- [Gerir recomendações de segurança no Azure Security Center](security-center-recommendations.md) - Saiba como usar recomendações no Azure Security Center para ajudar a proteger os seus recursos Azure.