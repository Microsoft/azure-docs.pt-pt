---
title: Azure Security Center FAQ - perguntas sobre máquinas virtuais
description: Perguntas frequentes sobre máquinas virtuais no Azure Security Center, um produto que o ajuda a prevenir, detetar e responder a ameaças
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: d84085115816a8fe1cba65e191ea391dd91a4aed
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599369"
---
# <a name="faq---questions-about-virtual-machines"></a>FAQ - Perguntas sobre máquinas virtuais


## <a name="what-types-of-virtual-machines-are-supported"></a>Que tipos de máquinas virtuais são suportados?

Estão disponíveis monitorizações e recomendações para máquinas virtuais (VMs) criadas utilizando os [modelos clássicos e](../azure-classic-rm.md)de implantação do Gestor de Recursos.

Consulte [plataformas suportadas no Azure Security Center](security-center-os-coverage.md) para obter uma lista de plataformas suportadas.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Por que o Centro de segurança do Azure não reconhece a solução de antimalware em execução na minha VM do Azure?

Centro de segurança do Azure tem visibilidade para antimalware instalado através de extensões do Azure. Por exemplo, o Centro de segurança não é capaz de detetar antimalware que foi pré-instalado numa imagem fornecida ou se tiver instalado antimalware nas suas máquinas virtuais através de seus próprios processos (por exemplo, sistemas de gestão de configuração).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Por que motivo recebo a mensagem "Dados de análise em falta" para a minha VM?

Esta mensagem é apresentada quando não existem dados de análise para uma VM. Pode demorar algum tempo (menos de uma hora) para que os dados de análise sejam preenchidos após a ativação da Recolha de Dados no Centro de Segurança do Azure. Após o preenchimento inicial dos dados de análise, pode receber esta mensagem pois não existem dados de análise ou não existem dados de análise recentes. As análises não preenchem uma VM no estado parado. Esta mensagem também pode aparecer se os dados de análise não tiverem sido povoadas recentemente (em conformidade com a política de retenção para o agente do Windows, que tem um valor predefinido de 30 dias).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>A frequência com que o Centro de segurança analisar para vulnerabilidades do sistema operativo, atualizações do sistema e problemas do endpoint protection?

Abaixo estão os tempos de latência para o Centro de Segurança de verificações de vulnerabilidades, atualizações e problemas:

- Configurações de segurança do sistema operacional – dados é atualizado no prazo de 48 horas
- Atualizações do sistema – dados é atualizada dentro de 24 horas
- Problemas de proteção de ponto de extremidade – dados serão atualizados dentro de 8 horas

Normalmente, o Centro de segurança verifica a existência de novos dados a cada hora e atualiza as recomendações em conformidade. 

> [!NOTE]
> O Security Center utiliza o Agente de Monitorização da Microsoft para recolher e armazenar dados. Para saber mais, consulte a Migração da [Plataforma azure Security Center.](security-center-platform-migration.md)


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Por que motivo recebo a mensagem "O agente da VM está em falta?"

O agente da VM tem de ser instalado em VMs para ativar a recolha de dados. O Agente de VM é instalado por predefinição em VMs que são implementadas a partir do Azure Marketplace. Para obter informações sobre como instalar o Agente VM em outros VMs, consulte o blog post [VM Agent and Extensions](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).