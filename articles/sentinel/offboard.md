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
ms.date: 09/26/2019
ms.author: rkarlin
ms.openlocfilehash: d3b9284282a7ee14cde2461598c81e6dfdfd9f72
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316749"
---
# <a name="remove-azure-sentinel-from-your-tenant"></a>Remover o Azure Sentinel do seu locatário

Se você não quiser mais usar o Azure Sentinel, este artigo explica como removê-lo do seu locatário.

## <a name="how-to-delete-azure-sentinel"></a>Como excluir o Azure Sentinel

Em segundo plano, quando você instala o Azure Sentinel, a solução **SecurityInsights** é instalada em seu espaço de trabalho selecionado. Portanto, a primeira coisa que você precisa fazer é remover a solução **SecurityInsights** .

1.  Vá para **Azure Sentinel**, seguido por **configuração**, seguido por **configurações de espaço de trabalho**e, em seguida, **soluções**.

2.  Selecione `SecurityInsights` e clique nele.

    ![Localizar a solução SecurityInsights](media/offboard/find-solution.png)

3.  Na parte superior da página, selecione **excluir**.

    > [!IMPORTANT]
    > Se você excluir o espaço de trabalho, o espaço de trabalho e o Azure Sentinel serão removidos do seu locatário no Azure Monitor.

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

-   Marcadores

Seus guias estratégicos, pastas de trabalho salvas, consultas de busca salvas e notebooks não são excluídos. **Alguns podem ser interrompidos devido aos dados removidos. Você pode removê-los manualmente.**

Depois de remover o serviço, há um período de carência de 30 dias durante o qual você pode reabilitar a solução e seus dados e regras de alerta serão restaurados, mas os conectores configurados que foram desconectados devem ser reconectados.

> [!NOTE]
> Se você remover a solução, sua assinatura continuará a ser registrada com o provedor de recursos do Azure Sentinel. **Você pode removê-lo manualmente.**




## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a remover o serviço do Azure Sentinel. Se você mudar de ideia e quiser instalá-la novamente:
- Comece [a usar a integração do Azure Sentinel](quickstart-onboard.md).

