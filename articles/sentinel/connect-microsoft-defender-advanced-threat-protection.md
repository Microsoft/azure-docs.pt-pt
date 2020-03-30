---
title: Ligue os dados ATP do Microsoft Defender ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados de Proteção de Ameaças Avançadas do Microsoft Defender ao Azure Sentinel.
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588217"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Conecte alertas da Proteção avançada de ameaças do Microsoft Defender 


> [!IMPORTANT]
> A ingestão de registos de proteção de ameaças avançadas do Microsoft Defender está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
 

Pode transmitir alertas da [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) para o Azure Sentinel com um único clique. Esta ligação permite-lhe transmitir os alertas da Microsoft Defender Advanced Threat Protection para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Licença válida para proteção de ameaças avançadas Microsoft Defender que está ativada como descrito no fornecimento de [licenças de validação e configuração completa para a Proteção de Ameaças Avançadas Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Deve ser administrador ou administrador de segurança do inquilino Azure Sentinel.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Ligue-se à Proteção avançada de ameaças do Microsoft Defender

Se o Microsoft Defender Advanced Threat Protection for implementado e ingerir os seus dados, os alertas podem ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados,** clique no azulejo de **proteção de ameaças avançada do Microsoft Defender** e selecione página de **conector Open**.
1. Clique em **Ligar**. 
1. Para utilizar o esquema relevante no Log Analytics para os alertas ATP do Defender, procure **securityAlert** e o **nome Fornecedor** é **MDATP**.




## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Microsoft Defender ATP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
