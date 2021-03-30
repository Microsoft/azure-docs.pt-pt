---
title: Ligue os dados do Sistema de Gestão de Ataques Illusive ao Azure Sentinel| Microsoft Docs
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
ms.openlocfilehash: ea7953be25473357f7ed572fa8b76076edc6f75a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87038122"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Ligue o seu Sistema de Gestão de Ataques Illusive ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados do Sistema de Gestão de Ataques Illusive em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como ligar o seu [Sistema de Gestão de Ataques Illusive](https://www.illusivenetworks.com/technology/platform/attack-detection-system) ao Azure Sentinel. O conector de dados do Sistema de Gestão de Ataques Illusive permite-lhe partilhar os dados de análise de superfície de ataque da Illusive e registos de incidentes com o Azure Sentinel e ver estas informações em dashboards dedicados que oferecem informações sobre o risco de superfície de ataque da sua organização (ASM Dashboard) e rastrear movimentos laterais não autorizados na rede da sua organização (ADS Dashboard).

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Avançados registos do Sistema de Gestão de Ataques Illusive para o agente Syslog  

Configure o Sistema de Gestão de Ataques para encaminhar mensagens Syslog em formato CEF para o seu espaço de trabalho Azure através do agente Syslog.

1. Inicie sessão na Consola Ilustiva e navegue para Definições >Reporting.

1. Encontre os servidores Syslog.

1. Insira as seguintes informações:
   - Nome do anfitrião: Endereço IP do agente Linux Syslog ou nome de anfitrião FQDN
   - Porto: 514
   - Protocolo: TCP
   - Mensagens de auditoria: Enviar mensagens de auditoria para o servidor

1. Para adicionar o servidor syslog, clique em Adicionar.

1. Para utilizar o esquema relevante em **Registos** para o Sistema de Gestão de Ataques Ilustivos, procure o **CommonSecurityLog**.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Sistema de Gestão de Ataques Illusive ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
