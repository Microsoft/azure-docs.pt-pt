---
title: Remova o Sentinela Azure. Microsoft Docs
description: Como eliminar a sua instância Azure Sentinel.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77581689"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Retire o Sentinela Azure do seu espaço de trabalho

Se já não quiser utilizar o Azure Sentinel, este artigo explica como removê-lo do seu espaço de trabalho.

## <a name="how-to-remove-azure-sentinel"></a>Como remover o Sentinela Azure

Siga este processo para remover o Azure Sentinel do seu espaço de trabalho:

1. Vá ao **Azure Sentinel**, seguido de **Definições,** e selecione o **separador Remover O Sentinela Azure**.

1. Antes de remover o Azure Sentinel, por favor use as caixas de verificação para nos informar por que está a removê-lo.

1. Selecione **Remover o Sentinela Azure do seu espaço**de trabalho .
    
    ![Eliminar a solução SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>O que acontece nos bastidores?

Quando remove a solução, o Azure Sentinel demora até 48 horas a completar a primeira fase do processo de eliminação.

Após a desconexão, inicia-se o processo de offboarding.

**A configuração destes conectores é removida:**
-   Office 365

-   AWS

-   Alertas de segurança de serviços da Microsoft (Azure ATP, Microsoft Cloud App Security incluindo relatórios de TI Cloud Discovery Shadow, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Informações sobre Ameaças

-   Registos de segurança comuns (incluindo registos baseados em CEF, Barracuda e Syslog) (Se tiver o Centro de Segurança Azure, estes registos continuarão a ser recolhidos.)

-   Eventos de Segurança do Windows (Se tiver o Azure Security Center, estes registos continuarão a ser recolhidos.)

Nas primeiras 48 horas, os dados e as regras analíticas (incluindo a configuração da automatização em tempo real) deixarão de estar acessíveis ou consultados no Azure Sentinel.

**Após 30 dias estes recursos são removidos:**

-   Incidentes (incluindo metadados de investigação)

-   Regras analíticas

-   Marcadores

Os seus livros, livros de livros guardados, consultas de caça guardadas e cadernos não são removidos. **Alguns podem quebrar devido aos dados removidos. Pode removê-las manualmente.**

Depois de remover o serviço, existe um período de carência de 30 dias durante o qual pode reativar a solução e os seus dados e regras analíticas serão restaurados, mas os conectores configurados que foram desligados devem ser reconectados.

> [!NOTE]
> Se remover a solução, a sua subscrição continuará a ser registada no fornecedor de recursos Azure Sentinel. **Pode removê-lo manualmente.**




## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a remover o serviço Azure Sentinel. Se mudar de ideia e quiser instalá-la novamente:
- Começar a [embarcar no Azure Sentinel.](quickstart-onboard.md)
