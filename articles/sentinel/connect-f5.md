---
title: Conectar dados F5 ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do F5 ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: cf6dc6977ff066b646beac4db5562ae8d97ab066
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610561"
---
# <a name="connect-f5-to-azure-sentinel"></a>Conectar F5 ao Azure Sentinel

Este artigo explica como conectar seu dispositivo F5 ao Azure Sentinel. O conector de dados F5 permite que você conecte seus logs F5 com facilidade com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. Usar F5 no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e aprimorará seus recursos de operação de segurança. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Configurar o F5 para enviar mensagens CEF

1. Acesse [F5 Configurando o log de eventos de segurança do aplicativo](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)e siga as instruções para configurar o log remoto, usando as seguintes diretrizes:
   - Defina o **tipo de armazenamento remoto** como **CEF**.
   - Defina o **protocolo** como **TCP**.
   - Defina o **endereço IP** para o endereço IP do servidor syslog.
   - Defina o **número da porta** como **514**ou a porta que você definiu o agente a ser usado.
   - Você pode definir o **tamanho máximo da cadeia de caracteres de consulta** para o tamanho definido no agente.

1. Para usar o esquema relevante em Log Analytics para os eventos CEF, procure `CommonSecurityLog`.

1. Continue na [etapa 3: validar a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar F5 ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

