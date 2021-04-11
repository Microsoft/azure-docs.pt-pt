---
title: Azure Automanage para Linux
description: Saiba mais sobre a auto-produção do Azure para máquinas virtuais as melhores práticas para serviços que são automaticamente a bordo e configurados para máquinas Linux.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 3aab43be49cb98fbe136e1f0216590785d650392
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953254"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Azure Automanage para as melhores práticas de máquinas virtuais - Linux

Estes serviços Azure são automaticamente acedidos a bordo quando utiliza a Automanagem para máquinas virtuais (VMs) num LM Linux. São essenciais para as nossas melhores práticas, o Livro Branco, que pode encontrar no nosso [Quadro de Adoção](/azure/cloud-adoption-framework/manage/azure-server-management)em Nuvem.

Para todos estes serviços, iremos configurar automaticamente, configurar automaticamente, monitorizar a deriva e remediar se for detetada deriva. Para saber mais sobre este processo, consulte [a Azure Automanage para máquinas virtuais.](automanage-virtual-machines.md)

## <a name="supported-linux-distributions-and-versions"></a>Distribuição e versões apoiadas do Linux

A automanagem suporta as seguintes distribuições e versões Linux:

- CentOS 7.3+
- RHEL 7.4+
- Ubuntu 16.04 e 18.04
- SLES 12 (apenas SP3-SP5)

## <a name="participating-services"></a>Serviços participantes

>[!NOTE]
> O Microsoft Antimalware não é suportado em VMs Linux neste momento.

|Serviço    |Description    |Ambientes Apoiados<sup>1</sup>    |Preferências suportadas<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Monitorização de Insights VM    |O Azure Monitor for VMs monitoriza o desempenho e a saúde das suas máquinas virtuais, incluindo os seus processos de funcionamento e dependências de outros recursos. Saiba [mais.](../azure-monitor/vm/vminsights-overview.md)    |Produção    |No    |
|Backup    |O Azure Backup fornece cópias de segurança independentes e isoladas para que estas protejam os dados nas suas VMs contra a destruição acidental. Saiba [mais.](../backup/backup-azure-vms-introduction.md) As cargas baseiam-se no número e no tamanho dos VMs que estão a ser protegidos. Saiba [mais.](https://azure.microsoft.com/pricing/details/backup/)    |Produção    |Yes    |
|Centro de Segurança do Azure    |O Azure Security Center é um sistema unificado de gestão de segurança de infraestruturas que fortalece a postura de segurança dos seus centros de dados e fornece uma proteção avançada de ameaças através das suas cargas de trabalho híbridas na nuvem. Saiba [mais.](../security-center/security-center-introduction.md)  A autogestão configurará a subscrição onde o seu VM reside na oferta de nível livre do Azure Security Center. Se a sua subscrição já estiver a bordo do Azure Security Center, então a Automanage não a reconfigurará.    |Produção, Dev/Teste    |No    |
|Gestão de Atualizações    |Pode utilizar a Gestão de Atualização na Azure Automation para gerir as atualizações do sistema operativo para as suas máquinas virtuais. Pode avaliar rapidamente o estado das atualizações disponíveis em todas as máquinas de agente e gerir o processo de instalação das atualizações necessárias para os servidores. Saiba [mais.](../automation/update-management/overview.md)    |Produção, Dev/Teste    |No    |
|Alterar o inventário de & de rastreio    |O Change Tracking and Inventory combina funções de rastreio e inventário de alterações para permitir rastrear alterações de infraestrutura de máquinas virtuais e servidores. O serviço suporta o rastreio de alterações através de serviços, software daemons, registo e ficheiros no seu ambiente para ajudá-lo a diagnosticar alterações indesejadas e aumentar alertas. O suporte ao inventário permite-lhe consultar os recursos in-guest para visibilidade em aplicações instaladas e outros itens de configuração.  Saiba [mais.](../automation/change-tracking/overview.md)    |Produção, Dev/Teste    |No    |
|Configuração de hóspedes Azure    | A política de configuração do hóspede é utilizada para monitorizar a configuração e reportar a conformidade da máquina. O serviço de auto-produção instalará a linha de base Azure Linux utilizando a extensão de Configuração do Hóspede. Para as máquinas Linux, o serviço de configuração do hóspede instalará a linha de base apenas em modo de auditoria. Poderá ver onde o seu VM está fora do cumprimento da linha de base, mas o incumprimento não será automaticamente remediado. Saiba [mais.](../governance/policy/concepts/guest-configuration.md)    |Produção, Dev/Teste    |No    |
|Adicionar Conta de Automatização    |A Azure Automation suporta a gestão ao longo do ciclo de vida da sua infraestrutura e aplicações. Saiba [mais.](../automation/automation-intro.md)    |Produção, Dev/Teste    |No    |
|Área de trabalho do Log Analytics    |O Azure Monitor armazena dados de registo num espaço de trabalho log Analytics, que é um recurso Azure e um recipiente onde os dados são recolhidos, agregados e serve como uma fronteira administrativa. Saiba [mais.](../azure-monitor/logs/design-logs-deployment.md)    |Produção, Dev/Teste    |No    |


<sup>1</sup> A seleção do ambiente está disponível quando está a ativar a auto-produção. Saiba [mais.](automanage-virtual-machines.md#environment-configuration) Também pode ajustar as definições padrão do ambiente e definir as suas preferências dentro das melhores práticas.


## <a name="next-steps"></a>Passos seguintes

Tente ativar a auto-produção de máquinas virtuais no portal Azure.

> [!div class="nextstepaction"]
> [Ativar a auto-produção de máquinas virtuais no portal Azure](quick-create-virtual-machines-portal.md)