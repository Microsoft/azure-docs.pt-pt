---
title: Suporte remoto de trabalho Azure Firewall
description: Este artigo mostra como o Azure Firewall pode suportar os seus requisitos de força de trabalho remota.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: da5100fafc98ae38809c93e9b3db5ef41c58766a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82863421"
---
# <a name="azure-firewall-remote-work-support"></a>Suporte remoto de trabalho Azure Firewall

O Azure Firewall é um serviço de segurança de rede gerido e baseado na nuvem que protege os recursos de rede virtual Azure. É uma firewall como um serviço com monitorização de estado com alta disponibilidade integrada e escalabilidade da cloud irrestrita.

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>Suporte de implementação de infraestruturas de ambiente de trabalho virtuais (VDI)

O trabalho a partir de políticas domésticas requer que muitas organizações de TI resolvam mudanças fundamentais na capacidade, rede, segurança e governação. Os colaboradores não estão protegidos pelas políticas de segurança em camadas associadas aos serviços no local enquanto trabalham a partir de casa. As implementações de Infraestruturas de Ambiente virtual (VDI) no Azure podem ajudar as organizações a responder rapidamente a este ambiente em mudança. No entanto, você precisa de uma maneira de proteger o acesso à Internet de entrada/saída para e a partir destas implementações VDI. Pode utilizar [as regras de ADN do](rule-processing.md) Azure Firewall juntamente com as suas capacidades de filtragem baseadas em inteligência de [ameaça](threat-intel.md) para proteger as suas implementações de VDI.

## <a name="azure-windows-virtual-desktop-support"></a>Suporte a desktop virtual do Azure Windows

O Windows Virtual Desktop é um serviço abrangente de virtualização de desktop e aplicações em execução no Azure. É a única infraestrutura virtual de desktop (VDI) que fornece gestão simplificada, Windows 10 multi-sessão, otimizações para o Office 365 ProPlus e suporte para ambientes de Serviços remotos de Desktop (RDS). Pode implementar e escalar os seus desktops e aplicações do Windows em Azure em minutos e obter funcionalidades de segurança e conformidade incorporadas. O Windows Virtual Desktop não necessita de abrir qualquer acesso à sua rede virtual. No entanto, deve permitir um conjunto de ligações de rede de saída para as máquinas virtuais do Windows Desktop que funcionam na sua rede virtual. Para obter mais informações, consulte [o Azure Firewall para proteger as implementações do Ambiente de Trabalho Virtual da Janela](protect-windows-virtual-desktop.md).

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [o Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/).