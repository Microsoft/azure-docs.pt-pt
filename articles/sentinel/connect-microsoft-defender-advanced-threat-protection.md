---
title: Ligue o Microsoft Defender para os dados do Endpoint ao Azure Sentinel. Microsoft Docs
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 69ce6cb42fd18364a7b93faa9cc01d1f793d5cd6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657540"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Ligue os alertas do Microsoft Defender para Endpoint (anteriormente Microsoft Defender ATP) 


> [!IMPORTANT]
> A ingestão de alertas microsoft Defender para endpoint está atualmente em visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

O [conector Microsoft Defender for Endpoint](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) permite-lhe transmitir alertas do Microsoft Defender para Endpoint para o Azure Sentinel. Isto irá permitir-lhe analisar de forma mais abrangente eventos de segurança em toda a sua organização e construir playbooks para uma resposta eficaz e imediata.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter uma licença válida para o Microsoft Defender para o Endpoint, conforme descrito no [Microsoft Defender para a implementação de Endpoint](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Deve ser administrador ou administrador de segurança do inquilino Azure Sentinel.


## <a name="connect-to-microsoft-defender-for-endpoint"></a>Ligue-se ao Microsoft Defender para o Endpoint

Se o Microsoft Defender for Endpoint estiver implantado e ingerir os seus dados, os alertas podem ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **os conectores de dados**, selecione **Microsoft Defender para Endpoint** (pode ainda ser chamado de Microsoft Defender Advanced Threat Protection) da galeria e selecione a página de **conector Open**.
1. Clique em **Ligar**. 
1. Para utilizar o esquema relevante no Log Analytics para os alertas ATP do Defender, procure por **SecurityAlert** e o **nome provedor** é **MDATP**.

## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar o Microsoft Defender para Endpoint ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
