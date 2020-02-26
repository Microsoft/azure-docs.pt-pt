---
title: 'Tutorial: Controlos de conformidade regulamentar - Centro de Segurança Azure'
description: 'Tutorial: Aprenda a melhorar a sua conformidade regulatória utilizando o Azure Security Center.'
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
ms.date: 11/12/2019
ms.author: memildin
ms.openlocfilehash: 1a6999c05c0b3dbaf572b376412f666c50c23df7
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604450"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Melhorar a sua conformidade regulamentar
---

O Azure Security Center ajuda a agilizar o processo para cumprir os requisitos de conformidade regulamentar, utilizando o painel de **controlo regulamentar.** No painel de instrumentos, o Security Center fornece insights sobre a sua postura de conformidade com base em avaliações contínuas do seu ambiente Azure. O Security Center analisa fatores de risco no seu ambiente híbrido de nuvem de acordo com as melhores práticas de segurança. Estas avaliações são mapeadas para controlos de conformidade a partir de um conjunto de normas apoiadas. No dashboard de conformidade regulamentar, pode ver o estado de todas as avaliações no seu ambiente no contexto de uma norma ou regulação específica. À medida que aja com base nas recomendações e reduza os fatores de risco no seu ambiente, a sua postura de conformidade melhora.

Neste tutorial, vai aprender a:

-   Avalie a sua conformidade regulamentar utilizando o dashboard de conformidade regulamentar

-   Melhore a sua postura de conformidade tomando medidas sobre recomendações

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para analisar as funcionalidades abrangidas por este tutorial, deve ter o nível de preços Standard do Security Center. Pode tentar o Nível Padrão do Centro de Segurança sem custos.
Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). O início rápido [Onboard your Azure subscription to Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started) (Incluir a sua subscrição do Azure no Centro de Segurança Standard) explica-lhe como atualizar para Standard.

##  <a name="assess-your-regulatory-compliance"></a>Avaliar a sua conformidade regulamentar

O Security Center avalia continuamente a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. Estas avaliações são apresentadas como recomendações, que se concentram em melhorar a sua higiene de segurança. No dashboard de conformidade regulamentar, pode ver um conjunto de normas de conformidade com todos os seus requisitos, onde os requisitos suportados são mapeados para avaliações de segurança aplicáveis. Isto permite-lhe visualizar a sua postura de conformidade em relação à norma, com base no estado destas avaliações.

A visão do dashboard de conformidade regulamentar pode ajudar a concentrar a sua atenção nas lacunas em conformidade com uma norma ou regulamento que é importante para si. Esta vista focada também permite monitorizar continuamente a sua pontuação de conformidade ao longo do tempo dentro de ambientes dinâmicos em nuvem e ambientes híbridos.

>[!NOTE]
> Por predefinição, o Security Center suporta as seguintes normas regulamentares: Azure CIS, PCI DSS 3.2, ISO 27001 e SOC TSP. 
>
> A funcionalidade [dynamic compliance packages (pré-visualização)](update-regulatory-compliance-packages.md) permite-lhe atualizar as normas mostradas no seu painel de conformidade regulamentar para os novos pacotes *dinâmicos.* Também pode utilizar a mesma funcionalidade de pré-visualização para adicionar novos pacotes de conformidade e monitorizar a sua conformidade com as normas adicionais. 

1.  No menu principal do Centro de Segurança, em termos de **POLÍTICA e CONFORMIDADE** selecione conformidade **regulamentar.** <br>
Na parte superior do ecrã, vê um dashboard com uma visão geral do seu estado de conformidade com o conjunto de regulamentos de conformidade suportados. Pode ver a sua pontuação de conformidade geral, e o número de avaliações de passagem vs. falta de dados associadas a cada padrão.

    ![descrição do computador alta confiança](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Selecione o separador de uma norma de conformidade relevante para si. Verá a lista de todos os controlos dessa norma. Nos controlos aplicáveis, poderá ver os detalhes das avaliações bem-sucedidas e com falhas associadas a esse controlo. Alguns controlos estão acinzentados. Estes controlos não têm avaliações do Centro de Segurança associadas a eles. Verifique os requisitos para estes e avalie-os no seu ambiente por conta própria. Algumas delas podem estar relacionadas com processos e não técnicas.

    ![separador de conformidade](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Para gerar e descarregar um relatório PDF resumindo o seu estado de conformidade atual para um determinado padrão, clique no **relatório Download**.

    O relatório fornece um resumo de alto nível do seu estado de conformidade para a norma selecionada com base em dados de avaliação do Security Center, e é organizado de acordo com os controlos dessa norma específica. O relatório pode ser partilhado com as partes interessadas relevantes e pode servir para fornecer provas aos auditores internos e externos.

    ![transferir](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Melhore a sua postura de conformidade

Dada a informação no dashboard de conformidade regulamentar, pode melhorar a sua postura de conformidade resolvendo recomendações diretamente dentro do dashboard.

1.  Clique em qualquer uma das avaliações falhadas que aparecem no painel de instrumentos para ver os detalhes para essa recomendação. Cada recomendação inclui um conjunto de medidas de reparação que devem ser seguidas para resolver a questão.

1.  Pode selecionar um recurso específico para ver mais detalhes e resolver a recomendação para esse recurso. <br>Por exemplo, no separador **padrão Azure CIS,** pode clicar na recomendação **Exigir transferência segura para a conta**de armazenamento .

    ![recomendação de conformidade](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. Ao clicar nas informações de recomendação e selecionar um recurso pouco saudável, leva-o diretamente à experiência de permitir uma transferência de **armazenamento segura** dentro do portal Azure.

    Para obter mais informações sobre como aplicar recomendações, consulte a [implementação de recomendações](security-center-recommendations.md)de segurança no Centro de Segurança Azure.

    ![recomendação de conformidade](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  Depois de tomar medidas para resolver recomendações, verá o impacto no relatório do painel de conformidade porque a sua pontuação de conformidade melhora.

    > [!NOTE]
    > As avaliações são executadas aproximadamente a cada 12 horas, portanto, verá o impacto nos dados de conformidade apenas depois da execução das avaliações.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o painel de conformidade regulamentar do Security Center para:

-   Veja e monitorize a sua postura de conformidade, em relação às normas e regulamentos que são importantes para si.

-   Melhore o seu estado de conformidade, resolvendo recomendações relevantes e observando a pontuação de conformidade melhorar.

O dashboard de conformidade regulamentar pode simplificar consideravelmente o processo de conformidade e reduzir significativamente o tempo necessário para recolher provas de conformidade para o seu ambiente Azure e híbrido.

Para saber mais ver:

-   [Atualização para pacotes dinâmicos de conformidade no seu dashboard de conformidade regulamentar (Pré-visualização)](update-regulatory-compliance-packages.md) - Conheça esta funcionalidade de pré-visualização que lhe permite atualizar as normas mostradas no seu dashboard de conformidade regulamentar para os novos pacotes *dinâmicos.* Também pode adicionar novos pacotes de conformidade e monitorizar o cumprimento das normas adicionais. 

-   [Monitorização da saúde de segurança no Azure Security Center](security-center-monitoring.md) - Saiba como monitorizar a saúde dos seus recursos Azure.

-   [Gerir recomendações](security-center-recommendations.md) de segurança no Azure Security Center - Aprenda a usar recomendações no Azure Security Center para ajudar a proteger os seus recursos Azure.

-   [Melhore a sua Pontuação Segura no Centro](security-center-secure-score.md) de Segurança Azure - Aprenda a priorizar vulnerabilidades e recomendações de segurança para melhorar a sua postura de segurança.