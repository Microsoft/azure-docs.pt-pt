---
title: Upgrade para Azure Defender - Azure Security Center
description: Este quickstart mostra-lhe como fazer upgrade para o Azure Defender do Security Center para obter segurança adicional.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: bd8c5c31579ac22cd6c77dffd31103680e393f69
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187175"
---
# <a name="quickstart-setting-up-azure-security-center"></a>Quickstart: Criação do Centro de Segurança Azure

O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção contra ameaças entre cargas de trabalho na cloud híbrida. Embora as funcionalidades gratuitas ofereçam segurança limitada apenas para os seus recursos Azure, permitindo que o Azure Defender aumente essas capacidades para as instalações e outras nuvens. O Azure Defender ajuda-o a encontrar e corrigir vulnerabilidades de segurança, a aplicar controlos de acesso e aplicação para bloquear atividades maliciosas, detetar ameaças usando análises e inteligência e responder rapidamente quando está sob ataque. Pode tentar a Azure Defender sem custos. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Neste artigo, você atualiza para a Azure Defender para obter segurança adicional e instale o agente Log Analytics nas suas máquinas para monitorizar vulnerabilidades e ameaças de segurança.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Para ativar o Azure Defender numa subscrição, deve ser-lhe atribuída a função de Proprietário de Assinatura, Colaborador de Assinatura ou Administrador de Segurança.


## <a name="enable-security-center-on-your-azure-subscription"></a>Ativar o Centro de Segurança na sua assinatura Azure

1. Inicie sessão no [portal do Azure](https://azure.microsoft.com/features/azure-portal/).

1. A partir do menu do portal, selecione **Security Center**. 

    A página geral do Centro de Segurança abre.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Painel geral do Centro de Segurança" lightbox="./media/security-center-get-started/overview.png":::

**Centro de Segurança – Descrição Geral** oferece uma vista unificada da postura de segurança das suas cargas de trabalho da cloud híbrida, permitindo-lhe detetar e avaliar a segurança das cargas de trabalho e identificar e mitigar o risco. O Security Center, sem custos adicionais, permite qualquer uma das suas subscrições Azure não previamente a bordo por si ou por outro utilizador de subscrição.

Pode visualizar e filtrar a lista de subscrições selecionando o item do menu **de Assinaturas.** O Centro de Segurança ajustará o ecrã para refletir a postura de segurança das subscrições selecionadas. 

Poucos minutos depois de lançar o Security Center pela primeira vez, pode ver:

- **Recomendações** para formas de melhorar a segurança dos seus recursos conectados.
- Um inventário dos seus recursos que estão agora a ser avaliados pelo Security Center, juntamente com a postura de segurança de cada um.

Para tirar o máximo partido do Security Center, é necessário completar os passos abaixo para ativar o Azure Defender e instalar o agente Log Analytics.

> [!TIP]
> Para ativar o Centro de Segurança em todas as subscrições dentro de um grupo de gestão, consulte [o Enable Security Center em várias subscrições do Azure](onboard-management-group.md).

## <a name="enable-azure-defender"></a>Ativar o Azure Defender

Para efeitos do Início rápido e tutoriais do Centro de Segurança, deve ativar o Azure Defender. Está disponível um julgamento gratuito de 30 dias. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). 

1. A partir da barra lateral do Security Center, **selecione Começar a trabalhar**.

    :::image type="content" source="./media/security-center-get-started/get-started-upgrade-tab.png" alt-text="Atualizar o separador da página de começar"::: 

    O separador **Upgrade** lista subscrições e espaços de trabalho elegíveis para embarque.

1. A partir dos **espaços de trabalho Select para ativar o Azure Defender na** lista, selecione os espaços de trabalho para atualizar.
   - Se selecionar subscrições e espaços de trabalho que não são elegíveis para julgamento, o próximo passo irá atualizá-los e os encargos começarão.
   - Se escolher um espaço de trabalho elegível para um teste gratuito, o próximo passo começará um julgamento.
1. Selecione **Upgrade** para ativar o Azure Defender.

## <a name="enable-automatic-data-collection"></a>Permitir a recolha automática de dados
O Security Center recolhe dados das suas máquinas para monitorizar vulnerabilidades e ameaças de segurança. Os dados são recolhidos utilizando o agente Log Analytics, que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho para análise. Por predefinição, o Centro de Segurança irá criar uma nova área de trabalho para si.

Quando o provisionamento automático está ativado, o Security Center instala o agente Log Analytics em todas as máquinas suportadas e quaisquer novas que sejam criadas. O aprovisionamento automático é vivamente recomendado.

Para permitir o provisionamento automático do agente Log Analytics:

1. A partir do menu do Centro de Segurança, **selecione definições de preços &**.
1. Selecione a subscrição relevante.
1. Na página de **recolha de dados,** desa **essa adir** automático ao **On**.
1. Selecione **Guardar**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Habilitação automática do agente Log Analytics":::

>[!TIP]
> Se um espaço de trabalho precisar de ser a provisionado, a instalação do agente pode demorar até 25 minutos.

Com o agente implantado nas suas máquinas, o Security Center pode fornecer recomendações adicionais relacionadas com o estado de atualização do sistema, configurações de segurança do SISTEMA, proteção de ponto final, bem como gerar alertas de segurança adicionais.

>[!NOTE]
> A definição de provisão automática para **Off** não remove o agente Log Analytics da Azure VMs onde o agente já foi a provisionado. Desativar o aprovisionamento automático limita a monitorização da segurança dos seus recursos.



## <a name="next-steps"></a>Passos seguintes
Neste quickstart, permitiu que o Azure Defender e fornecesse o agente Log Analytics para uma gestão unificada de segurança e proteção contra ameaças através das suas cargas de trabalho em nuvem híbrida. Para obter mais informações sobre como utilizar o Centro de Segurança, avance para o início rápido para inclusão de computadores Windows que estão no local e noutras clouds.

> [!div class="nextstepaction"]
> [Quickstart: Máquinas não-Azure a bordo](quickstart-onboard-machines.md)

Quer otimizar e economizar nos gastos na nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar custos com a Gestão de Custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png