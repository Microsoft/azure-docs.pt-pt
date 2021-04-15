---
title: Ligue o Ambiente de Trabalho Virtual do Windows ao | do Azure Sentinel Microsoft Docs
description: Aprenda a ligar os seus dados de ambiente de trabalho virtual do Windows ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2021
ms.author: bagol
ms.openlocfilehash: a835ea7b5e79ecc9b2d26dc6955984d0d0ff2906
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107380300"
---
# <a name="connect-windows-virtual-desktop-data-to-azure-sentinel"></a>Conecte os dados do Ambiente de Trabalho Virtual do Windows ao Azure Sentinel

Este artigo descreve como pode monitorizar os ambientes do Windows Virtual Desktop (WVD) utilizando o Azure Sentinel.

Por exemplo, monitorizar os ambientes de ambientes de ambiente de trabalho virtual do Windows pode permitir-lhe fornecer mais trabalho remoto utilizando ambientes de trabalho virtualizados, mantendo a postura de segurança da sua organização.

## <a name="windows-virtual-desktop-data-in-azure-sentinel"></a>Windows Virtual Desktop dados em Azure Sentinel

Os dados do Windows Virtual Desktop em Azure Sentinel incluem os seguintes tipos:


|Dados  |Descrição  |
|---------|---------|
|**Registos de eventos do Windows**     |  Os registos de eventos do Windows do ambiente WVD são transmitidos para um espaço de trabalho log analytics ativado pelo Azure Sentinel da mesma forma que os registos de eventos do Windows de outras máquinas windows, fora do ambiente WVD. <br><br>Instale o agente Log Analytics na sua máquina Windows e configuure os registos de eventos do Windows a enviar para o espaço de trabalho do Log Analytics.<br><br>Para obter mais informações, consulte:<br>- [Instale o agente Log Analytics nos computadores Windows](/azure/azure-monitor/agents/agent-windows)<br>- [Recolher fontes de dados de registo de eventos do Windows com o agente Log Analytics](/azure/azure-monitor/agents/data-sources-windows-events)<br>- [Conecte eventos de segurança do Windows](connect-windows-security-events.md)       |
|**Alertas microsoft Defender for Endpoint (MDE)**     |  Para configurar o MDE para o Windows Virtual Desktop, utilize o mesmo procedimento que faria em qualquer outro ponto final do Windows. <br><br>Para obter mais informações, consulte: <br>- [Configurar o Microsoft Defender para implementação de Endpoint](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Ligue os dados do Microsoft 365 Defender ao Azure Sentinel](connect-microsoft-365-defender.md)       |
|**Diagnósticos de desktop virtual do Windows**     | O diagnóstico virtual do Windows Desktop é uma funcionalidade do serviço Pcpg Virtual Desktop PaaS do Windows, que regista informações sempre que alguém atribui a função de Desktop Virtual do Windows que utiliza o serviço. <br><br>Cada registo contém informações sobre qual a função de Desktop Virtual do Windows envolvida na atividade, quaisquer mensagens de erro que apareçam durante a sessão, informações do inquilino e informações do utilizador. <br><br>A funcionalidade de diagnóstico cria registos de atividade tanto para as ações do utilizador como para as ações administrativas. <br><br>Para obter mais informações, consulte [Use Log Analytics para a funcionalidade de diagnóstico no Windows Virtual Desktop](/azure/virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019).        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Conecte os dados do Ambiente de Trabalho Virtual do Windows

Para começar a ingerir dados do Windows Virtual Desktop no Azure Sentinel, utilize as instruções da documentação virtual do Windows Desktop.

Para obter mais informações, consulte [os dados do Push Windows Virtual Desktop no seu espaço de trabalho Log Analytics](/azure/virtual-desktop/diagnostics-log-analytics).

## <a name="find-your-data"></a>Encontre os seus dados

Depois de estabelecida uma ligação bem sucedida, faça consultas no Azure Sentinel contra os seus dados do Log Analytics.

Por exemplo, consulte as consultas de amostras a partir da [documentação](/azure/virtual-desktop/diagnostics-log-analytics)do Windows Virtual Desktop .


O Azure Sentinel também fornece consultas incorporadas na área de desktop virtual **do General**  >  **Logs**  >  **WINDOWS:**

[![Consultas embutidas em Azure Sentinel. ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png)](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>Passos seguintes


Para obter mais informações, consulte o [Monitor Azure para o glossário virtual do Windows Desktop](/azure/virtual-desktop/azure-monitor-glossary).
