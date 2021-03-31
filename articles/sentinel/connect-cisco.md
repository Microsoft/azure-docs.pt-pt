---
title: Ligue os dados da Cisco ao Azure Sentinel| Microsoft Docs
description: Saiba como ligar o seu aparelho Cisco ASA ao Azure Sentinel para ver os dashboards, criar alertas personalizados e melhorar a investigação.
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
ms.openlocfilehash: e8a64dd3e47384ba2bf7579f8052177252634622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85566039"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Ligue cisco ASA a Azure Sentinel



Este artigo explica como ligar o seu aparelho Cisco ASA ao Azure Sentinel. O conector de dados Cisco ASA permite-lhe conectar facilmente os seus registos Cisco ASA com a Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. A utilização da Cisco ASA no Azure Sentinel irá fornecer-lhe mais informações sobre o uso da Internet da sua organização e irá melhorar as suas capacidades de operação de segurança. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Avançados registos cisco ASA para o agente Syslog

A Cisco ASA não suporta o CEF, por isso os registos são enviados como Syslog e o agente Azure Sentinel sabe como analisá-los como se fossem registos CEF. Configure a Cisco ASA para encaminhar mensagens Syslog para o seu espaço de trabalho Azure através do agente Syslog:

1. Vá enviar [mensagens Syslog para um servidor Syslog externo](https://aka.ms/asi-syslog-cisco-forwarding)e siga as instruções para configurar a ligação. Utilize estes parâmetros quando solicitado:
    - Desateia a **porta** para o 514 ou a porta que definiu no agente.
    - Desaponhe **syslog_ip** no endereço IP do agente.

1. Para utilizar o esquema relevante no Log Analytics para os eventos cisco, procure `CommonSecurityLog` .

1. Continuar a [PASSO 3: Validar a conectividade](connect-cef-verify.md).




## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os eletrodomésticos Cisco ASA ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.


