---
title: Alertas de segurança para ambientes em Azure DevTest Labs
description: Este artigo mostra-lhe como visualizar alertas de segurança para um ambiente em DevTest Labs e tomar as medidas apropriadas.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c9f7cf9fe7ab0e3f573470228ee1962aa92ccaef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91308695"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alertas de segurança para ambientes em Azure DevTest Labs
Como utilizador de laboratório, pode agora ver alertas do Centro de Segurança Azure para os seus ambientes de laboratório. O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e soluções de parceiros ligadas, tal como soluções de proteção de ponto final e firewall, para detetar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioritários é apresentada no Centro de Segurança juntamente com as informações necessárias para investigar rapidamente o problema e fornecer recomendações sobre como remediar um ataque. [Saiba mais sobre alertas de segurança no Azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Pré-requisitos
Atualmente, você pode ver alertas de segurança apenas para a plataforma como um ambiente de serviço (PaaS) implantado no seu laboratório. Para testar ou utilizar esta função, [coloque um ambiente no seu laboratório](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Ver alertas de segurança para um ambiente

1. Na página inicial do seu laboratório, selecione **alertas de segurança** no menu esquerdo. Deve ver o número de alertas de segurança (altos, médios e baixos). Saiba mais sobre [como os alertas são classificados.](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)

    ![Alertas de segurança - visão geral](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Clique com o botão direito em três pontos (...) na última coluna e selecione **Ver alertas de segurança**. 

    ![Screenshot que mostra a página de alertas de segurança com "Ver alertas de segurança" selecionados.](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Veja mais detalhes sobre os alertas e recomendações de conselheiros. Saiba mais sobre [como gerir e responder aos alertas de segurança no Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Ver alertas de segurança](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre ambientes, consulte os seguintes artigos:

- [Criar ambientes multi-vm e recursos PaaS com modelos de Gestor de Recursos Azure](devtest-lab-create-environment-from-arm.md)
- [Configurar e utilizar ambientes públicos](devtest-lab-configure-use-public-environments.md)
