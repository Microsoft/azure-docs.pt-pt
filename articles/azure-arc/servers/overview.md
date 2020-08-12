---
title: Azure Arc para servidores (pré-visualização) Visão geral
description: Aprenda a usar o Azure Arc para os servidores gerirem máquinas que estão hospedadas fora do Azure como se fosse um recurso Azure.
keywords: automação azul, DSC, powershell, configuração de estado desejada, gestão de atualização, rastreio de alterações, inventário, runbooks, python, gráfico, híbrido
ms.custom: references_regions
ms.date: 08/06/2020
ms.topic: overview
ms.openlocfilehash: f11eedaf5f70cb24fa6c1588b7f26b2eed4734ce
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121804"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>O que é Azure Arc para servidores (pré-visualização)?

O Azure Arc para servidores (pré-visualização) permite-lhe gerir as suas máquinas Windows e Linux hospedadas fora do Azure na sua rede corporativa ou noutro fornecedor de nuvem, à semelhança da forma como gere as máquinas virtuais nativas do Azure. Quando uma máquina híbrida está ligada ao Azure, torna-se uma máquina conectada e é tratada como um recurso em Azure. Cada máquina conectada tem um ID de recurso, é gerida como parte de um grupo de recursos dentro de uma subscrição, e beneficia de construções padrão do Azure, tais como Azure Policy e aplicação de tags.

Para entregar esta experiência com as suas máquinas híbridas hospedadas fora de Azure, o agente Azure Connected Machine precisa de ser instalado em cada máquina que planeia ligar ao Azure. Este agente não fornece qualquer outra funcionalidade, e não substitui o agente Azure [Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando pretende monitorizar proactivamente o SISTEMA e as cargas de trabalho em funcionamento na máquina, geri-lo utilizando livros de automação ou soluções como a Update Management, ou utilizar outros serviços Azure como o [Azure Security Center.](../../security-center/security-center-intro.md)

>[!NOTE]
>Esta versão de pré-visualização destina-se a fins de avaliação e recomendamos que não gere máquinas de produção críticas.
>

## <a name="supported-scenarios"></a>Cenários suportados

Quando liga a sua máquina ao Azure Arc para servidores (pré-visualização), permite a capacidade de executar as seguintes tarefas de gestão de configuração:

- Atribua [configurações de hóspedes da Azure Policy](../../governance/policy/concepts/guest-configuration.md) usando a mesma experiência que a atribuição de políticas para máquinas virtuais Azure.

- Monitorize o desempenho do sistema operativo do hóspede da máquina conectada e descubra componentes de aplicação para monitorizar os seus processos e dependências com outros recursos que a aplicação comunica utilizando [o Azure Monitor para VMs](../../azure-monitor/insights/vminsights-overview.md).

- Simplificar a implementação com outros serviços Azure Automation State Configuration e Azure Monitor Log Analytics utilizando as [extensões Azure VM](manage-vm-extensions.md) suportadas para as suas máquinas não-Azure Windows ou Linux. Isto inclui a realização de configuração pós-implantação ou instalação de software utilizando a extensão de script personalizado.

Registar dados recolhidos e armazenados num espaço de trabalho log Analytics da máquina híbrida agora contém propriedades específicas da máquina, como um ID de recursos. Isto pode ser usado para suportar o acesso ao registo [de contexto de recursos.](../../azure-monitor/platform/design-logs-deployment.md#access-mode)

## <a name="supported-regions"></a>Regiões suportadas

Com Azure Arc para servidores (pré-visualização), apenas certas regiões são suportadas:

- Rio Eastus
- Westus2
- Europa Ocidental
- Sudeste da Ásia

Na maioria dos casos, a localização selecionada quando cria o script de instalação deve ser a região Azure geograficamente mais próxima da localização da sua máquina. Os dados em repouso serão armazenados dentro da geografia Azure que contém a região especificada, o que também pode afetar a sua escolha de região se tiver requisitos de residência de dados. Se a região Azure a que a sua máquina está ligada for afetada por uma falha, a máquina ligada não é afetada, mas as operações de gestão utilizando o Azure podem não conseguir completar. Em caso de paragem regional, se tiver múltiplas localizações que fornecem um serviço geograficamente redundante, o melhor é ligar as máquinas em cada local a uma região de Azure diferente.

### <a name="agent-status"></a>Estado do agente

O agente 'Máquina Conectada' envia uma mensagem de batimento cardíaco regular ao serviço a cada 5 minutos. Se o serviço deixar de receber estas mensagens de batimento cardíaco de uma máquina, essa máquina é considerada offline e o estado será automaticamente alterado para **Desligado** no portal dentro de 15 a 30 minutos. Ao receber uma mensagem de batimento cardíaco subsequente do agente Da Máquina Conectada, o seu estado será automaticamente alterado para **Connected**.

## <a name="next-steps"></a>Passos seguintes

Antes de avaliar ou permitir que o Arc para servidores (pré-visualização) em várias máquinas híbridas, reveja o artigo de visão geral do [agente da Máquina Conectada](agent-overview.md) para entender o que é necessário, detalhes técnicos sobre o agente e métodos de implementação.
