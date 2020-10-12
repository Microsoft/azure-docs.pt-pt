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
ms.date: 11/12/2019
ms.author: memildin
ms.openlocfilehash: 7f235f9255df49c096e224e1754ad2bbdf664423
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90895812"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Melhorar a sua conformidade regulamentar
---

O Azure Security Center ajuda a agilizar o processo de cumprimento dos requisitos de conformidade regulamentar, utilizando o **painel de conformidade regulamentar**. No painel de instrumentos, o Security Center fornece informações sobre a sua postura de conformidade com base em avaliações contínuas do seu ambiente Azure. O Security Center analisa fatores de risco no seu ambiente de nuvem híbrida de acordo com as melhores práticas de segurança. Estas avaliações são mapeadas para controlos de conformidade de um conjunto de normas apoiadas. No painel de conformidade regulamentar, pode ver o estado de todas as avaliações dentro do seu ambiente no contexto de uma determinada norma ou regulamento. À medida que age sobre as recomendações e reduz os fatores de risco no seu ambiente, a sua postura de conformidade melhora.

Neste tutorial, vai aprender a:

-   Avalie a sua conformidade regulamentar utilizando o painel de conformidade regulamentar

-   Melhore a sua postura de conformidade tomando medidas sobre recomendações

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para passar pelas funcionalidades abrangidas por este tutorial, [o Azure Defender](azure-defender.md) tem de estar ativado. Pode experimentar gratuitamente o Azure Defender durante 30 dias.

##  <a name="assess-your-regulatory-compliance"></a>Avalie a sua conformidade regulamentar

O Security Center avalia continuamente a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. Estas avaliações são apresentadas como recomendações, que se concentram em melhorar a sua higiene de segurança. No painel de conformidade regulamentar, pode ver um conjunto de normas de conformidade com todos os seus requisitos, onde os requisitos suportados são mapeados para avaliações de segurança aplicáveis. Isto permite-lhe visualizar a sua postura de conformidade em relação à norma, com base no estado destas avaliações.

A visão do painel de conformidade regulamentar pode ajudar a concentrar a sua atenção nas lacunas em conformidade com uma norma ou regulamento que é importante para si. Esta visão focada também permite monitorizar continuamente a sua pontuação de conformidade ao longo do tempo dentro de ambientes dinâmicos de nuvem e híbrido.

>[!NOTE]
> Por padrão, o Security Center suporta as seguintes normas regulamentares: Azure CIS, PCI DSS 3.2, ISO 27001 e SOC TSP. 
>
> A funcionalidade [de pacotes de conformidade dinâmica (pré-visualização)](update-regulatory-compliance-packages.md) permite-lhe atualizar as normas apresentadas no seu painel de conformidade regulamentar para os novos pacotes *dinâmicos.* Também pode utilizar a mesma funcionalidade de pré-visualização para adicionar novos pacotes de conformidade e monitorizar o cumprimento de normas adicionais. 

1.  No menu principal do Centro de Segurança, no âmbito **da POLICY & conformidade** selecione conformidade **regulamentar**. <br>
Na parte superior do ecrã, vê-se um dashboard com uma visão geral do seu estado de conformidade com o conjunto de regulamentos de conformidade suportados. Pode ver a sua pontuação geral de conformidade, e o número de avaliações de passagem vs. falhando associadas a cada padrão.

    ![descrição do computador alta confiança](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Selecione o separador de uma norma de conformidade relevante para si. Verá a lista de todos os controlos dessa norma. Nos controlos aplicáveis, poderá ver os detalhes das avaliações bem-sucedidas e com falhas associadas a esse controlo. Alguns controlos estão acinzentados. Estes controlos não têm avaliações do Centro de Segurança associadas a eles. Verifique os requisitos para estes e avalie-os no seu ambiente por conta própria. Algumas delas podem estar relacionadas com processos e não técnicas.

    ![separador de conformidade](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Para gerar e descarregar um relatório PDF que ressoe o seu estado de conformidade atual para uma determinada norma, clique em **Baixar o relatório**.

    O relatório fornece um resumo de alto nível do seu estado de conformidade para a norma selecionada com base nos dados de avaliação do Centro de Segurança, e é organizado de acordo com os controlos dessa norma específica. O relatório pode ser partilhado com as partes interessadas relevantes e pode servir para apresentar provas aos auditores internos e externos.

    ![transferir](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Melhore a sua postura de conformidade

Tendo em conta as informações no painel de conformidade regulamentar, pode melhorar a sua postura de conformidade, resolvendo recomendações diretamente dentro do painel de instrumentos.

1.  Clique em qualquer uma das avaliações falhadas que aparecem no painel de instrumentos para ver os detalhes dessa recomendação. Cada recomendação inclui um conjunto de medidas de reparação que devem ser seguidas para resolver a questão.

1.  Pode selecionar um recurso específico para visualizar mais detalhes e resolver a recomendação para esse recurso. <br>Por exemplo, no separador **padrão Azure CIS,** pode clicar na recomendação **Exigir transferência segura para a conta de armazenamento**.

    ![Selecionando a recomendação **Exija transferência segura para a conta de armazenamento**](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. Ao clicar nas informações de recomendação e selecionar um recurso pouco saudável, ele leva-o diretamente à experiência de permitir uma **transferência segura** de armazenamento dentro do portal Azure.

    Para obter mais informações sobre como aplicar recomendações, consulte [as recomendações de segurança de implementação no Centro de Segurança Azure.](security-center-recommendations.md)

    ![Configuração da conta de armazenamento](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  Depois de tomar medidas para resolver recomendações, verá o impacto no relatório do painel de conformidade porque a sua pontuação de conformidade melhora.

    > [!NOTE]
    > As avaliações são executadas aproximadamente a cada 12 horas, portanto, verá o impacto nos dados de conformidade apenas depois da execução das avaliações.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o painel de conformidade regulamentar do Centro de Segurança para:

-   Veja e monitorize a sua postura de conformidade, em relação aos padrões e regulamentos que são importantes para si.

-   Melhore o seu estado de conformidade, resolvendo recomendações relevantes e vendo a pontuação de conformidade melhorar.

O painel de conformidade regulamentar pode simplificar consideravelmente o processo de conformidade e reduzir significativamente o tempo necessário para recolher provas de conformidade para o seu ambiente Azure e híbrido.

Para saber mais veja:

-   [Atualização para pacotes de conformidade dinâmicos no seu painel de conformidade regulamentar (Preview)](update-regulatory-compliance-packages.md) - Saiba mais sobre esta funcionalidade de pré-visualização que lhe permite atualizar as normas apresentadas no seu painel de conformidade regulamentar para os novos pacotes *dinâmicos.* Também pode adicionar novos pacotes de conformidade e monitorizar o cumprimento de normas adicionais. 

-   [Segurança monitorização de saúde no Azure Security Center](security-center-monitoring.md) - Saiba como monitorizar a saúde dos seus recursos Azure.

-   [Gerir recomendações de segurança no Azure Security Center](security-center-recommendations.md) - Saiba como usar recomendações no Azure Security Center para ajudar a proteger os seus recursos Azure.

-   [Melhore a sua Pontuação Segura no Centro de Segurança Azure](secure-score-security-controls.md) - Aprenda a priorizar vulnerabilidades e recomendações de segurança para melhorar a sua postura de segurança.