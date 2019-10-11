---
title: Conectar dados do Microsoft defender ATP ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de proteção avançada contra ameaças do Microsoft defender ao Azure Sentinel.
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
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 19d496ebb61a3ceb47f69f661e30ab529dc64f3d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256750"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Conectar alertas da proteção avançada contra ameaças do Microsoft defender 


> [!IMPORTANT]
> A ingestão dos logs da proteção avançada contra ameaças do Microsoft defender está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Você pode transmitir alertas da [proteção avançada contra ameaças do Microsoft defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) para o Azure Sentinel com um único clique. Essa conexão permite que você transmita os alertas da proteção avançada contra ameaças do Microsoft defender para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Licença válida para a proteção avançada contra ameaças do Microsoft defender habilitada conforme descrito em [validar provisionamento de licenciamento e concluir configuração para proteção avançada contra ameaças do Microsoft defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Você deve ser um administrador ou um administrador de segurança no locatário do Azure Sentinel.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Conectar-se à proteção avançada contra ameaças do Microsoft defender

Se a proteção avançada contra ameaças do Microsoft defender for implantada e ingerir seus dados, os alertas poderão ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados**, clique no bloco **proteção avançada contra ameaças do Microsoft defender** e selecione a **página abrir conector**.
1. Clique em **Ligar**. 
1. Para usar o esquema relevante no Log Analytics para os alertas do defender ATP, procure **SecurityAlert** e o **nome do provedor** é **MDATP**.




## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar o Microsoft defender ATP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
