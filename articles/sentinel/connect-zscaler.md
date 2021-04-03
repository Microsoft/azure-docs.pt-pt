---
title: Ligue os dados do Zscaler ao Azure Sentinel| Microsoft Docs
description: Saiba como ligar os dados do Zscaler ao Azure Sentinel.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: d788219e36ac94b1d13b2f4819c3e546622ddff1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655260"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Ligue o acesso à Internet Zscaler ao Azure Sentinel

Este artigo explica como ligar o seu aparelho Zscaler Internet Access ao Azure Sentinel. O conector de dados Zscaler permite-lhe ligar facilmente os seus registos Zscaler Internet Access (ZIA) com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. A utilização do Zscaler no Azure Sentinel irá fornecer-lhe mais informações sobre o uso da Internet da sua organização e aumentará as suas capacidades de operação de segurança. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Configure o seu Zscaler para enviar mensagens CEF

1. No aparelho Zscaler é necessário definir estes valores de modo a que o aparelho envie os registos necessários no formato necessário ao agente Syslog Azure Sentinel, com base no agente Log Analytics. Pode modificar estes parâmetros no seu aparelho, desde que os modifique também no daemon Syslog no agente Azure Sentinel.
    - Protocolo = TCP
    - Porta = 514
    - Formato = CEF
    - Endereço IP - certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que dedicou para este fim.
 Para mais informações, consulte o Guia de [Implantação do Zscaler e do Azure Sentinel.](https://aka.ms/ZscalerCEFInstructions)
 
   > [!NOTE]
   > Esta solução suporta o Syslog RFC 3164 ou o RFC 5424.


1. Para utilizar o esquema relevante no Log Analytics para os eventos CEF, procure `CommonSecurityLog` .
1. Continuar a [PASSO 3: Validar a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Zscaler Internet Access ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](./tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.