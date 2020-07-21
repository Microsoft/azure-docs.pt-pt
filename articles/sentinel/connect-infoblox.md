---
title: Ligue os dados do Sistema Operativo de Identidade da Rede Infoblox (NIOS) ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados do Sistema Operativo de Identidade de Rede Infoblox (NIOS) ao Azure Sentinel.
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
ms.openlocfilehash: ed4f2d769dbda3dec7b353fddfd1e5e0f3d00f9b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532171"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Ligue o infoblox NIOS ao Azure Sentinel

Este artigo explica como ligar o seu [aparelho do Sistema Operativo de Identidade de Rede Infoblox (NIOS)](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) ao Azure Sentinel. O conector de dados Infoblox NIOS permite-lhe conectar facilmente os seus registos Infoblox com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. A integração entre Infoblox NIOS e Azure Sentinel faz uso do Syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Registos de Infoblox para o agente Syslog  

Configure infoblox para encaminhar mensagens Syslog para o seu espaço de trabalho Azure através do agente Syslog.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione o conector **Infoblox NIOS.**

1. Selecione **Abrir a página do conector**.

1. Siga as instruções na página **Infoblox NIOS.**

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics no âmbito do Syslog.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 

## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu a ligar Infoblox NIOS ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.