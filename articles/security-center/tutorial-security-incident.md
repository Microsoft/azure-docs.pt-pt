---
title: Tutorial de resposta de alerta - Centro de Segurança Azure
description: Neste tutorial, você vai aprender a triagem de alertas de segurança e determinar a causa principal & âmbito de um alerta.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2020
ms.author: memildin
ms.openlocfilehash: 02b0ee4d572290436cc45bab73921ae1298bc72f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359004"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Tutorial: Triagem, investigar e responder a alertas de segurança
O Security Center analisa continuamente as suas cargas de trabalho em nuvem híbrida usando análises avançadas e inteligência de ameaça para alertá-lo sobre atividades potencialmente maliciosas nos seus recursos na nuvem. Também pode integrar alertas de outros produtos e serviços de segurança no Centro de Segurança. Uma vez levantado um alerta, são necessárias medidas rápidas para investigar e remediar a questão potencial de segurança. 

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Triar alertas de segurança
> * Investigue um alerta de segurança para determinar a causa principal
> * Responda a um alerta de segurança e atenue essa causa raiz

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para analisar as funcionalidades abrangidas por este tutorial, tem de ter o Azure Defender habilitado. Pode tentar a Azure Defender sem custos. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). O quickstart [Começar com o Security Center](security-center-get-started.md) acompanha-o como fazer o upgrade.


## <a name="triage-security-alerts"></a>Triar alertas de segurança
O Centro de Segurança proporciona uma vista unificada de todos os alertas de segurança. Os alertas de segurança são classificados com base na gravidade da atividade detetada. 

Triagem dos seus alertas a partir da página **de alertas** de segurança:

:::image type="content" source="./media/tutorial-security-incident/alerts-list.png" alt-text="Página da lista de alertas de segurança" lightbox="./media/tutorial-security-incident/alerts-list.png":::

Use esta página para rever os alertas de segurança ativos no seu ambiente para decidir qual o alerta para investigar primeiro.

Ao triagem de alertas de segurança, priorize alertas baseados na gravidade do alerta, abordando primeiro alertas com maior gravidade. Saiba mais sobre a gravidade dos alertas em [Como são classificados os alertas?](security-center-alerts-overview.md#how-are-alerts-classified)

> [!TIP]
> Pode ligar o Azure Security Center às soluções SIEM mais populares, incluindo o Azure Sentinel e consumir os alertas da sua ferramenta de eleição. Saiba mais em [Alertas de Exportação para um SIEM.](continuous-export.md)


## <a name="investigate-a-security-alert"></a>Investigue um alerta de segurança

Quando decidir qual o alerta para investigar primeiro:

1. Selecione o alerta pretendido.
1. A partir da página geral do alerta, selecione o recurso para investigar primeiro.
1. Inicie a sua investigação a partir do painel esquerdo, que mostra a informação de alto nível sobre o alerta de segurança.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="Página da lista de alertas de segurança":::

    Este painel mostra:
    - Gravidade, estado de alerta e tempo de atividade
    - Descrição que explica a atividade precisa que foi detetada
    - Recursos afetados
    - Kill chain intenção da atividade na matriz MITRE ATT&CK

1. Para obter informações mais detalhadas que o ajudem a investigar a atividade suspeita, examine o separador **Detalhes do Alerta.**

1. Quando tiver revisto a informação nesta página, poderá ter o suficiente para proceder a uma resposta. Se precisar de mais detalhes:

    - Contacte o titular do recurso para verificar se a atividade detetada é falsamente positiva.
    - Investigue os troncos crus gerados pelo recurso atacado

## <a name="respond-to-a-security-alert"></a>Responda a um alerta de segurança
Depois de investigar um alerta e compreender o seu âmbito de aplicação, pode responder ao alerta de segurança do Centro de Segurança Azure:

1.  Abra o **separador de ação Take** para ver as respostas recomendadas.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Página da lista de alertas de segurança" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  Reveja a secção **de ameaças para** as etapas de investigação manuais necessárias para atenuar a questão.
1.  Para endurecer os seus recursos e prevenir futuros ataques deste tipo, remediar as recomendações de segurança na secção **Prevenir futuros ataques.**
1.  Para ativar uma aplicação lógica com etapas de resposta automatizadas, utilize a secção **de resposta automatizada Trigger.**
1.  Se a atividade detetada *não for* maliciosa, pode suprimir futuros alertas deste tipo utilizando a secção de **alertas semelhantes de Supressão.**

1.  Quando terminar a investigação sobre o alerta e responder da forma apropriada, altere o estatuto para **Dispensado.**

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="Página da lista de alertas de segurança":::

    Isto remove o alerta da lista principal de alertas. Pode utilizar o filtro na página da lista de alertas para visualizar todos os alertas com o estado **de Despedimento.**

1.  Encorajamo-lo a fornecer feedback sobre o alerta à Microsoft:
    1. Marcar o alerta como **Útil** ou **Não útil.**
    1. Selecione uma razão e adicione um comentário.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Página da lista de alertas de segurança":::

    > [!TIP]
    > Revemos o seu feedback para melhorar os nossos algoritmos e fornecer melhores alertas de segurança.

## <a name="end-the-tutorial"></a>Termine o tutorial

Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se pretender continuar a trabalhar com os rápidos e tutoriais subsequentes, mantenha o provisionamento automático e o Azure Defender ativado. 

Se não pretende continuar, ou se pretende desativar qualquer uma destas funcionalidades:

1. Volte ao menu principal do Centro de Segurança e **selecione preços e configurações**.
1. Selecione a subscrição relevante.
1. Para reduzir, selecione **Azure Defender off**.
1. Para desativar o fornecimento automático, abra a página **de Recolha de Dados** e desemalte **o provisionamento automático** para **desligar**.
4. Selecione **Guardar**.

>[!NOTE]
> A desativação do fornecimento automático não remove o agente Log Analytics dos VMs Azure que já têm o agente. Desativar o aprovisionamento automático limita a monitorização da segurança dos seus recursos.
>

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você aprendeu sobre as funcionalidades do Centro de Segurança a serem usadas ao responder a um alerta de segurança. Para materiais relacionados ver:

- [Responder a alertas do Azure Defender para o Key Vault](defender-for-key-vault-usage.md)
- [Alertas de segurança – um guia de referência](alerts-reference.md)
- [Introdução ao Azure Defender](azure-defender.md)
