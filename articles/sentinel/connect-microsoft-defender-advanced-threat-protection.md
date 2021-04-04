---
title: Ligue o Microsoft Defender para os dados do Ponto Final ao Azure Sentinel | Microsoft Docs
description: Saiba como ligar o Microsoft Defender para os dados do Endpoint (anteriormente Microsoft Defender ATP) ao Azure Sentinel.
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
ms.openlocfilehash: 0db4e0fe0472c75f1eae392980ae697f53007244
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98623371"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Ligue os alertas do Microsoft Defender para Endpoint (anteriormente Microsoft Defender ATP)

> [!IMPORTANT]
>
> - **O Microsoft Defender for Endpoint** era anteriormente conhecido como **Microsoft Defender Advanced Threat Protection** ou **MDATP**.
>
>     Pode ver o nome antigo ainda em uso no produto (incluindo o seu conector de dados em Azure Sentinel) por um período de tempo.

O [conector Microsoft Defender for Endpoint](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) permite-lhe transmitir alertas do Microsoft Defender para Endpoint para o Azure Sentinel. Isto irá permitir-lhe analisar de forma mais abrangente eventos de segurança em toda a sua organização e construir playbooks para uma resposta eficaz e imediata.

> [!NOTE]
>
> Para ingerir os novos registos de dados brutos do Microsoft Defender para [a caça avançada](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)do Endpoint, utilize o novo conector para o Microsoft 365 Defender (anteriormente Microsoft Threat Protection, consulte [documentação).](./connect-microsoft-365-defender.md)

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter uma licença válida para o Microsoft Defender para o Endpoint, conforme descrito no [Microsoft Defender para a implementação de Endpoint](/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- Você deve ser um administrador global ou um administrador de segurança no inquilino Azure Sentinel.

## <a name="connect-to-microsoft-defender-for-endpoint"></a>Ligue-se ao Microsoft Defender para o Endpoint

Se o Microsoft Defender for Endpoint estiver implantado e ingerir os seus dados, os alertas podem ser facilmente transmitidos para o Azure Sentinel.

1. No Azure Sentinel, selecione **os conectores de dados**, selecione **Microsoft Defender para Endpoint** (pode ainda ser chamado *de Microsoft Defender Advanced Threat Protection*) da galeria e selecione a página **de conector Open**.

1. Clique em **Ligar**. 

1. Para consultar o Microsoft Defender para alertas de ponto final em **Logs,** insira o **SecurityAlert** na janela de consulta e adicione um filtro onde o **nome do Fornecedor** é **MDATP**.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Microsoft Defender para Endpoint ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](./tutorial-detect-threats-built-in.md)