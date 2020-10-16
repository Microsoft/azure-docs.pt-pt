---
title: Remediar recomendações no Centro de Segurança Azure Microsoft Docs
description: Este artigo explica como remediar recomendações no Azure Security Center para proteger os seus recursos e cumprir as políticas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 4bad3227e08c0fbe0d280967e45bbef9d477e1b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569140"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Recomendações de remediação no Centro de Segurança do Azure

As recomendações dão-lhe sugestões sobre como garantir melhor os seus recursos. Implementa uma recomendação seguindo as medidas de reparação previstas na recomendação.

## <a name="remediation-steps"></a>Medidas de reparação <a name="remediation-steps"></a>

Depois de rever todas as recomendações, decida qual delas deve remediar primeiro. Recomendamos que utilize o [impacto Secure Score](security-center-recommendations.md#monitor-recommendations) para ajudar a priorizar o que fazer primeiro.

1. Na lista, clique na recomendação.

1. Siga as instruções na secção **de passos de reparação.** Cada recomendação tem o seu próprio conjunto de instruções. A imagem que se segue mostra medidas de reparação para configurar aplicações para permitir apenas o tráfego em HTTPS.

    ![Detalhes da recomendação](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Uma vez concluída, aparece uma notificação informando-o se a reparação foi bem sucedida.

## <a name="quick-fix-remediation"></a>Reparação rápida<a name="one-click"></a>

O Quick Fix permite-lhe remediar rapidamente uma recomendação sobre múltiplos recursos. Só está disponível para recomendações específicas. O Quick Fix simplifica a remediação e permite-lhe aumentar rapidamente a sua Pontuação Segura, melhorando a segurança do seu ambiente.

Para implementar a reparação da Quick Fix:

1. Da lista de recomendações que têm a **Quick Fix!** etiqueta, clique na recomendação.

    [![Selecione Quick Fix!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. A partir do separador **recursos insalubres,** selecione os recursos em que pretende implementar a recomendação e clique em **Remediate**.

    > [!NOTE]
    > Alguns dos recursos listados podem ser desativados, porque não tem as permissões apropriadas para os modificar.

1. Na caixa de confirmação, leia os detalhes e implicações da reparação.

    ![Correção Rápida](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > As implicações estão listadas na caixa cinzenta na janela **de recursos Remediate** que se abre após clicar em **Remediate**. Eles listam as mudanças que acontecem quando prosseguem com a reparação da Quick Fix.

1. Insira os parâmetros relevantes se necessário e aprove a reparação.

    > [!NOTE]
    > Pode levar vários minutos após a reparação concluir para ver os recursos no separador **recursos saudáveis.** Para visualizar as ações de reparação, verifique o registo de [atividades](#activity-log).

1. Uma vez concluída, aparece uma notificação informando-o se a reparação foi bem sucedida.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Reparação rápida registação no registo de atividade <a name="activity-log"></a>

A operação de remediação utiliza uma implementação de modelo ou chamada API REST PATCH para aplicar a configuração no recurso. Estas operações estão registadas no [registo de atividades do Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Passos seguintes

Neste documento, foi-lhe mostrado como remediar recomendações no Centro de Segurança. Para saber mais sobre o Centro de Segurança, consulte os seguintes tópicos:

* [Definir políticas de segurança no Azure Security Center](tutorial-security-policy.md) - Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos Azure.
* [Segurança monitorização de saúde no Azure Security Center](security-center-monitoring.md) - Saiba como monitorizar a saúde dos seus recursos Azure.
