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
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992238"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alertas de segurança para ambientes em Laboratórios Azure DevTest
Este artigo mostra-lhe como ver alertas de segurança para ambientes em Azure DevTest Labs. 

## <a name="prerequisites"></a>Pré-requisitos
Atualmente, você pode ver alertas de segurança apenas para ambientes implantados no seu laboratório. Para testar ou utilizar esta funcionalidade, coloque um ambiente no seu laboratório. 

## <a name="view-security-alerts-for-an-environment"></a>Ver alertas de segurança para um ambiente

1. Na página inicial do seu laboratório, selecione **alertas** de segurança no menu esquerdo. Deve ver o número de alertas de segurança (alto, médio e baixo).

    ![Alertas de segurança - visão geral](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Clique à direita em três pontos (...) na última coluna e selecione **Ver alertas**de segurança . 

    ![Ver alertas de segurança](./media/environment-security-alerts/view-security-alerts-menu.png)
3. Veja mais detalhes sobre os alertas e recomendações de assessor. 

    ![Ver alertas de segurança](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre ambientes, consulte os seguintes artigos:

- [Crie ambientes multi-vm e recursos PaaS com modelos de Gestor de Recursos Azure](devtest-lab-create-environment-from-arm.md)
- [Configure e use ambientes públicos](devtest-lab-configure-use-public-environments.md)
