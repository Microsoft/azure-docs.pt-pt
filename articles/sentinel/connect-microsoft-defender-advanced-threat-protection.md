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
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756351"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Conecte alertas da Proteção avançada de ameaças do Microsoft Defender 


> [!IMPORTANT]
> A ingestão de alertas avançados de proteção contra ameaças do Microsoft Defender está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
 

O conector [de proteção de ameaças avançada do Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) permite-lhe transmitir alertas da Microsoft Defender Advanced Threat Protection para o Azure Sentinel. Isto permitir-lhe-á analisar mais exaustivamente os eventos de segurança em toda a sua organização e construir playbooks para uma resposta eficaz e imediata.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter uma licença válida para a Proteção avançada de ameaças do Microsoft Defender, tal como descrito na [implementação ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)do Microsoft Defender . 
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
