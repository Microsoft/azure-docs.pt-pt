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
ms.date: 02/10/2021
ms.author: memildin
ms.openlocfilehash: fb8dc22c923b7b53a6263baa43046862af4d2f04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370274"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Melhorar a conformidade regulamentar

O Azure Security Center ajuda a agilizar o processo de cumprimento dos requisitos de conformidade regulamentar, utilizando o **painel de conformidade regulamentar**. 

O Security Center avalia continuamente o seu ambiente de nuvem híbrida para analisar os fatores de risco de acordo com os controlos e boas práticas nos padrões aplicados às suas subscrições. O painel de instrumentos reflete o estado da sua conformidade com estas normas. 

Quando ativa o Security Center numa subscrição do Azure, o [Benchmark de Segurança Azure](../security/benchmarks/introduction.md) é automaticamente atribuído a essa subscrição. Este referencial amplamente respeitado baseia-se nos controlos do [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e do Instituto Nacional de [Normalização e Tecnologia (NIST)](https://www.nist.gov/) com foco na segurança centrada na nuvem.

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
- Tem de ser inscrito numa conta que tenha acesso ao leitor aos dados de conformidade da política **(O Leitor de Segurança** é insuficiente). O papel do **leitor global** para a subscrição funcionará. No mínimo, terá de ter funções **de Contribuidor de Política de Recursos** e **Administração de Segurança** atribuídas.

##  <a name="assess-your-regulatory-compliance"></a>Avalie a sua conformidade regulamentar

O painel de conformidade regulamentar mostra as suas normas de conformidade selecionadas com todos os seus requisitos, onde os requisitos suportados são mapeados para avaliações de segurança aplicáveis. O estado destas avaliações reflete o seu cumprimento da norma.

Utilize o painel de conformidade regulamentar para ajudar a concentrar a sua atenção nas lacunas de acordo com as normas e regulamentos escolhidos. Esta visão focada também permite monitorizar continuamente a sua conformidade ao longo do tempo dentro de ambientes dinâmicos de nuvem e híbrido.

1. A partir do menu do Centro de Segurança, **selecione conformidade regulamentar**.

    Na parte superior do ecrã encontra-se um dashboard com uma visão geral do seu estado de conformidade com o conjunto de regulamentos de conformidade suportados. Você verá a sua pontuação geral de conformidade, e o número de avaliações de passagem vs. falhando associadas a cada padrão.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Painel de conformidade regulamentar" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Selecione um separador para um padrão de conformidade que seja relevante para si (1). Verá em que subscrições o padrão é aplicado (2), e a lista de todos os controlos para esse padrão (3). Para os controlos aplicáveis, pode ver os detalhes das avaliações de passagem e falha associadas a esse controlo (4) e o número de recursos afetados (5). Alguns controlos estão acinzentados. Estes controlos não têm avaliações do Centro de Segurança associadas a eles. Verifique os seus requisitos e avalie-os no seu ambiente. Alguns destes podem estar relacionados com processos e não técnicos.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Explorando os detalhes do cumprimento de uma norma específica":::

1. Para gerar um relatório PDF com um resumo do seu estado de conformidade atual para uma determinada norma, selecione **Relatório de Descarregamento**.

    O relatório fornece um resumo de alto nível do seu estado de conformidade para a norma selecionada com base nos dados de avaliação do Centro de Segurança. O relatório é organizado de acordo com os controlos desta norma específica. O relatório pode ser partilhado com as partes interessadas relevantes e pode apresentar provas aos auditores internos e externos.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Relatório de conformidade de descarregamento":::

## <a name="improve-your-compliance-posture"></a>Melhore a sua postura de conformidade

Utilizando a informação no painel de conformidade regulamentar, melhore a sua postura de conformidade resolvendo recomendações diretamente dentro do painel de instrumentos.

1.  Selecione qualquer uma das avaliações falhadas que aparecem no painel de instrumentos para ver os detalhes dessa recomendação. Cada recomendação inclui um conjunto de medidas de reparação para resolver a questão.

1.  Selecione um recurso específico para ver mais detalhes e resolva a recomendação para esse recurso. <br>Por exemplo, na norma **Azure CIS 1.1.0,** selecione a recomendação **A encriptação do disco deve ser aplicada em máquinas virtuais**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Selecionar uma recomendação a partir de uma norma leva diretamente à página de detalhes da recomendação":::

1. Neste exemplo, quando selecionar **Tome medidas** a partir da página de detalhes da recomendação, você chega às páginas Azure Virtual Machine do portal Azure, onde pode ativar a encriptação a partir do separador **Segurança:**

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Tome o botão de ação na página de detalhes da recomendação leva às opções de remediação":::

    Para obter mais informações sobre como aplicar recomendações, consulte [as recomendações de segurança de implementação no Centro de Segurança Azure.](security-center-recommendations.md)

1.  Depois de tomar medidas para resolver recomendações, verá o resultado no relatório do painel de conformidade porque a sua pontuação de conformidade melhora.

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

Por exemplo, pode querer que o Centro de Segurança envie um e-mail a um utilizador específico quando uma avaliação de conformidade falha. Em primeiro lugar, terá de criar a aplicação lógica (utilizando [as Aplicações Lógicas Azure)](../logic-apps/logic-apps-overview.md)e, em seguida, configurar o gatilho numa nova automatização de fluxos de trabalho, como explicado nas [respostas do Automamate aos gatilhos do Security Center](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Utilização de alterações nas avaliações de conformidade regulamentar para desencadear uma automatização do fluxo de trabalho" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>FAQ – Dashboard de conformidade regulamentar

- [Que normas são suportadas no painel de conformidade?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Porque é que alguns controlos parecem estar acinzentados?](#why-do-some-controls-appear-grayed-out)
- [Como posso remover um padrão incorporado, como PCI-DSS, ISO 27001 ou SOC2 TSP do painel de instrumentos?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Fiz a proposta alterada com base na recomendação, mas não está a ser refletida no painel de instrumentos.](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Que permissões preciso para aceder ao painel de conformidade?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [O painel de conformidade regulamentar não está a carregar para mim.](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Como posso ver um relatório de controlos de passagem e falha por norma no meu painel de instrumentos?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Como posso descarregar um relatório com dados de conformidade num formato diferente do PDF?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Como posso criar exceções para algumas das políticas do painel de conformidade regulamentar?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Que planos ou licenças do Azure Defender preciso para usar o painel de conformidade regulamentar?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Que normas são suportadas no painel de conformidade?
Por predefinição, o painel de conformidade regulamentar mostra-lhe o Benchmark de Segurança Azure. O Azure Security Benchmark é a autoria da Microsoft, diretrizes específicas para a segurança e as melhores práticas de conformidade baseadas em quadros comuns de conformidade. Saiba mais na introdução do [Azure Security Benchmark](../security/benchmarks/introduction.md).

Para acompanhar a sua conformidade com qualquer outro padrão, terá de adicioná-los explicitamente ao seu painel de instrumentos.
 
Pode adicionar padrões como Azure CIS 1.1.0 (novo), NIST SP 800-53 R4, NIST SP 800-171 R2, SWIFT CSP CSCF-v2020, UK Official and UK NHS, HIPAA HITRUST, Canada Federal PBMM, ISO 27001, SOC2-TSP e PCI-DSS 3.2.1.  
 
Mais normas serão adicionadas ao painel de instrumentos e incluídas nas informações sobre [Personalizar o conjunto de normas no seu painel de conformidade regulamentar](update-regulatory-compliance-packages.md).

### <a name="why-do-some-controls-appear-grayed-out"></a>Porque é que alguns controlos parecem estar acinzentados?
Para cada padrão de conformidade no painel de instrumentos, há uma lista dos controlos padrão. Para os controlos aplicáveis, pode ver os detalhes das avaliações de passagem e falha.

Alguns controlos estão acinzentados. Estes controlos não têm avaliações do Centro de Segurança associadas a eles. Alguns podem ser procedimentos ou processos relacionados, e, portanto, não podem ser verificados pelo Centro de Segurança. Alguns ainda não têm políticas ou avaliações automatizadas implementadas, mas terão no futuro. E alguns controlos podem ser a responsabilidade da plataforma, tal como explicado na [responsabilidade partilhada na nuvem.](../security/fundamentals/shared-responsibility.md) 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Como posso remover um padrão incorporado, como PCI-DSS, ISO 27001 ou SOC2 TSP do painel de instrumentos? 
Para personalizar o painel de conformidade regulamentar, e focar-se apenas nos padrões que lhe são aplicáveis, pode remover qualquer um dos padrões regulamentares apresentados que não sejam relevantes para a sua organização. Para remover uma norma, siga as instruções em [Remover uma norma do seu painel de instrumentos](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Fiz a proposta alterada com base na recomendação, mas não está a ser refletida no painel de instrumentos.
Depois de tomar medidas para resolver recomendações, aguarde 12 horas para ver as alterações aos seus dados de conformidade. As avaliações são efetuadas aproximadamente a cada 12 horas, pelo que verá o efeito nos seus dados de conformidade apenas após a execução das avaliações.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Que permissões preciso para aceder ao painel de conformidade?
Para visualizar os dados de conformidade, precisa de ter pelo menos o acesso **do Leitor** aos dados de conformidade da política; para que o Leitor de Segurança por si só não seja suficiente. Se você é um leitor global na subscrição, isso também será suficiente.

O conjunto mínimo de funções para aceder ao painel de instrumentos e gerir padrões é **o Contribuinte de Política de Recursos** e o Administrador de **Segurança.**


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>O painel de conformidade regulamentar não está a carregar para mim.
Para utilizar o painel de conformidade regulamentar, o Azure Security Center deve ter o Azure Defender ativado ao nível da subscrição. Se o painel de instrumentos não estiver a carregar corretamente, experimente os seguintes passos:

1. Limpe a cache do seu navegador.
1. Experimente um navegador diferente.
1. Tente abrir o painel de instrumentos a partir de diferentes localizações da rede.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Como posso ver um relatório de controlos de passagem e falha por norma no meu painel de instrumentos?
No painel de instrumentos principal, pode ver-se um relatório de controlos de passagem e falha para (1) os padrões de conformidade mais baixos do 'top 4' no painel de instrumentos. Para ver todo o estado dos controlos de passagem/falha, selecione (2) **Mostrar tudo *x*** (onde x é o número de padrões que está a seguir). Um plano de contexto apresenta o estado de conformidade de cada um dos seus padrões rastreados.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Secção sumária do painel de conformidade regulamentar":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Como posso descarregar um relatório com dados de conformidade num formato diferente do PDF?
Quando selecionar **Baixar relatório,** selecione o padrão e o formato (PDF ou CSV). O relatório resultante refletirá o conjunto atual de subscrições que selecionou no filtro do portal.

- O relatório PDF mostra um estado de resumo para o padrão selecionado
- O relatório CSV fornece resultados detalhados por recurso, uma vez que se refere a políticas associadas a cada controlo

Atualmente, não há suporte para o download de um relatório para uma política personalizada; apenas para as normas regulamentares fornecidas.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Como posso criar exceções para algumas das políticas do painel de conformidade regulamentar?
Para políticas que sejam integradas no Centro de Segurança e incluídas na pontuação segura, pode criar isenções para um ou mais recursos diretamente no portal, conforme explicado na [isenção de recursos e recomendações da sua pontuação segura.](exempt-resource.md)

Para outras políticas, pode criar uma isenção diretamente na própria política, seguindo as instruções na [estrutura de isenção da Política Azure.](../governance/policy/concepts/exemption-structure.md)


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Que planos ou licenças do Azure Defender preciso para usar o painel de conformidade regulamentar?
Se tiver algum dos pacotes Azure Defender ativados em qualquer um dos seus tipos de recursos Azure, tem acesso ao Painel de Conformidade Regulamentar, com todos os seus dados, no Centro de Segurança.





## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o painel de conformidade regulamentar do Centro de Segurança para:

> [!div class="checklist"]
> * Veja e monitorize a sua postura de conformidade relativamente aos padrões e regulamentos que são importantes para si.
> * Melhore o seu estado de conformidade, resolvendo recomendações relevantes e vendo a pontuação de conformidade melhorar.

O painel de conformidade regulamentar pode simplificar consideravelmente o processo de conformidade e reduzir significativamente o tempo necessário para recolher provas de conformidade para o seu ambiente Azure, híbrido e multi-nuvem.

Para saber mais, consulte estas páginas relacionadas:

- [Personalize o conjunto de padrões no seu painel de conformidade regulamentar](update-regulatory-compliance-packages.md) - Saiba como selecionar quais as normas que aparecem no seu painel de conformidade regulamentar. 
- [Gerir recomendações de segurança no Azure Security Center](security-center-recommendations.md) - Saiba como usar recomendações no Centro de Segurança para ajudar a proteger os seus recursos Azure.