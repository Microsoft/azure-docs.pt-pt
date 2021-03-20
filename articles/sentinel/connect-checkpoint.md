---
title: Ligue os dados do Ponto de Verificação ao Azure Sentinel| Microsoft Docs
description: Configure o seu aparelho Check Point para encaminhar mensagens Syslog em formato CEF para o seu espaço de trabalho Azure Sentinel através do agente Syslog.
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
ms.openlocfilehash: 32cbdabef4d89c7fabb47a52ebf0589690b7ab33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85566050"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Ligue o Ponto de Verificação ao Sentinela Azure



Este artigo explica como ligar o seu aparelho Check Point ao Azure Sentinel. O conector de dados do Check Point permite-lhe ligar facilmente os seus registos de Check Point com o Azure Sentinel, para ver os dashboards, criar alertas personalizados e melhorar a investigação. A utilização do Check Point no Azure Sentinel irá fornecer-lhe mais informações sobre o uso da Internet da sua organização e aumentará as suas capacidades de operação de segurança. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Registos de Ponto de Verificação para a frente para o agente Syslog

Configure o seu aparelho Check Point para encaminhar mensagens Syslog no formato CEF para o seu espaço de trabalho Azure através do agente Syslog.

1. Vá à [Exportação de Registos de Pontos de Verificação.](https://aka.ms/asi-syslog-checkpoint-forwarding)
1. Desloque-se até à **Implementação Básica** e siga as instruções para configurar a ligação, utilizando as seguintes orientações:
   - Coloque a **porta Syslog** no **514** ou na porta que definiu no agente.
     - Substitua o **nome** e o endereço IP do **servidor-alvo** no CLI pelo nome do agente Syslog e endereço IP.
     - Desaceia o formato ao **CEF**.
1. Se estiver a utilizar as versões R77.30 ou R80.10, percorra as **instalações** e siga as instruções para instalar um Exportador de Registos para a sua versão.
1. Continuar a [PASSO 3: Validar a conectividade](connect-cef-verify.md).
 

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos check point ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- [Validar a conectividade.](connect-cef-verify.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.


