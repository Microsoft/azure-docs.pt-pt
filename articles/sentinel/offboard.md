---
title: Remover Azure Sentinel. Microsoft Docs
description: Como apagar a sua instância Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77581689"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Remova Azure Sentinel do seu espaço de trabalho

Se já não quiser utilizar o Azure Sentinel, este artigo explica como removê-lo do seu espaço de trabalho.

## <a name="how-to-remove-azure-sentinel"></a>Como remover Azure Sentinel

Acompanhe este processo para remover o Azure Sentinel do seu espaço de trabalho:

1. Vá ao **Azure Sentinel**, seguido de **Definições,** e selecione o separador **Remove Azure Sentinel**.

1. Antes de remover o Azure Sentinel, por favor, use as caixas de verificação para nos informar por que está a removê-lo.

1. **Selecione Remover Azure Sentinel do seu espaço de trabalho**.
    
    ![Eliminar a solução SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>O que acontece nos bastidores?

Quando remove a solução, o Azure Sentinel demora até 48 horas a completar a primeira fase do processo de eliminação.

Após a desconexão ser identificada, o processo de embarque começa.

**A configuração destes conectores é removida:**
-   Office 365

-   AWS

-   Alertas de segurança dos serviços da Microsoft (Azure ATP, Microsoft Cloud App Security, incluindo cloud discovery shadow it reportando, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Informações sobre Ameaças

-   Registos de segurança comuns (incluindo registos baseados em CEF, Barracuda e Syslog) (Se tiver o Azure Security Center, estes registos continuarão a ser recolhidos.)

-   Eventos de Segurança do Windows (Se tiver o Azure Security Center, estes registos continuarão a ser recolhidos.)

Nas primeiras 48 horas, os dados e as regras analíticas (incluindo a configuração da automatização em tempo real) deixarão de ser acessíveis ou consultais no Azure Sentinel.

**Após 30 dias estes recursos são removidos:**

-   Incidentes (incluindo metadados de investigação)

-   Regras analíticas

-   Marcadores

Os seus livros de jogadas, livros guardados, consultas de caça guardadas e cadernos não são removidos. **Alguns podem quebrar devido aos dados removidos. Pode removê-las manualmente.**

Após a remoção do serviço, existe um período de carência de 30 dias durante o qual pode voltar a ativar a solução e os seus dados e regras analíticas serão restaurados, mas os conectores configurados que foram desligados devem ser reconectados.

> [!NOTE]
> Se remover a solução, a sua subscrição continuará registada no fornecedor de recursos Azure Sentinel. **Pode removê-lo manualmente.**




## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a remover o serviço Azure Sentinel. Se mudar de ideias e quiser instalá-la novamente:
- Começa a [embarcar no Azure Sentinel.](quickstart-onboard.md)
