---
title: Configure a sua solução de segurança para ligar os dados do CEF à Pré-visualização do Sentinela Azure| Microsoft Docs
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
ms.openlocfilehash: 80e5cc18888b0e014fc1f617ca27dee05146472e
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567674"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>PASSO 2: Configurar a sua solução de segurança para enviar mensagens CEF

Neste passo, irá efetuar as alterações de configuração necessárias na sua própria solução de segurança para enviar registos para o agente CEF.

## <a name="configure-a-solution-with-a-connector"></a>Configure uma solução com um conector

Se a sua solução de segurança já tiver um conector existente, utilize as instruções específicas do conector da seguinte forma:

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Check Point](connect-checkpoint.md)
- [Cisco ASA](connect-cisco.md)
- [Citrix WAF](connect-citrix-waf.md)
- [CyberArk Enterprise Password Vault](connect-cyberark.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)
- [Produtos da Forcepoint](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [Illusive Networks AMS](connect-illusive-attack-management-system.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Servidor Secreto Timótico](connect-thycotic-secret-server.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [TippingPoint da Trend Micro](connect-trend-micro-tippingpoint.md)
- [Plataforma forense da rede WireX](connect-wirex-systems.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>Configure qualquer outra solução

Se não existir um conector para a sua solução de segurança específica, utilize as seguintes instruções genéricas para encaminhar os registos para o agente CEF.

1. Aceda ao artigo de configuração específico para obter etapas sobre como configurar a sua solução para enviar mensagens CEF. Se a sua solução não estiver listada, no aparelho é necessário definir estes valores de modo a que o aparelho envie os registos necessários no formato necessário ao agente Syslog Azure Sentinel, com base no agente Log Analytics. Pode modificar estes parâmetros no seu aparelho, desde que os modifique também no daemon Syslog no agente Azure Sentinel.
    - Protocolo = TCP
    - Porta = 514
    - Formato = CEF
    - Endereço IP - certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que dedicou para este fim.

   Esta solução suporta o Syslog RFC 3164 ou o RFC 5424.

1. Para pesquisar eventos CEF em Log Analytics, `CommonSecurityLog` insira na janela de consulta.

1. Continue a PASSO 3: [valide a conectividade](connect-cef-verify.md).

> [!NOTE]
> **Alterar a fonte do campo TimeGenerated**
>
> - Por predefinição, o agente Log Analytics povoa o campo *TimeGenerated* no esquema com o momento em que o agente recebeu o evento do daemon Syslog. Como resultado, a hora em que o evento foi gerado no sistema de origem não é registada no Azure Sentinel.
>
> - Pode, no entanto, executar o seguinte comando, que irá descarregar e executar o `TimeGenerated.py` script. Este script configura o agente Log Analytics para povoar o campo *TimeGenerated* com a hora original do evento no seu sistema de origem, em vez do tempo que foi recebido pelo agente.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](./tutorial-detect-threats-built-in.md)