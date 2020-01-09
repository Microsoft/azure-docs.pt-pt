---
title: Conectar dados do Check Point ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Check Point ao Azure Sentinel.
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
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: 94658e34679a4cf99ad0e1ad02e60c6a1d190361
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610680"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Conectar ponto de verificação ao Azure Sentinel



Este artigo explica como conectar seu dispositivo do Check Point ao Azure Sentinel. O conector de dados do Check Point permite que você conecte facilmente seus logs do ponto de verificação com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. O uso do Check Point no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e aprimorará seus recursos de operação de segurança. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Encaminhar logs de ponto de verificação para o agente de syslog

Configure seu dispositivo de verificação de ponto para encaminhar mensagens de syslog no formato CEF para seu espaço de trabalho do Azure por meio do agente de syslog.

1. Vá para a [exportação de log do Check Point](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Role para baixo até a **implantação básica** e siga as instruções para configurar a conexão, usando as seguintes diretrizes:
   - Defina a **porta do syslog** como **514** ou a porta que você definiu no agente.
     - Substitua o **nome** e o **endereço IP do servidor de destino** na CLI pelo nome do agente de syslog e pelo endereço IP.
     - Defina o formato como **CEF**.
1. Se você estiver usando a versão R 77.30 ou R 80.10, role até **instalações** e siga as instruções para instalar um exportador de log para sua versão.
1. Continue na [etapa 3: validar a conectividade](connect-cef-verify.md).
 

## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar dispositivos do Check Point ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- [Valide a conectividade](connect-cef-verify.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.


