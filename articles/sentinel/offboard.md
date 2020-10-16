---
title: Remover Azure Sentinel Microsoft Docs
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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: f9c400b55b0da47495db4f1ff4ceb86aa39fe2cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90885835"
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

-   Alertas de segurança dos serviços da Microsoft: Microsoft Defender for Identity *(anteriormente Azure ATP),* Microsoft Cloud App Security incluindo cloud Discovery Shadow IT reportando, Azure AD Identity Protection, Microsoft Defender for Endpoint *(anteriormente Microsoft Defender ATP),* alertas do Azure Security Center do Azure Security Center

-   Informações sobre Ameaças

-   Registos de segurança comuns (incluindo registos baseados em CEF, Barracuda e Syslog) (Se receber alertas do Azure Defender do Azure Security Center, estes registos continuarão a ser recolhidos.)

-   Eventos de Segurança do Windows (Se receber alertas do Azure Defender do Azure Security Center, estes registos continuarão a ser recolhidos.)

Nas primeiras 48 horas, os dados e as regras analíticas (incluindo a configuração da automatização em tempo real) deixarão de ser acessíveis ou consultais no Azure Sentinel.

**Após 30 dias estes recursos são removidos:**

-   Incidentes (incluindo metadados de investigação)

-   Regras analíticas

-   Marcadores

Os seus livros de jogadas, livros guardados, consultas de caça guardadas e cadernos não são removidos. **Alguns podem quebrar devido aos dados removidos. Pode removê-las manualmente.**

Após a remoção do serviço, existe um período de carência de 30 dias durante o qual pode voltar a ativar a solução e os seus dados e regras analíticas serão restaurados, mas os conectores configurados que foram desligados devem ser reconectados.

> [!NOTE]
> Se remover a solução, a sua subscrição continuará registada no fornecedor de recursos Azure Sentinel. **Pode removê-lo manualmente.**




## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a remover o serviço Azure Sentinel. Se mudar de ideias e quiser instalá-la novamente:
- Começa a [embarcar no Azure Sentinel.](quickstart-onboard.md)
