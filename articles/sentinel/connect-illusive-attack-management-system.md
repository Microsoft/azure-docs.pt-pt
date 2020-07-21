---
title: Ligue os dados do Sistema de Gestão de Ataques Illusive ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados do Sistema de Gestão de Ataques Illusive ao Azure Sentinel.
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
ms.openlocfilehash: 7d2d3871dd9836e2c68155aa82ce01dced128bf3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532170"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Ligue o seu Sistema de Gestão de Ataques Illusive ao Azure Sentinel

Este artigo explica como ligar o seu [Sistema de Gestão de Ataques Illusive](https://www.illusivenetworks.com/technology/platform/attack-detection-system) ao Azure Sentinel. O conector de dados do Sistema de Gestão de Ataques Illusive permite-lhe partilhar os dados de análise de superfície de ataque da Illusive e registos de incidentes com o Azure Sentinel e ver estas informações em dashboards dedicados que oferecem informações sobre o risco de superfície de ataque da sua organização (ASM Dashboard) e rastrear movimentos laterais não autorizados na rede da sua organização (ADS Dashboard).

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Avançados registos do Sistema de Gestão de Ataques Illusive para o agente Syslog  

Configure o Sistema de Gestão de Ataques para encaminhar mensagens Syslog em formato CEF para o seu espaço de trabalho Azure através do agente Syslog.

1. Inicie sessão na Consola Ilustiva e navegue para Definições >Reporting.

1. Encontre Serversץ Syslog

1. Insira as seguintes informações:
   - Nome do anfitrião: Endereço IP do agente Linux Syslog ou nome de anfitrião FQDN
   - Porto: 514
   - Protocolo: TCP
   - Mensagens de auditoria: Enviar mensagens de auditoria para o servidor

1. Para adicionar o servidor syslog, clique em Adicionar.

1. Para utilizar o esquema relevante no Log Analytics para o Sistema de Gestão de Ataques Illusive, procure o CommonSecurityLog.

## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu a ligar o Sistema de Gestão de Ataques Illusive ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
