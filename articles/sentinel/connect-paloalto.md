---
title: Ligue os dados das Redes Palo Alto ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados da Palo Alto Networks ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588132"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Ligue as Redes Palo Alto ao Sentinela Azure



Este artigo explica como ligar o seu aparelho Palo Alto Networks ao Azure Sentinel. O conector de dados Palo Alto Networks permite-lhe ligar facilmente os seus registos da Palo Alto Networks com o Azure Sentinel, ver dashboards, criar alertas personalizados e melhorar a investigação. A utilização de Redes Palo Alto no Azure Sentinel irá fornecer-lhe mais informações sobre o uso da Internet da sua organização, e irá melhorar as suas capacidades de operação de segurança. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Forward Palo Alto Networks logs to the Syslog agent

Configure as Redes Palo Alto para retransmitir mensagens Syslog em formato CEF para o seu espaço de trabalho Azure através do agente Syslog:
1.  Vá a Guias de Configuração de [Formato comum de Eventos (CEF)](https://docs.paloaltonetworks.com/resources/cef) e descarregue o pdf para o seu tipo de aparelho. Siga todas as instruções do guia para configurar o seu aparelho Palo Alto Networks para recolher eventos CEF. 

1.  Vá à monitorização do [Syslog configure](https://aka.ms/asi-syslog-paloalto-forwarding) e siga os passos 2 e 3 para configurar o reencaminhado do seu aparelho Palo Alto Networks para o Azure Sentinel.

    1. Certifique-se de definir o formato do **servidor Syslog** para **BSD**.

       > [!NOTE]
       > As operações de cópia/pasta do PDF podem alterar o texto e inserir caracteres aleatórios. Para evitar isto, copie o texto para um editor e remova quaisquer caracteres que possam quebrar o formato de registo antes de o colar, como pode ver neste exemplo.
 
        ![Problema de cópia de texto CEF](./media/connect-cef/paloalto-text-prob1.png)

1. Para utilizar o esquema relevante no Log Analytics para os eventos da Palo Alto Networks, procure o **CommonSecurityLog**.

1. Continuar a [PASSO 3: Validar a conectividade.](connect-cef-verify.md)




## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos da Palo Alto Networks ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.


