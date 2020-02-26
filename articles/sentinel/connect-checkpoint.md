---
title: Ligue os dados do Ponto de Verificação ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados do Check Point ao Azure Sentinel.
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
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588421"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Ligar ponto de verificação ao Sentinela Azure



Este artigo explica como ligar o seu aparelho Check Point ao Azure Sentinel. O conector de dados check Point permite-lhe ligar facilmente os seus registos de Check Point com o Azure Sentinel, ver dashboards, criar alertas personalizados e melhorar a investigação. A utilização do Check Point no Azure Sentinel irá fornecer-lhe mais informações sobre o uso da Internet da sua organização e aumentará as suas capacidades de operação de segurança. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Registos do Ponto de Verificação Para A Frente para o agente Syslog

Configure o seu aparelho Check Point para encaminhar mensagens Syslog em formato CEF para o seu espaço de trabalho Azure através do agente Syslog.

1. Vá verificar a [exportação](https://aka.ms/asi-syslog-checkpoint-forwarding)de log de ponto .
1. Desloque-se até à **implantação básica** e siga as instruções para configurar a ligação, utilizando as seguintes diretrizes:
   - Desloque a **porta Syslog** para **514** ou a porta que definiu sobre o agente.
     - Substitua o **nome** e o endereço IP do **servidor-alvo** no CLI com o nome do agente Syslog e o endereço IP.
     - Detete o formato para **CEF**.
1. Se estiver a utilizar a versão R77.30 ou R80.10, desloque-se até às **Instalações** e siga as instruções para instalar um Exportador de Registos para a sua versão.
1. Continuar a [PASSO 3: Validar a conectividade.](connect-cef-verify.md)
 

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos Check Point ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- [Validar a conectividade.](connect-cef-verify.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.


