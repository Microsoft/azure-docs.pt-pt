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
ms.openlocfilehash: 32023fca15de9d3f9258bc05166c3cff4184ea15
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499854"
---
# <a name="faq---questions-about-virtual-machines"></a>FAQ - Perguntas sobre máquinas virtuais


## <a name="what-types-of-virtual-machines-are-supported"></a>Que tipos de máquinas virtuais são suportadas?

A monitorização e recomendações estão disponíveis para máquinas virtuais (VMs) criadas utilizando os [modelos clássicos e de implementação do Gestor de Recursos.](../azure-resource-manager/management/deployment-models.md)

Consulte [as plataformas apoiadas no Centro de Segurança Azure](security-center-os-coverage.md) para obter uma lista de plataformas apoiadas.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Porque é que o Azure Security Center não reconhece a solução antimalware que funciona no meu Azure VM?

O Azure Security Center tem visibilidade em antimalware instalado através de extensões Azure. Por exemplo, o Security Center não é capaz de detetar antimalware que foi pré-instalado numa imagem que forneceu ou se instalou antimalware nas suas máquinas virtuais utilizando os seus próprios processos (como sistemas de gestão de configuração).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Por que recebo a mensagem "Dados de Scan Desaparecidos" para o meu VM?

Esta mensagem é apresentada quando não existem dados de análise para uma VM. Pode demorar algum tempo (menos de uma hora) para que os dados de análise sejam preenchidos após a ativação da Recolha de Dados no Centro de Segurança do Azure. Após o preenchimento inicial dos dados de análise, pode receber esta mensagem pois não existem dados de análise ou não existem dados de análise recentes. As análises não preenchem uma VM no estado parado. Esta mensagem também pode aparecer se os dados de digitalização não aparecerem recentemente (de acordo com a política de retenção para o agente Windows, que tem um valor padrão de 30 dias).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Com que frequência o Security Center verifica as vulnerabilidades do sistema operativo, as atualizações do sistema e os problemas de proteção de pontos finais?

Abaixo estão os tempos de latência para o Centro de Segurança de verificações de vulnerabilidades, atualizações e problemas:

- Configurações de segurança do sistema operativo – os dados são atualizados dentro de 48 horas
- Atualizações do sistema – os dados são atualizados dentro de 24 horas
- Problemas de Proteção de Pontos Finais – os dados são atualizados dentro de 8 horas

O Centro de Segurança normalmente analisa novos dados a cada hora, e atualiza as recomendações em conformidade. 

> [!NOTE]
> O Security Center utiliza o agente Log Analytics para recolher e armazenar dados. Para saber mais, consulte [a Plataforma de Migração do Centro de Segurança Azure.](./security-center-enable-data-collection.md)


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Por que recebo a mensagem "Agente VM está desaparecido?"

O Agente VM deve ser instalado em VMs para permitir a recolha de dados. O Agente de VM é instalado por predefinição em VMs que são implementadas a partir do Azure Marketplace. Para obter informações sobre como instalar o Agente VM em outros VMs, consulte o blog post [VM Agent and Extensions](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).