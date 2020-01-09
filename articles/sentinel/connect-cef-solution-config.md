---
title: Configure sua solução de segurança para conectar dados de CEF à visualização do Azure Sentinel | Microsoft Docs
description: Saiba como configurar sua solução de segurança para conectar dados de CEF ao Azure Sentinel.
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
ms.openlocfilehash: 2513638be6f895598f93758d8d8dbdf04c9561e9
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615362"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>ETAPA 2: configurar sua solução de segurança para enviar mensagens CEF

Nesta etapa, você executará as alterações de configuração necessárias na sua própria solução de segurança para enviar logs para o agente CEF.

## <a name="configure-a-solution-with-a-connector"></a>Configurar uma solução com um conector

Se sua solução de segurança já tiver um conector existente, use as instruções específicas do conector da seguinte maneira:

- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [Uma proteção de identidade](connect-one-identity.md)
- [Redes Palo Alto](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Configurar qualquer outra solução
Se não existir um conector para sua solução de segurança específica, use as instruções genéricas a seguir para encaminhar logs para o agente CEF.

1. Vá para o artigo de configuração específico para obter as etapas sobre como configurar sua solução para enviar mensagens CEF. Se sua solução não estiver listada, no dispositivo, você precisará definir esses valores para que o dispositivo envie os logs necessários no formato necessário para o agente de syslog do Sentinela do Azure, com base no agente de Log Analytics. Você pode modificar esses parâmetros em seu dispositivo, contanto que também os modifique no daemon do syslog no agente do Azure Sentinel.
    - Protocolo = TCP
    - Porta = 514
    - Formato = CEF
    - Endereço IP-certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que você dedicaram para essa finalidade.

   > [!NOTE]
   > Esta solução oferece suporte a RFC 3164 ou a RFC 5424 do syslog.


1. Para usar o esquema relevante em Log Analytics para os eventos CEF, procure `CommonSecurityLog`.

1. Continue na etapa 3: [validar a conectividade](connect-cef-verify.md).

## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar os dispositivos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

