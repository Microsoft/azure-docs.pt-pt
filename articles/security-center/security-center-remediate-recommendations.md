---
title: Recomendações remediar no Centro de Segurança Azure | Microsoft Docs
description: Este artigo explica como responder às recomendações do Azure Security Center para proteger os seus recursos e satisfazer as políticas de segurança.
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
ms.openlocfilehash: 6222491d35317e549a3d21f29395b9c25813ea9f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100360"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Recomendações de remediação no Centro de Segurança do Azure

As recomendações dão-lhe sugestões sobre como garantir melhor os seus recursos. Implementa uma recomendação seguindo as medidas de reparação previstas na recomendação.

## <a name="remediation-steps"></a>Medidas de reparação <a name="remediation-steps"></a>

Depois de rever todas as recomendações, decida qual delas deve remediar primeiro. Recomendamos que dê prioridade aos controlos de segurança com maior potencial para aumentar a sua pontuação segura.

1. Na lista, selecione uma recomendação.

1. Siga as instruções na secção **de passos de reparação.** Cada recomendação tem o seu próprio conjunto de instruções. A imagem que se segue mostra medidas de reparação para configurar aplicações para permitir apenas o tráfego em HTTPS.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="Medidas de reparação manual para uma recomendação" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. Uma vez concluída, aparece uma notificação informando-o se o problema está resolvido.

## <a name="quick-fix-remediation"></a>Reparação rápida

Para simplificar a remediação e melhorar a segurança do seu ambiente (e aumentar a sua pontuação segura), muitas recomendações incluem uma opção de correção rápida.

A correção rápida ajuda-o a remediar rapidamente uma recomendação sobre múltiplos recursos.

> [!TIP]
> As soluções de correção rápida só estão disponíveis para recomendações específicas. Para encontrar as recomendações que têm uma correção rápida disponível, utilize o filtro **de ações de resposta** para a lista de recomendações:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="Use os filtros acima da lista de recomendações para encontrar recomendações que tenham a opção de correção rápida":::

Para implementar uma solução de correção rápida:

1. Da lista de recomendações que têm a **Quick Fix!** etiqueta, selecione uma recomendação.

    [![Selecione Quick Fix!](media/security-center-remediate-recommendations/security-center-quick-fix-select.png)](media/security-center-remediate-recommendations/security-center-quick-fix-select.png#lightbox)

1. A partir do separador **recursos insalubres,** selecione os recursos em que pretende implementar a recomendação e selecione **Remediate**.

    > [!NOTE]
    > Alguns dos recursos listados podem ser desativados, porque não tem as permissões apropriadas para os modificar.

1. Na caixa de confirmação, leia os detalhes e implicações da reparação.

    ![Correção rápida](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > As implicações estão listadas na caixa cinzenta na janela **de recursos Remediate** que se abre após clicar em **Remediate**. Eles listam o que acontece quando prosseguem com a rápida correção.

1. Insira os parâmetros relevantes se necessário e aprove a reparação.

    > [!NOTE]
    > Pode levar vários minutos após a reparação concluir para ver os recursos no separador **recursos saudáveis.** Para visualizar as ações de reparação, verifique o registo de [atividades](#activity-log).

1. Uma vez concluída, aparece uma notificação informando-o se a reparação foi bem sucedida.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Correção rápida de remediação no registo de atividade <a name="activity-log"></a>

A operação de remediação utiliza uma implementação de modelo ou chamada API REST PATCH para aplicar a configuração no recurso. Estas operações estão registadas no [registo de atividades do Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Passos seguintes

Neste documento, foi-lhe mostrado como remediar recomendações no Centro de Segurança. Para saber mais sobre o Centro de Segurança, consulte as seguintes páginas:

* [Definir políticas de segurança no Azure Security Center](tutorial-security-policy.md) - Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos Azure
* [O que são políticas de segurança, iniciativas e recomendações?](security-policy-concept.md)