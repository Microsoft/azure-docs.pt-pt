---
title: Ligue os dados da Palo Alto Networks ao Azure Sentinel| Microsoft Docs
description: Saiba como utilizar o conector de dados palo Alto Networks para ligar facilmente os seus registos palo Alto Networks com o Azure Sentinel.
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
ms.openlocfilehash: 245db436fc3216fe5c8c8f51c50c0ac03190f9eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85564560"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Ligue as redes Palo Alto a Azure Sentinel



Este artigo explica como ligar o seu aparelho Palo Alto Networks ao Azure Sentinel. O conector de dados palo Alto Networks permite-lhe conectar facilmente os seus registos palo Alto Networks com Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. A utilização de Redes Palo Alto no Azure Sentinel irá fornecer-lhe mais informações sobre o uso da Internet da sua organização, e irá melhorar as suas capacidades de operação de segurança. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Forward Palo Alto Networks entra no agente Syslog

Configure a Palo Alto Networks para encaminhar mensagens Syslog em formato CEF para o seu espaço de trabalho Azure através do agente Syslog:
1.  Aceda aos [Guias de Configuração do Formato comum de Eventos (CEF)](https://docs.paloaltonetworks.com/resources/cef) e descarregue o pdf para o tipo de aparelho. Siga todas as instruções do guia para configurar o seu aparelho Palo Alto Networks para recolher eventos CEF. 

1.  Vá à [monitorização do Syslog Configure](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/configure-syslog-monitoring) e siga os passos 2 e 3 para configurar o reencaminhamento do seu aparelho Palo Alto Networks para O Azure Sentinel.

    1. Certifique-se de que define o formato do **servidor Syslog** para **BSD**.

       > [!NOTE]
       > As operações de cópia/pasta do PDF podem alterar o texto e inserir caracteres aleatórios. Para evitar isto, copie o texto para um editor e remova quaisquer caracteres que possam quebrar o formato de registo antes de colá-lo, como pode ver neste exemplo.
 
        ![Problema de cópia de texto CEF](./media/connect-cef/paloalto-text-prob1.png)

1. Para utilizar o esquema relevante no Log Analytics para os eventos palo Alto Networks, procure o **CommonSecurityLog**.

1. Continuar a [PASSO 3: Validar a conectividade](connect-cef-verify.md).




## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos palo Alto Networks ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.


