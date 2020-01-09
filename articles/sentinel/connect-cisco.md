---
title: Conectar dados Cisco ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados Cisco ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: 67a76f1fcd4cfcdd372407ae62eb03d5905cda68
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610663"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Conectar o Cisco ASA ao Azure Sentinel



Este artigo explica como conectar seu dispositivo Cisco ASA ao Azure Sentinel. O Cisco ASA data Connector permite que você conecte facilmente seus logs do Cisco ASA com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. Usar o Cisco ASA no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e aprimorará seus recursos de operação de segurança. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Encaminhe os logs do Cisco ASA para o agente de syslog

O Cisco ASA não dá suporte a CEF, portanto, os logs são enviados como syslog e o agente do Azure Sentinel sabe como analisá-los como se fossem logs de CEF. Configure o Cisco ASA para encaminhar mensagens de syslog para seu espaço de trabalho do Azure por meio do agente de syslog:

1. Vá para [enviar mensagens do syslog para um servidor syslog externo](https://aka.ms/asi-syslog-cisco-forwarding)e siga as instruções para configurar a conexão. Use estes parâmetros quando solicitado:
    - Defina **porta** como 514 ou a porta que você definiu no agente.
    - Defina **syslog_ip** para o endereço IP do agente.

1. Para usar o esquema relevante em Log Analytics para os eventos Cisco, procure por `CommonSecurityLog`.

1. Continue na [etapa 3: validar a conectividade](connect-cef-verify.md).




## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar os appliances Cisco ASA ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.


