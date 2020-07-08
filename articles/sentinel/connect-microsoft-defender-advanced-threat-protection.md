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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80756351"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Ligue alertas do Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> A ingestão de alertas de Proteção avançada de ameaças do Microsoft Defender está atualmente em visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

O conector [de proteção de ameaças avançadas do Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) permite transmitir alertas do Microsoft Defender Advanced Threat Protection para o Azure Sentinel. Isto irá permitir-lhe analisar de forma mais abrangente eventos de segurança em toda a sua organização e construir playbooks para uma resposta eficaz e imediata.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter uma licença válida para a Proteção de Ameaças Avançadas do Microsoft Defender, conforme descrito na [implementação do Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Deve ser administrador ou administrador de segurança do inquilino Azure Sentinel.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Ligue-se à Proteção Avançada de Ameaças avançadas do Microsoft Defender

Se o Microsoft Defender Advanced Threat Protection for implementado e ingerir os seus dados, os alertas podem ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **os conectores de dados,** clique no azulejo **de proteção de ameaças avançadas do Microsoft Defender** e selecione a página do **conector Open**.
1. Clique em **Ligar**. 
1. Para utilizar o esquema relevante no Log Analytics para os alertas ATP do Defender, procure por **SecurityAlert** e o **nome provedor** é **MDATP**.




## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar o Microsoft Defender ATP ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
