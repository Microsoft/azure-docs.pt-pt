---
title: Azure Automanage para máquinas virtuais melhores práticas
description: Conheça a auto-produção do Azure para as melhores práticas de máquinas virtuais para serviços que são automaticamente a bordo e configurados para si.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: dce076da08a7c31d7e2637dd5b8b29c9202ea10e
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206412"
---
# <a name="azure-automanage-for-virtual-machines-best-practices"></a>Azure Automanage para as melhores práticas de máquinas virtuais


Estes serviços Azure são automaticamente acedidos para si quando utiliza a Automanagem para máquinas virtuais. São essenciais para as nossas melhores práticas, o Livro Branco, que pode encontrar no nosso [Quadro de Adoção](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/azure-server-management)em Nuvem.

Para todos estes serviços, iremos automaticamente embarcar, configurar automaticamente, monitorizar a deriva e mediar se for detetada deriva. Para saber mais sobre este processo, consulte [a Azure Automanage para máquinas virtuais.](automanage-virtual-machines.md)


## <a name="participating-services"></a>Serviços participantes

|Serviço    |Description    |Perfis Suportados<sup>1</sup>    |Preferências suportadas<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Monitorização de Insights VM    |O Azure Monitor for VMs monitoriza o desempenho e a saúde das suas máquinas virtuais, incluindo os seus processos de funcionamento e dependências de outros recursos. Saiba [mais.](../azure-monitor/insights/vminsights-overview.md)    |Azure VM Melhores Práticas – Produção    |No    |
|Cópia de segurança    |O Azure Backup fornece cópias de segurança independentes e isoladas para que estas protejam os dados nas suas VMs contra a destruição acidental. Saiba [mais.](../backup/backup-azure-vms-introduction.md) As cargas baseiam-se no número e no tamanho dos VMs que estão a ser protegidos. Saiba [mais.](https://azure.microsoft.com/pricing/details/backup/)    |Azure VM Melhores Práticas – Produção    |Yes    |
|Centro de Segurança do Azure    |O Azure Security Center é um sistema unificado de gestão de segurança de infraestruturas que fortalece a postura de segurança dos seus centros de dados e fornece uma proteção avançada de ameaças através das suas cargas de trabalho híbridas na nuvem. Saiba [mais.](../security-center/security-center-intro.md)  A autogestão configurará a subscrição onde o seu VM reside na oferta de nível livre do Azure Security Center. Se a sua subscrição já estiver a bordo do Azure Security Center, então a gestão automática não a reconfigurará.    |Azure VM Melhores Práticas – Produção, Azure VM Melhores Práticas – Dev/Teste    |No    |
|Microsoft Antimalware    |O Microsoft Antimalware for Azure é uma proteção gratuita em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares maliciosos. Gera alertas quando um software malicioso ou indesejado conhecido tenta instalar-se ou funcionar nos seus sistemas Azure. Saiba [mais.](../security/fundamentals/antimalware.md) |Azure VM Melhores Práticas – Produção, Azure VM Melhores Práticas – Dev/Teste    |Yes    |
|Gestão de Atualizações    |Pode utilizar a Gestão de Atualização na Azure Automation para gerir as atualizações do sistema operativo para as suas máquinas virtuais. Pode avaliar rapidamente o estado das atualizações disponíveis em todas as máquinas de agente e gerir o processo de instalação das atualizações necessárias para os servidores. Saiba [mais.](../automation/update-management/update-mgmt-overview.md)    |Azure VM Melhores Práticas – Produção, Azure VM Melhores Práticas – Dev/Teste    |No    |
|Alterar o inventário de & de rastreio    |O Change Tracking and Inventory combina funções de rastreio e inventário de alterações para permitir rastrear alterações de infraestrutura de máquinas virtuais e servidores. O serviço suporta o rastreio de alterações através de serviços, software daemons, registo e ficheiros no seu ambiente para ajudá-lo a diagnosticar alterações indesejadas e aumentar alertas. O suporte ao inventário permite-lhe consultar os recursos in-guest para visibilidade em aplicações instaladas e outros itens de configuração.  Saiba [mais.](../automation/change-tracking/overview.md)    |Azure VM Melhores Práticas – Produção, Azure VM Melhores Práticas – Dev/Teste    |No    |
|Adicionar Conta de Automatização    |A Azure Automation suporta a gestão ao longo do ciclo de vida da sua infraestrutura e aplicações. Saiba [mais.](../automation/automation-intro.md)    |Azure VM Melhores Práticas – Produção, Azure VM Melhores Práticas – Dev/Teste    |No    |
|Área de trabalho do Log Analytics    |O Azure Monitor armazena dados de registo num espaço de trabalho log Analytics, que é um recurso Azure e um recipiente onde os dados são recolhidos, agregados e serve como uma fronteira administrativa. Saiba [mais.](../azure-monitor/platform/design-logs-deployment.md)    |Azure VM Melhores Práticas – Produção, Azure VM Melhores Práticas – Dev/Teste    |No    |


<sup>1 Os</sup> perfis de configuração estão disponíveis quando está a ativar a auto-produção. Saiba [mais.](automanage-virtual-machines.md#configuration-profiles) Também pode ajustar as definições padrão do perfil de configuração e definir as suas preferências dentro das melhores práticas.


## <a name="next-steps"></a>Passos seguintes

Tente ativar a auto-produção de máquinas virtuais no portal Azure.

> [!div class="nextstepaction"]
> [Ativar a auto-produção de máquinas virtuais no portal Azure](quick-create-virtual-machines-portal.md)