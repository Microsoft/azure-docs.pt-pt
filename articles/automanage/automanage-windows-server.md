---
title: Azure Automanagem para Servidor do Windows
description: Saiba mais sobre a auto-gestão Azure para máquinas virtuais as melhores práticas para serviços que são automaticamente acamados e configurados para máquinas Windows Server.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: a3bb0a2877c71d19b05f424dc44e302c69577b2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662716"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---windows-server"></a>Azure Automanagem para as melhores práticas de máquinas virtuais - Windows Server

Estes serviços Azure são automaticamente acedidos para si quando utiliza a Automanagem para máquinas virtuais (VMs) num VM do Windows Server. São essenciais para as nossas melhores práticas, o Livro Branco, que pode encontrar no nosso [Quadro de Adoção](/azure/cloud-adoption-framework/manage/azure-server-management)em Nuvem.

Para todos estes serviços, iremos configurar automaticamente, configurar automaticamente, monitorizar a deriva e remediar se for detetada deriva. Para saber mais sobre este processo, consulte [a Azure Automanage para máquinas virtuais.](automanage-virtual-machines.md)

## <a name="supported-windows-server-versions"></a>Versões suportadas do Windows Server

A automanagem suporta as seguintes versões do Windows Server:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Edição Azure do Windows Server 2019

## <a name="participating-services"></a>Serviços participantes

|Serviço    |Description    |Ambientes Apoiados<sup>1</sup>    |Preferências suportadas<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Monitorização de Insights VM    |O Azure Monitor for VMs monitoriza o desempenho e a saúde das suas máquinas virtuais, incluindo os seus processos de funcionamento e dependências de outros recursos. Saiba [mais.](../azure-monitor/vm/vminsights-overview.md)    |Produção    |No    |
|Backup    |O Azure Backup fornece cópias de segurança independentes e isoladas para que estas protejam os dados nas suas VMs contra a destruição acidental. Saiba [mais.](../backup/backup-azure-vms-introduction.md) As cargas baseiam-se no número e no tamanho dos VMs que estão a ser protegidos. Saiba [mais.](https://azure.microsoft.com/pricing/details/backup/)    |Produção    |Yes    |
|Centro de Segurança do Azure    |O Azure Security Center é um sistema unificado de gestão de segurança de infraestruturas que fortalece a postura de segurança dos seus centros de dados e fornece uma proteção avançada de ameaças através das suas cargas de trabalho híbridas na nuvem. Saiba [mais.](../security-center/security-center-introduction.md)  A autogestão configurará a subscrição onde o seu VM reside na oferta de nível livre do Azure Security Center. Se a sua subscrição já estiver a bordo do Azure Security Center, então a Automanage não a reconfigurará.    |Produção, Dev/Teste    |No    |
|Microsoft Antimalware    |O Microsoft Antimalware for Azure é uma proteção gratuita em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares maliciosos. Gera alertas quando um software malicioso ou indesejado conhecido tenta instalar-se ou funcionar nos seus sistemas Azure. Saiba [mais.](../security/fundamentals/antimalware.md) |Produção, Dev/Teste    |Yes    |
|Gestão de Atualizações    |Pode utilizar a Gestão de Atualização na Azure Automation para gerir as atualizações do sistema operativo para as suas máquinas virtuais. Pode avaliar rapidamente o estado das atualizações disponíveis em todas as máquinas de agente e gerir o processo de instalação das atualizações necessárias para os servidores. Saiba [mais.](../automation/update-management/overview.md)    |Produção, Dev/Teste    |No    |
|Alterar o inventário de & de rastreio    |O Change Tracking and Inventory combina funções de rastreio e inventário de alterações para permitir rastrear alterações de infraestrutura de máquinas virtuais e servidores. O serviço suporta o rastreio de alterações através de serviços, software daemons, registo e ficheiros no seu ambiente para ajudá-lo a diagnosticar alterações indesejadas e aumentar alertas. O suporte ao inventário permite-lhe consultar os recursos in-guest para visibilidade em aplicações instaladas e outros itens de configuração.  Saiba [mais.](../automation/change-tracking/overview.md)    |Produção, Dev/Teste    |No    |
|Configuração de hóspedes Azure    | A política de configuração do hóspede é utilizada para monitorizar a configuração e reportar a conformidade da máquina. O serviço de auto-produção instalará as [linhas de segurança](/windows/security/threat-protection/windows-security-baselines) do Windows utilizando a extensão de Configuração do Convidado. Para as máquinas Do Windows, o serviço de configuração do hóspede reaplicará automaticamente as definições de linha de base se estiverem fora de conformidade. Saiba [mais.](../governance/policy/concepts/guest-configuration.md)    |Produção, Dev/Teste    |No    |
|Adicionar Conta de Automatização    |A Azure Automation suporta a gestão ao longo do ciclo de vida da sua infraestrutura e aplicações. Saiba [mais.](../automation/automation-intro.md)    |Produção, Dev/Teste    |No    |
|Área de trabalho do Log Analytics    |O Azure Monitor armazena dados de registo num espaço de trabalho log Analytics, que é um recurso Azure e um recipiente onde os dados são recolhidos, agregados e serve como uma fronteira administrativa. Saiba [mais.](../azure-monitor/logs/design-logs-deployment.md)    |Produção, Dev/Teste    |No    |


<sup>1</sup> A seleção do ambiente está disponível quando está a ativar a auto-produção. Saiba [mais.](automanage-virtual-machines.md#environment-configuration) Também pode ajustar as definições padrão do ambiente e definir as suas preferências dentro das melhores práticas.


## <a name="next-steps"></a>Passos seguintes

Tente ativar a auto-produção de máquinas virtuais no portal Azure.

> [!div class="nextstepaction"]
> [Ativar a auto-produção de máquinas virtuais no portal Azure](quick-create-virtual-machines-portal.md)