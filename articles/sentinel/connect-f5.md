---
title: Ligue os dados de F5 ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar dados de F5 ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588268"
---
# <a name="connect-f5-to-azure-sentinel"></a>Ligue F5 ao Sentinela Azure

Este artigo explica como ligar o seu aparelho F5 ao Azure Sentinel. O conector de dados F5 permite-lhe ligar facilmente os seus registos F5 com o Azure Sentinel, ver dashboards, criar alertas personalizados e melhorar a investigação. A utilização de F5 no Azure Sentinel irá fornecer-lhe mais informações sobre o uso da Internet da sua organização e aumentará as suas capacidades de operação de segurança. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Configure o seu F5 para enviar mensagens CEF

1. Vá ao Registo de Eventos de Segurança de [Aplicações de Configuração F5](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)e siga as instruções para configurar a exploração remota, utilizando as seguintes diretrizes:
   - Delineie o tipo de **armazenamento remoto** para **CEF**.
   - Definir o **Protocolo** para **a TCP**.
   - Detete o **endereço IP** para o endereço IP do servidor Syslog.
   - Detete o número da **porta** para **514**, ou a porta que definiu o seu agente para utilizar.
   - Pode definir o tamanho máximo da **corda de consulta** para o tamanho que se define no seu agente.

1. Para utilizar o esquema relevante no Log Analytics para `CommonSecurityLog`os eventos CEF, procure .

1. Continuar a [PASSO 3: Validar a conectividade.](connect-cef-verify.md)


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar F5 ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.

