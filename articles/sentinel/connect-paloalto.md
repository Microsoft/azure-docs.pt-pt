---
title: Conectar dados do Palo Alto Networks ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Palo Alto Networks ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: bfdf961906626b485f07ddd29da9b8509dc53cc0
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610527"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Conectar redes Palo Alto ao Azure Sentinel



Este artigo explica como conectar seu dispositivo Palo Alto Networks ao Azure Sentinel. O conector de dados do Palo Alto Networks permite que você conecte facilmente seus logs do Palo Alto Networks com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. O uso de redes Palo Alto no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e aprimorará seus recursos de operação de segurança. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Encaminhar os logs do Palo Alto Networks para o agente de syslog

Configure as redes Palo Alto para encaminhar mensagens de syslog no formato CEF para seu espaço de trabalho do Azure por meio do agente de syslog:
1.  Vá para os [guias de configuração de CEF (formato de evento comum)](https://docs.paloaltonetworks.com/resources/cef) e baixe o PDF para o tipo de dispositivo. Siga todas as instruções no guia para configurar seu dispositivo Palo Alto Networks para coletar eventos CEF. 

1.  Vá para [Configurar o monitoramento de syslog](https://aka.ms/asi-syslog-paloalto-forwarding) e siga as etapas 2 e 3 para configurar o encaminhamento de eventos CEF do seu dispositivo Palo Alto Networks para o Azure Sentinel.

    1. Certifique-se de definir o **formato do servidor syslog** como **BSD**.

       > [!NOTE]
       > As operações de copiar/colar do PDF podem alterar o texto e inserir caracteres aleatórios. Para evitar isso, copie o texto para um editor e remova todos os caracteres que possam quebrar o formato de log antes de colá-lo, como você pode ver neste exemplo.
 
        ![Problema de cópia de texto de CEF](./media/connect-cef/paloalto-text-prob1.png)

1. Para usar o esquema relevante em Log Analytics para os eventos do Palo Alto Networks, procure **CommonSecurityLog**.

1. Continue na [etapa 3: validar a conectividade](connect-cef-verify.md).




## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar os dispositivos Palo Alto Networks ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.


