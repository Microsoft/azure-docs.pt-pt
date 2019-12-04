---
title: Transferir Azure Sentinel | Microsoft Docs
description: Como excluir sua instância do Sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: rkarlin
ms.openlocfilehash: 4c0c415235fd290bc47ac402a6b81a1afa7af903
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74777439"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Remover o Azure Sentinel do seu espaço de trabalho

Se você não quiser mais usar o Azure Sentinel, este artigo explica como removê-lo do seu espaço de trabalho.

## <a name="how-to-delete-azure-sentinel"></a>Como excluir o Azure Sentinel

Em segundo plano, quando você instala o Azure Sentinel, a solução **SecurityInsights** é instalada em seu espaço de trabalho selecionado. Portanto, a primeira coisa que você precisa fazer é remover a solução **SecurityInsights** .

1.  Vá para **Azure Sentinel**, seguido por **configuração**, seguido por **configurações de espaço de trabalho**e, em seguida, **soluções**.

2.  Selecione `SecurityInsights` e clique nele.

    ![Localizar a solução SecurityInsights](media/offboard/find-solution.png)

3.  Na parte superior da página, selecione **excluir**.

    > [!IMPORTANT]
    > Se você remover o espaço de trabalho, ele poderá afetar outras soluções e fontes de dados que estão usando esse espaço de trabalho, incluindo Azure Monitor. Para verificar quais soluções estão usando esse espaço de trabalho, consulte [listar soluções de monitoramento instaladas](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions). Para verificar quais dados de soluções estão sendo incluídos no espaço de trabalho, consulte [noções básicas sobre o volume de dados ingeridos](../azure-monitor/platform/manage-cost-storage.md#understanding-ingested-data-volume).

    ![Excluir a solução SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>O que acontece nos bastidores?

Quando você exclui a solução, o Azure Sentinel leva até 48 horas para concluir a primeira fase do processo de exclusão.

Depois que a desconexão é identificada, o processo de remoção é iniciado.

**A configuração desses conectores foi excluída:**
-   Office 365

-   AWS

-   Alertas de segurança de serviços da Microsoft (Azure ATP Microsoft Cloud App Security incluindo Cloud Discovery relatório de ti de sombra, Azure AD Identity Protection, Microsoft defender ATP, central de segurança do Azure)

-   Informações sobre Ameaças

-   Logs de segurança comuns (incluindo logs baseados em CEF, Barracuda e syslog) (se você tiver a central de segurança do Azure, esses logs continuarão a ser coletados.)

-   Eventos de segurança do Windows (se você tiver a central de segurança do Azure, esses logs continuarão a ser coletados).

Nas primeiras 48 horas, os dados e as regras de alerta (incluindo a configuração de automação em tempo real) não estarão mais acessíveis ou consultáveis no Azure Sentinel.

**Após 30 dias, esses recursos são excluídos:**

-   Incidentes (incluindo metadados de investigação)

-   Regras de alerta

-   indicadores

Seus guias estratégicos, pastas de trabalho salvas, consultas de busca salvas e notebooks não são excluídos. **Alguns podem ser interrompidos devido aos dados removidos. Você pode removê-los manualmente.**

Depois de remover o serviço, há um período de carência de 30 dias durante o qual você pode reabilitar a solução e seus dados e regras de alerta serão restaurados, mas os conectores configurados que foram desconectados devem ser reconectados.

> [!NOTE]
> Se você remover a solução, sua assinatura continuará a ser registrada com o provedor de recursos do Azure Sentinel. **Você pode removê-lo manualmente.**




## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a remover o serviço do Azure Sentinel. Se você mudar de ideia e quiser instalá-la novamente:
- Comece [a usar a integração do Azure Sentinel](quickstart-onboard.md).

