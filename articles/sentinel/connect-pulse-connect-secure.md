---
title: Ligue os dados de ligação de pulso secure a Azure Sentinel/ Microsoft Docs
description: Saiba como ligar os dados pulse Connect Secure ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 01d41b20c543262280e215cf4e31fd0cccce877f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531969"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Ligue o seu Pulse Connect Secure ao Azure Sentinel

Este artigo explica como ligar o seu aparelho [Pulse Connect Secure](https://www.pulsesecure.net/products/pulse-connect-secure/) ao Azure Sentinel. O conector de dados Pulse Connect Secure permite-lhe ligar facilmente os seus registos Pulse Connect Secure com Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. A integração entre Pulse Connect Secure e Azure Sentinel faz uso do Syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Pulse Dianteiro Conecte registos seguros ao agente Syslog  

Configure o Pulse Connect Secure para encaminhar mensagens Syslog para o seu espaço de trabalho Azure através do agente Syslog.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione o conector **Pulse Connect Secure.**

1. Selecione **Abrir a página do conector**.

1. Siga as instruções na página **Pulse Connect Secure.**

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics no âmbito do Syslog.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 

## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu a ligar o Pulse Connect Secure ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.