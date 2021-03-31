---
title: Ligue os dados VIP da Symantec ao Azure Sentinel| Microsoft Docs
description: Saiba como ligar os dados VIP da Symantec ao Azure Sentinel.
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
ms.openlocfilehash: c7429108f70d735cb6e314a0d4daa27ba0d31637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100090416"
---
# <a name="connect-your-symantec-vip-to-azure-sentinel"></a>Ligue o seu Symantec VIP ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados Symantec VIP em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como ligar o seu aparelho [Symantec VIP](https://vip.symantec.com/) ao Azure Sentinel. O conector de dados Symantec VIP permite-lhe conectar facilmente os seus registos VIP Symantec com Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. A integração entre a Symantec VIP e a Azure Sentinel faz uso do Syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="forward-symantec-vip-logs-to-the-syslog-agent"></a>Avançados registos VIP Symantec para o agente Syslog  

Configure a Symantec VIP para encaminhar mensagens Syslog para o seu espaço de trabalho Azure através do agente Syslog.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione o conector **VIP Symantec.**

1. Selecione **Abrir a página do conector**.

1. Siga as instruções na página **VIP da Symantec.**

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics no âmbito do Syslog.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics. 

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar os registos VIP da Symantec ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.