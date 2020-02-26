---
title: Configure a sua solução de segurança para ligar os dados do CEF à Pré-visualização do Azure Sentinel. Microsoft Docs
description: Saiba como configurar a sua solução de segurança para ligar os dados do CEF ao Azure Sentinel.
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
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588455"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>PASSO 2: Configure a sua solução de segurança para enviar mensagens CEF

Neste passo, realizará as alterações de configuração necessárias na sua própria solução de segurança para enviar registos ao agente CEF.

## <a name="configure-a-solution-with-a-connector"></a>Configure uma solução com um conector

Se a sua solução de segurança já tiver um conector existente, utilize as instruções específicas do conector da seguinte forma:

- [Ponto de verificação](connect-checkpoint.md)
- [Rio Cisco](connect-cisco.md)
- [ExtraHop Reveal (x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [Uma salvaguarda de identidade](connect-one-identity.md)
- [Redes Palo Alto](connect-paloalto.md)
- [Tendência Micro Segurança Profunda](connect-trend-micro.md)
- [Rio Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Configure qualquer outra solução
Se não existir um conector para a sua solução de segurança específica, utilize as seguintes instruções genéricas para encaminhar os registos para o agente CEF.

1. Vá ao artigo de configuração específico para ver como configurar a sua solução para enviar mensagens CEF. Se a sua solução não estiver listada, no aparelho é necessário definir estes valores de modo a que o aparelho envie os registos necessários no formato necessário ao agente Syslog Azure Sentinel, com base no agente Log Analytics. Pode modificar estes parâmetros no seu aparelho, desde que os modifique também no daemon Syslog no agente Azure Sentinel.
    - Protocolo = TCP
    - Porta = 514
    - Formato = CEF
    - Endereço IP - certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que dedicou para o efeito.

   > [!NOTE]
   > Esta solução suporta syslog RFC 3164 ou RFC 5424.


1. Para utilizar o esquema relevante no Log Analytics para os eventos CEF, procure `CommonSecurityLog`.

1. Continuar a PASSO 3: [validar a conectividade.](connect-cef-verify.md)

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)

