---
title: Ligue os dados de Fortinet ao Azure Sentinel| Microsoft Docs
description: Ligue o seu aparelho Fortinet ao Azure Sentinel para ver os dashboards, criar alertas personalizados e melhorar a investigação.
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
ms.openlocfilehash: 60be22f439547d006f54e489833b63171e617e3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913998"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Conecte Fortinet a Azure Sentinel



Este artigo explica como ligar o seu aparelho Fortinet ao Azure Sentinel. O conector de dados Fortinet permite-lhe conectar facilmente os seus registos Fortinet com a Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. A utilização do Fortinet no Azure Sentinel irá fornecer-lhe mais informações sobre o uso da Internet da sua organização e aumentará as suas capacidades de operação de segurança. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Forward Fortinet logs para o agente Syslog

Configure Fortinet para encaminhar mensagens Syslog em formato CEF para o seu espaço de trabalho Azure através do agente Syslog.

1. Abra o CLI no seu aparelho Fortinet e execute os seguintes comandos:

    ```console
    config log syslogd setting
    set status enable
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    end
    ```

    - Substitua o **endereço IP** do servidor pelo endereço IP do agente.
    - Coloque a **porta de syslog** no **514** ou a porta definida no agente.
    - Para ativar o formato CEF nas primeiras versões FortiOS, poderá ser necessário executar o conjunto de **comandos csv desativado**.
 
   > [!NOTE] 
   > Para mais informações, aceda à [biblioteca de documentos Fortinet.](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary) Selecione a sua versão e utilize o **Manual** e **o Referencial de Mensagem de Registo**.

1. Para utilizar o esquema relevante no Azure Monitor Log Analytics para os eventos Fortinet, procure `CommonSecurityLog` .

1. Continuar a [PASSO 3: Validar a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a ligar os aparelhos Fortinet ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.


