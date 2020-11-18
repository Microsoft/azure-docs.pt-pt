---
title: Ligue os dados do Microsoft Defender for Identity (anteriormente Azure ATP) ao Azure Sentinel. Microsoft Docs
description: Saiba como transmitir registos do Microsoft Defender for Identity (anteriormente Azure Advanced Threat Protection) (ATP) para Azure Sentinel com um único clique.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: b0cafcbc9fa6f0fcb31f44b44dfbe4fc3e10c17d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656076"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Conecte dados do Microsoft Defender for Identity (anteriormente Azure Advanced Threat Protection)

> [!IMPORTANT]
> O conector de dados de identidade do Microsoft Defender em Azure Sentinel encontra-se atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode transmitir registos do [Microsoft Defender for Identity](/azure-advanced-threat-protection/what-is-atp) para Azure Sentinel com um único clique.

## <a name="prerequisites"></a>Pré-requisitos

- Utilizador com permissões globais de administrador ou administrador de segurança
- Deve ser um cliente de pré-visualização do Microsoft Defender for Identity e permitir a integração entre o Microsoft Defender for Identity e o Microsoft Cloud App Security. Para obter mais informações, consulte [o Microsoft Defender for Identity Integration](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection).

## <a name="connect-to-microsoft-defender-for-identity"></a>Ligue-se ao Microsoft Defender por identidade

Certifique-se de que a versão de pré-visualização do Microsoft Defender for Identity está [ativada na sua rede](/azure-advanced-threat-protection/install-atp-step1).
Se o Microsoft Defender for Identity for implementado e ingerir os seus dados, os alertas suspeitos podem ser facilmente transmitidos para o Azure Sentinel. Pode levar até 24 horas para que os alertas comecem a fluir para Azure Sentinel.


1. Para ligar o Microsoft Defender for Identity ao Azure Sentinel, tem primeiro de permitir a integração entre o Microsoft Defender for Identity e o Microsoft Cloud App Security. Para obter informações sobre como fazê-lo, consulte [o Microsoft Defender for Identity integration](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection).

1. Em Azure Sentinel, selecione **os conectores de dados** e, em seguida, clique no **telha Microsoft Defender for Identity (Preview).**

1. Pode selecionar se deseja que os alertas do Microsoft Defender for Identity gerem automaticamente incidentes no Azure Sentinel. No **âmbito do Create incidents** select **Enable** to enable the pred pred alytic rule that creates incidents automaticly from alerts gerados no serviço de segurança conectado. Em seguida, pode editar esta regra sob **a análise** e, em seguida, **as regras Ative**.

1. Clique em **Ligar**.

1. Para utilizar o esquema relevante no Log Analytics para os alertas de identidade do Microsoft Defender, procure por **SecurityAlert**.

> [!NOTE]
> Se os alertas forem superiores a 30 KB, o Azure Sentinel deixa de exibir o campo Entidades nos alertas.

## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar o Microsoft Defender for Identity ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)