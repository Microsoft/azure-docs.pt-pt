---
title: Melhorar a conformidade regulatória usando a central de segurança do Azure | Microsoft Docs
description: 'Tutorial: Saiba como melhorar sua conformidade regulatória usando a central de segurança do Azure.'
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
ms.date: 4/30/2019
ms.author: memildin
ms.openlocfilehash: 20842997c5df81835024a6f458cd863b4e4d78b0
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202426"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Melhore a sua conformidade regulamentar
---

A central de segurança do Azure ajuda a simplificar o processo para atender aos requisitos de conformidade regulatória, usando o painel de conformidade regulatória. No painel, a central de segurança fornece informações sobre sua postura de conformidade com base em avaliações contínuas de seu ambiente do Azure. As avaliações realizadas pela central de segurança analisam fatores de risco em seu ambiente de nuvem híbrida de acordo com as práticas recomendadas de segurança. Essas avaliações são mapeadas para controles de conformidade de um conjunto de padrões com suporte. No painel conformidade regulatória, você tem uma visão clara do status de todas essas avaliações em seu ambiente no contexto de um padrão ou regulamento específico. Conforme você atua nas recomendações e reduz os fatores de risco em seu ambiente, você pode ver sua postura de conformidade melhorar.

Neste tutorial, vai aprender a:

-   Avalie sua conformidade regulatória usando o painel de conformidade regulatória

-   Melhore sua postura de conformidade executando ações sobre recomendações

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para percorrer os recursos abordados neste tutorial, você deve ter o tipo de preço Standard da central de segurança. Você pode experimentar a central de segurança Standard sem nenhum custo.
Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). O início rápido [Onboard your Azure subscription to Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started) (Incluir a sua subscrição do Azure no Centro de Segurança Standard) explica-lhe como atualizar para Standard.

##  <a name="assess-your-regulatory-compliance"></a>Avalie sua conformidade regulatória

A central de segurança avalia continuamente a configuração de seus recursos para identificar problemas e vulnerabilidades de segurança. Essas avaliações são apresentadas como recomendações, que se concentram em melhorar sua higiene de segurança. No painel de conformidade regulatória, você pode exibir um conjunto de padrões de conformidade com todos os seus requisitos, onde os requisitos com suporte são mapeados para avaliações de segurança aplicáveis. Isso permite que você exiba sua postura de conformidade em relação ao padrão, com base no status dessas avaliações.

A exibição do painel conformidade regulatória pode ajudar a concentrar sua atenção nas lacunas de conformidade com um padrão ou regulamento que seja importante para você. Essa exibição focalizada também permite que você monitore continuamente sua pontuação de conformidade ao longo do tempo em ambientes híbridos e de nuvem dinâmica.

>[!NOTE]
> Atualmente, os padrões regulatórios com suporte são: Azure CIS, PCI DSS 3,2, ISO 27001 e SOC TSP. As normas adicionais serão apresentadas no dashboard à medida que forem desenvolvidas.
1.  No menu principal da central de segurança, em **política &AMP; conformidade** , selecione **conformidade regulatória**. <br>
Na parte superior da tela, você verá um painel com uma visão geral do status de conformidade com o conjunto de regulamentos de conformidade com suporte. Você pode ver sua pontuação de conformidade geral e o número de avaliações de passagem vs. com falha associadas a cada padrão.

    ![alta confiança da descrição do computador](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  Selecione o separador de uma norma de conformidade relevante para si. Verá a lista de todos os controlos dessa norma. Nos controlos aplicáveis, poderá ver os detalhes das avaliações bem-sucedidas e com falhas associadas a esse controlo. Alguns controlos estão desativados. Estes controlos não têm nenhuma avaliação do Centro de Segurança associada. Você precisa analisar os requisitos para eles e avaliá-los no seu ambiente por conta própria. Alguns deles podem ser relacionados ao processo e não técnicos.

    ![guia conformidade](./media/security-center-compliance-dashboard/compliance-pci.png)

3. Selecione a guia **tudo** para ver uma exibição de todas as recomendações relevantes da central de segurança e seus padrões associados. Essa exibição pode ser útil para identificar todos os padrões diferentes afetados por uma recomendação específica. <br> Você pode usar essa exibição para priorizar as recomendações que precisa resolver. Por exemplo, se você vir que a recomendação **habilitar MFA para contas com permissões de proprietário em sua assinatura** está falhando em vários recursos e está associada a vários padrões, a resolução dessa recomendação terá um alto impacto sobre sua pontuação de conformidade geral.

    ![impacto da Pontuação de conformidade](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

1. Para gerar e baixar um relatório em PDF resumindo seu status de conformidade atual para um padrão específico, clique em **baixar relatório**.

    O relatório fornece um resumo de alto nível de seu status de conformidade para o padrão selecionado com base nos dados de avaliações da central de segurança e é organizado de acordo com os controles desse padrão específico. O relatório pode ser compartilhado com os participantes relevantes e pode servir para fornecer evidências para auditores internos e externos.

    ![download](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Melhore sua postura de conformidade

Dadas as informações no painel de conformidade regulatória, você pode melhorar sua postura de conformidade resolvendo as recomendações diretamente no painel.

1.  Clique em qualquer uma das avaliações com falha que aparecem no painel para exibir os detalhes dessa recomendação. Cada recomendação inclui um conjunto de etapas de correção que devem ser seguidas para resolver o problema.

2.  Você pode selecionar um recurso específico para exibir mais detalhes e resolver a recomendação para esse recurso. <br>Por exemplo, na guia **padrão do Azure cis** , você pode clicar na recomendação **exigir transferência segura para a conta de armazenamento**.

    ![recomendação de conformidade](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. À medida que você clica nas informações de recomendação e seleciona um recurso não íntegro, ele conduz você diretamente à experiência de habilitar a **transferência de armazenamento seguro** dentro do portal do Azure.<br>Para obter mais informações sobre como aplicar recomendações, veja [implementar recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md).

    ![recomendação de conformidade](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  Depois de executar uma ação para resolver as recomendações, você verá o impacto no relatório do painel de conformidade, pois a pontuação de conformidade melhora.

    > [!NOTE]
    > As avaliações são executadas aproximadamente a cada 12 horas, portanto, verá o impacto nos dados de conformidade apenas depois da execução das avaliações.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a usar o painel de conformidade regulatória da central de segurança para:

-   Exiba e monitore sua postura de conformidade em relação aos padrões e regulamentos que são importantes para você.

-   Melhore seu status de conformidade resolvendo recomendações relevantes e observando a melhor pontuação de conformidade.

O painel de conformidade regulatória pode simplificar bastante o processo de conformidade e reduzir significativamente o tempo necessário para coletar evidências de conformidade para seu ambiente híbrido e do Azure.

Para saber mais sobre a central de segurança, consulte:

-   [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.

-   [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md)– saiba como usar as recomendações na central de segurança do Azure para ajudar a proteger os recursos do Azure.

-   [Melhorar sua pontuação segura na central de segurança do Azure](security-center-secure-score.md)– saiba como priorizar vulnerabilidades e recomendações de segurança para melhorar a sua postura de segurança.

-   [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
