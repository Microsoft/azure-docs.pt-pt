---
title: Alertas de segurança para ambientes em Laboratórios Azure DevTest
description: Este artigo mostra-lhe como ver alertas de segurança para um ambiente em DevTest Labs e tomar uma ação apropriada.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588710"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alertas de segurança para ambientes em Laboratórios Azure DevTest
Como utilizador de laboratório, agora pode ver alertas do Azure Security Center para os seus ambientes de laboratório. O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e soluções de parceiros ligadas, tal como soluções de proteção de ponto final e firewall, para detetar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioritários é apresentada no Centro de Segurança juntamente com as informações necessárias para investigar rapidamente o problema e fornecer recomendações sobre como remediar um ataque. [Saiba mais sobre alertas de segurança no Centro de Segurança Azure.](../security-center//security-center-alerts-overview.md)  


## <a name="prerequisites"></a>Pré-requisitos
Atualmente, você pode ver alertas de segurança apenas para a plataforma como um serviço (PaaS) ambientes implantados no seu laboratório. Para testar ou utilizar esta funcionalidade, [coloque um ambiente no seu laboratório.](devtest-lab-create-environment-from-arm.md) 

## <a name="view-security-alerts-for-an-environment"></a>Ver alertas de segurança para um ambiente

1. Na página inicial do seu laboratório, selecione **alertas** de segurança no menu esquerdo. Deve ver o número de alertas de segurança (alto, médio e baixo). Saiba mais sobre [como os alertas são classificados.](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)

    ![Alertas de segurança - visão geral](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Clique à direita em três pontos (...) na última coluna e selecione **Ver alertas**de segurança . 

    ![Ver alertas de segurança](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Veja mais detalhes sobre os alertas e recomendações de assessor. Saiba mais sobre [gestão e resposta a alertas](../security-center/security-center-managing-and-responding-alerts.md)de segurança no Centro de Segurança Azure.

    ![Ver alertas de segurança](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre ambientes, consulte os seguintes artigos:

- [Crie ambientes multi-vm e recursos PaaS com modelos de Gestor de Recursos Azure](devtest-lab-create-environment-from-arm.md)
- [Configure e use ambientes públicos](devtest-lab-configure-use-public-environments.md)
