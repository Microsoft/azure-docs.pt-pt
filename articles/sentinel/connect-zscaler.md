---
title: Ligue os dados do Zscaler ao Azure Sentinel. Microsoft Docs
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
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587996"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Ligue o acesso à Internet de Zscaler ao Sentinela Azure

> [!IMPORTANT]
> O conector de dados Zscaler em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Este artigo explica como ligar o seu aparelho zscaler Internet Access ao Azure Sentinel. O conector de dados Zscaler permite-lhe ligar facilmente os seus registos de Acesso à Internet (ZIA) ao Azure Sentinel, ver dashboards, criar alertas personalizados e melhorar a investigação. A utilização do Zscaler no Azure Sentinel irá fornecer-lhe mais informações sobre o uso da Internet da sua organização e aumentará as suas capacidades de operação de segurança. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Configure o seu Zscaler para enviar mensagens CEF

1. No aparelho Zscaler é necessário definir estes valores de modo a que o aparelho envie os registos necessários no formato necessário ao agente Syslog Azure Sentinel, com base no agente Log Analytics. Pode modificar estes parâmetros no seu aparelho, desde que os modifique também no daemon Syslog no agente Azure Sentinel.
    - Protocolo = TCP
    - Porta = 514
    - Formato = CEF
    - Endereço IP - certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que dedicou para o efeito.
 Para mais informações, consulte o Guia de [Implantação Zscaler e Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Esta solução suporta syslog RFC 3164 ou RFC 5424.


1. Para utilizar o esquema relevante no Log Analytics para `CommonSecurityLog`os eventos CEF, procure .
1. Continuar a [PASSO 3: Validar a conectividade.](connect-cef-verify.md)


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Acesso à Internet Zscaler ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.


