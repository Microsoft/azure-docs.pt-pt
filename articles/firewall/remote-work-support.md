---
title: Suporte de trabalho remoto Azure Firewall
description: Este artigo mostra como o Azure Firewall pode suportar os seus requisitos de força de trabalho remota.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: da5100fafc98ae38809c93e9b3db5ef41c58766a
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82863421"
---
# <a name="azure-firewall-remote-work-support"></a>Suporte de trabalho remoto Azure Firewall

O Azure Firewall é um serviço de segurança de rede gerido e baseado na nuvem que protege os seus recursos de rede virtual Azure. É uma firewall como um serviço com monitorização de estado com alta disponibilidade integrada e escalabilidade da cloud irrestrita.

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>Suporte de implementação de infraestrutura de ambiente de trabalho virtual (VDI)

O trabalho a partir de políticas domésticas requer que muitas organizações de TI abordem mudanças fundamentais na capacidade, na rede, na segurança e na governação. Os funcionários não estão protegidos pelas políticas de segurança associadas aos serviços no local enquanto trabalham a partir de casa. As implementações de infraestruturas de ambiente de trabalho virtuais (VDI) no Azure podem ajudar as organizações a responder rapidamente a este ambiente em mudança. No entanto, precisa de uma forma de proteger o acesso à Internet de entrada/saída de e para estas implementações vDI. Pode utilizar as [regras de ADN](rule-processing.md) do Azure Firewall juntamente com as suas capacidades de filtragem baseadas em inteligência de [ameaça](threat-intel.md) para proteger as suas implementações vDI.

## <a name="azure-windows-virtual-desktop-support"></a>Suporte para desktop virtual do Windows Azure

O Windows Virtual Desktop é um serviço abrangente de virtualização de computadores e aplicações em funcionamento no Azure. É a única infraestrutura virtual de desktop (VDI) que oferece gestão simplificada, multi-sessões Windows 10, otimizações para o Office 365 ProPlus e suporte para ambientes de Serviços de Desktop Remoto (RDS). Pode implementar e dimensionar os seus desktops e aplicações do Windows no Azure em minutos e obter funcionalidades de segurança e conformidade incorporadas. O Windows Virtual Desktop não requer que abra qualquer acesso de entrada à sua rede virtual. No entanto, deve permitir um conjunto de ligações de rede de saída para as máquinas virtuais do Windows Virtual Desktop que funcionam na sua rede virtual. Para mais informações, consulte utilize firewall [Azure para proteger as implementações](protect-windows-virtual-desktop.md)de ambiente de trabalho virtual da janela .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/).