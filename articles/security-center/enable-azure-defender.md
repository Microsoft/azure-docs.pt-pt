---
title: Permitir as proteções integradas de carga de trabalho do Azure Security Center
description: Saiba como permitir que o Azure Defender estenda as proteções do Azure Security Center aos seus recursos híbridos e multicloud
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/24/2021
ms.openlocfilehash: 6496b18c8c22cbbd4fd3344736a4b38b7a998890
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102108920"
---
# <a name="quickstart-enable-azure-defender"></a>Quickstart: Enable Azure Defender

Para conhecer os benefícios do Azure Defender, consulte o [Security Center free vs Azure Defender ativado](security-center-pricing.md).

## <a name="prerequisites"></a>Pré-requisitos

Para efeitos do Início rápido e tutoriais do Centro de Segurança, deve ativar o Azure Defender. 

Pode proteger toda uma subscrição do Azure com o Azure Defender e as proteções serão herdadas por todos os recursos dentro da subscrição.

Está disponível um julgamento gratuito de 30 dias. Para obter detalhes sobre preços na sua moeda de eleição e de acordo com a sua região, consulte [os preços do Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender-from-the-azure-portal"></a>Ativar o Azure Defender a partir do portal Azure

Para ativar todas as funcionalidades do Centro de Segurança, incluindo capacidades de proteção contra ameaças, deve ativar o Azure Defender na subscrição que contenha as cargas de trabalho aplicáveis. Permitir isso ao nível do espaço de trabalho não permite o acesso de VM a tempo, controlos de aplicações adaptativos e deteções de rede para recursos Azure. Além disso, os únicos planos do Azure Defender disponíveis ao nível do espaço de trabalho são o Azure Defender para servidores e o Azure Defender para servidores SQL em máquinas.

- Pode ativar **contas do Azure Defender para armazenamento** ao nível da subscrição ou ao nível de recursos
- Pode ativar **o Azure Defender para o SQL** ao nível de subscrição ou ao nível de recursos
- Pode ativar a proteção contra ameaças para **a Base de Dados Azure para MariaDB/ MySQL/ PostgreSQL apenas** ao nível dos recursos

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>Para ativar o Azure Defender nas suas subscrições e espaços de trabalho:

- Para ativar o Azure Defender numa única subscrição:

    1. A partir do menu principal do Security Center, **selecione definições de preços &**.
    1. Selecione a subscrição ou espaço de trabalho que pretende proteger.
    1. Selecione **Azure Defender** para atualizar.
    1. Selecione **Guardar**.

    > [!TIP]
    > Você vai notar que cada plano em Azure Defender tem um preço separado e pode ser definido individualmente para dentro ou fora. Por exemplo, é melhor desativar o Azure Defender para o Serviço de Aplicações em subscrições que não possuam um plano de Serviço de Aplicações Azure associado. 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Página de preços do Centro de Segurança no portal":::

- Para ativar o Azure Defender em várias subscrições ou espaços de trabalho:

    1. A partir da barra lateral do Security Center, **selecione Começar a trabalhar**.

        O separador **Upgrade** lista subscrições e espaços de trabalho elegíveis para embarque.

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="Atualizar o separador da página de começar"::: 

    1. A partir das **subscrições e espaços de trabalho Select para permitir o Azure Defender na** lista, selecione as subscrições e espaços de trabalho para atualizar e selecione **Upgrade** para ativar o Azure Defender.

       - Se selecionar subscrições e espaços de trabalho que não são elegíveis para julgamento, o próximo passo irá atualizá-los e os encargos começarão.
       - Se escolher um espaço de trabalho elegível para um teste gratuito, o próximo passo começará um julgamento.

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Atualizar todos os espaços de trabalho selecionados e subscrições a partir da página de arranque":::


## <a name="next-steps"></a>Passos seguintes

Agora que ativou o Azure Defender, permita a recolha automática de dados pelos agentes e extensões necessários descritos em [agentes de provisionamento automático e extensões do Azure Security Center](security-center-enable-data-collection.md).