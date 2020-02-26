---
title: Ligue os dados da Fortinet ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar dados da Fortinet ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588200"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Ligue fortinet a Azure Sentinel



Este artigo explica como ligar o seu aparelho Fortinet ao Azure Sentinel. O conector de dados Fortinet permite-lhe ligar facilmente os seus registos Fortinet com o Azure Sentinel, ver dashboards, criar alertas personalizados e melhorar a investigação. A utilização do Fortinet no Azure Sentinel irá fornecer-lhe mais informações sobre o uso da Internet da sua organização e aumentará as suas capacidades de operação de segurança. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Registos de Fortinet para o agente Syslog

Configure a Fortinet para retransmitir mensagens Syslog em formato CEF para o seu espaço de trabalho Azure através do agente Syslog.

1. Abra o CLI no seu aparelho Fortinet e execute os seguintes comandos:

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Substitua o **endereço IP** do servidor pelo endereço IP do agente.
    - Coloque a **porta de slog** para **514** ou a porta colocada no agente.
    - Para ativar o formato CEF nas versões FortiOS iniciais, poderá ser necessário executar o conjunto de **comandocsv desativar**.
 
   > [!NOTE] 
   > Para mais informações, vá à biblioteca de [documentos Fortinet.](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary) Selecione a sua versão e utilize o **Manual** e referência de **mensagens**de registo .

1. Para utilizar o esquema relevante no Azure Monitor Log Analytics para os eventos Fortinet, procure `CommonSecurityLog`.

1. Continuar a [PASSO 3: Validar a conectividade.](connect-cef-verify.md)


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a ligar os aparelhos Fortinet ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.


