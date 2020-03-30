---
title: Ligue os dados da Cisco ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar dados da Cisco ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588404"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Ligue cisco ASA ao Sentinela Azure



Este artigo explica como ligar o seu aparelho Cisco ASA ao Azure Sentinel. O conector de dados Cisco ASA permite-lhe ligar facilmente os seus registos Cisco ASA com o Azure Sentinel, ver dashboards, criar alertas personalizados e melhorar a investigação. A utilização da Cisco ASA no Azure Sentinel irá fornecer-lhe mais informações sobre o uso da Internet da sua organização e aumentará as suas capacidades de operação de segurança. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Encaminhaos cisco ASA para o agente Syslog

A Cisco ASA não suporta o CEF, pelo que os registos são enviados como Syslog e o agente Azure Sentinel sabe como analisá-los como se fossem registos CEF. Configure a Cisco ASA para encaminhar mensagens Syslog para o seu espaço de trabalho Azure através do agente Syslog:

1. Vá enviar [mensagens Syslog para um servidor Syslog externo](https://aka.ms/asi-syslog-cisco-forwarding)e siga as instruções para configurar a ligação. Utilize estes parâmetros quando solicitado:
    - Detete a **porta** para 514 ou a porta que coloque no agente.
    - Coloque **syslog_ip** no endereço IP do agente.

1. Para utilizar o esquema relevante no Log Analytics para `CommonSecurityLog`os eventos cisco, procure .

1. Continuar a [PASSO 3: Validar a conectividade.](connect-cef-verify.md)




## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos Cisco ASA ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.


