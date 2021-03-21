---
title: Upgrade para Azure Defender - Azure Security Center
description: Este quickstart mostra-lhe como fazer upgrade para o Azure Defender do Security Center para obter segurança adicional.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f37a59d5db3883754b602b2b2525e07b57206b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099357"
---
# <a name="quickstart-set-up-azure-security-center"></a>Quickstart: Criar o Centro de Segurança Azure

O Azure Security Center fornece uma gestão unificada de segurança e proteção contra ameaças através das suas cargas de trabalho híbridas e multi-cloud. Embora as funcionalidades gratuitas ofereçam segurança limitada apenas para os seus recursos Azure, permitindo que o Azure Defender aumente essas capacidades para as instalações e outras nuvens. O Azure Defender ajuda-o a encontrar e corrigir vulnerabilidades de segurança, a aplicar controlos de acesso e aplicação para bloquear atividades maliciosas, detetar ameaças usando análises e inteligência e responder rapidamente quando está sob ataque. Pode tentar a Azure Defender sem custos. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Esta secção de arranque rápido irá acompanhá-lo através de todos os passos recomendados para ativar o Azure Security Center e o Azure Defender. Quando completar todos os passos de arranque rápido, terá:

> [!div class="checklist"]
> * Centro de Segurança ativado nas suas subscrições Azure
> * Azure Defender ativado nas suas subscrições Azure
> * Configuração automática de recolha de dados
> * Notificações de email criadas para alertas de segurança
> * As suas máquinas híbridas e multi-nuvens ligadas ao Azure

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Para ativar o Azure Defender numa subscrição, deve ser-lhe atribuída a função de Proprietário de Assinatura, Colaborador de Assinatura ou Administrador de Segurança.

## <a name="enable-security-center-on-your-azure-subscription"></a>Ativar o Centro de Segurança na sua assinatura Azure

> [!TIP]
> Para ativar o Centro de Segurança em todas as subscrições dentro de um grupo de gestão, consulte [o Enable Security Center em várias subscrições do Azure](onboard-management-group.md).

1. Inicie sessão no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).

1. A partir do menu do portal, selecione **Security Center**. 

    A página geral do Centro de Segurança abre.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Painel geral do Centro de Segurança" lightbox="./media/security-center-get-started/overview.png":::

    **Centro de Segurança – Descrição Geral** oferece uma vista unificada da postura de segurança das suas cargas de trabalho da cloud híbrida, permitindo-lhe detetar e avaliar a segurança das cargas de trabalho e identificar e mitigar o risco. O Security Center, sem custos adicionais, permite qualquer uma das suas subscrições Azure não previamente a bordo por si ou por outro utilizador de subscrição.

Pode visualizar e filtrar a lista de subscrições selecionando o item do menu **de Assinaturas.** O Centro de Segurança ajustará o ecrã para refletir a postura de segurança das subscrições selecionadas. 

Poucos minutos depois de lançar o Security Center pela primeira vez, pode ver:

- **Recomendações** para formas de melhorar a segurança dos seus recursos conectados.
- Um inventário dos seus recursos que estão agora a ser avaliados pelo Security Center, juntamente com a postura de segurança de cada um.

Para tirar o máximo partido do Centro de Segurança, continue com os próximos passos da secção de arranque rápido.



## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, permitiu que o Centro de Segurança Azure. O próximo passo é permitir ao Azure Defender uma gestão unificada de segurança e proteção contra ameaças através das suas cargas de trabalho em nuvem híbrida.

> [!div class="nextstepaction"]
> [Quickstart: Enable Azure Defender](enable-azure-defender.md)