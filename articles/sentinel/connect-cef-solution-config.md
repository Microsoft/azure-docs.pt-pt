---
title: Configure a sua solução de segurança para ligar os dados do CEF ao Azure Sentinel Preview Microsoft Docs
description: Saiba como configurar a sua solução de segurança para ligar dados CEF ao Azure Sentinel.
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
ms.openlocfilehash: e2ed3680a0867ab8f7e2ad41603883f07a4be427
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655753"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>PASSO 2: Configurar a sua solução de segurança para enviar mensagens CEF

Neste passo, irá efetuar as alterações de configuração necessárias na sua própria solução de segurança para enviar registos para o agente CEF.

## <a name="configure-a-solution-with-a-connector"></a>Configure uma solução com um conector

Se a sua solução de segurança já tiver um conector existente, utilize as instruções específicas do conector da seguinte forma:

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Configure qualquer outra solução

Se não existir um conector para a sua solução de segurança específica, utilize as seguintes instruções genéricas para encaminhar os registos para o agente CEF.

1. Aceda ao artigo de configuração específico para obter etapas sobre como configurar a sua solução para enviar mensagens CEF. Se a sua solução não estiver listada, no aparelho é necessário definir estes valores de modo a que o aparelho envie os registos necessários no formato necessário ao agente Syslog Azure Sentinel, com base no agente Log Analytics. Pode modificar estes parâmetros no seu aparelho, desde que os modifique também no daemon Syslog no agente Azure Sentinel.
    - Protocolo = TCP
    - Porta = 514
    - Formato = CEF
    - Endereço IP - certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que dedicou para este fim.

   > [!NOTE]
   > Esta solução suporta o Syslog RFC 3164 ou o RFC 5424.

1. Para utilizar o esquema relevante no Log Analytics para os eventos CEF, procure `CommonSecurityLog` .

1. Continue a PASSO 3: [valide a conectividade](connect-cef-verify.md).

## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu a ligar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](./tutorial-detect-threats-built-in.md)