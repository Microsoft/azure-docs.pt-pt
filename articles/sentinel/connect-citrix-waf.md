---
title: Ligue os dados da Citrix WAF ao Azure Sentinel. Microsoft Docs
description: Aprenda a utilizar o conector de dados Citrix WAF para puxar os seus registos para o Azure Sentinel. Veja os dados da Citrix WAF nos livros, crie alertas e melhore a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 475e04ad3dd8c7cc51d6345b4b51dd68cff5b597
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103126"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>Ligue o seu Citrix WAF ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados Citrix Web Application Firewall (WAF) em Azure Sentinel está atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um contrato de nível de serviço. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como ligar o seu aparelho Citrix Web Application Firewall (WAF) ao Azure Sentinel. O conector de dados Citrix WAF permite-lhe ligar facilmente os seus registos Citrix WAF com Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. Ao ligar os registos CEF da Citrix WAF ao Azure Sentinel, pode aproveitar a pesquisa e a correlação, alertando e ameaçando o enriquecimento de inteligência para cada registo.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Registos da Citrix WAF para o agente Syslog  

Citrix WAF envia mensagens Syslog em formato CEF para um servidor de reencaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng) com o agente Log Analytics instalado nele, que encaminha os registos para o seu espaço de trabalho Azure Sentinel.

1. Se não tiver um servidor de reencaminhamento de registos, consulte [estas instruções](connect-cef-agent.md) para pôr uma a funcionar.

1. Siga as instruções fornecidas pela Citrix para [configurar o WAF,](https://support.citrix.com/article/CTX234174)configurar a [marcação CEF](https://support.citrix.com/article/CTX136146)e [configurar o envio dos registos para o seu reencaminhador](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html)de registos . Certifique-se de que envia os registos para a porta TCP 514 no endereço IP da máquina de reencaminhador de registos.

1. Valide a sua ligação e verifique a ingestão de dados utilizando [estas instruções](connect-cef-verify.md). Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** sob a secção **Azure Sentinel,** na tabela *CommonSecurityLog.*

Para consultar os registos Citrix WAF em Log Analytics, introduza `CommonSecurityLog` na parte superior da janela de consulta.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a Citrix WAF ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.