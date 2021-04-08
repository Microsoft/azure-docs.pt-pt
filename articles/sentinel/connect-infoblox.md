---
title: Ligue os dados do Sistema Operativo de Identidade da Rede Infoblox (NIOS) ao Azure Sentinel| Microsoft Docs
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
ms.openlocfilehash: 28abb9f09e3bca2522b959c6a9b890de5320b17a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98567445"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Ligue o infoblox NIOS ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados Infoblox NIOS em Azure Sentinel encontra-se atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como ligar o seu [aparelho do Sistema Operativo de Identidade de Rede Infoblox (NIOS)](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) ao Azure Sentinel. O conector de dados Infoblox NIOS permite-lhe conectar facilmente os seus registos Infoblox com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. A integração entre Infoblox NIOS e Azure Sentinel faz uso do Syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Registos de Infoblox para o agente Syslog  

Configure infoblox para encaminhar mensagens Syslog para o seu espaço de trabalho Azure Sentinel através do agente Syslog.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione o conector **Infoblox NIOS.**

1. Selecione **Abrir a página do conector**.

1. Siga as instruções na página **Infoblox NIOS.**

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics no âmbito do Syslog.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics. 

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar Infoblox NIOS ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.