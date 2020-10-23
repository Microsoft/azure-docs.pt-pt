---
title: Visão geral dos servidores azure Arc
description: Saiba como usar o Azure Arc habilitado os servidores a gerir servidores hospedados fora do Azure como um recurso Azure.
keywords: automação azul, DSC, powershell, configuração de estado desejada, gestão de atualização, rastreio de alterações, inventário, runbooks, python, gráfico, híbrido
ms.date: 10/15/2020
ms.topic: overview
ms.openlocfilehash: 01de579d2e1ea84c0e9da4ceafbd33dbad4c6e27
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460857"
---
# <a name="what-is-azure-arc-enabled-servers"></a>O que são servidores preparados para o Azure Arc?

Os servidores ativados Azure Arc permitem-lhe gerir as suas máquinas Windows e Linux hospedadas fora do Azure, na sua rede corporativa ou noutro fornecedor de nuvem, semelhante à forma como gere as máquinas virtuais nativas do Azure. Quando uma máquina híbrida está ligada ao Azure, torna-se uma máquina conectada e é tratada como um recurso em Azure. Cada máquina conectada tem um ID de recurso, é gerida como parte de um grupo de recursos dentro de uma subscrição, e beneficia de construções padrão do Azure, tais como Azure Policy e aplicação de tags. Os prestadores de serviços que gerem a infraestrutura de um cliente no local podem gerir as suas máquinas híbridas, tal como fazem hoje com recursos nativos da Azure, em vários ambientes de clientes, utilizando [o Farol Azure](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) com arco Azure.

Para entregar esta experiência com as suas máquinas híbridas hospedadas fora de Azure, o agente Azure Connected Machine precisa de ser instalado em cada máquina que planeia ligar ao Azure. Este agente não fornece qualquer outra funcionalidade, e não substitui o agente Azure [Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando pretende monitorizar proactivamente o SISTEMA e as cargas de trabalho em funcionamento na máquina, geri-lo utilizando livros de automação ou soluções como a Update Management, ou utilizar outros serviços Azure como o [Azure Security Center.](../../security-center/security-center-introduction.md)

## <a name="supported-scenarios"></a>Cenários suportados

Quando liga a sua máquina a servidores ativados do Azure Arc, permite a capacidade de executar as seguintes tarefas de gestão e monitorização de configuração:

- Atribua [configurações de hóspedes da Azure Policy](../../governance/policy/concepts/guest-configuration.md) usando a mesma experiência que a atribuição de políticas para máquinas virtuais Azure. Hoje em dia, a maioria das políticas de Configuração de Hóspedes não aplica configurações, apenas auditam as definições dentro da máquina. Para compreender o custo da utilização das políticas de configuração de hóspedes da Azure Policy com servidores ativados pelo Arc, consulte o [guia de preços da](https://azure.microsoft.com/pricing/details/azure-policy/)Política Azure .

- Informe sobre alterações de configuração sobre software instalado, serviços microsoft, registo do Windows e ficheiros, e daemons Linux em servidores monitorizados utilizando o Azure Automation [Change Change Tracking and Inventory](../../automation/change-tracking/overview.md).

- Monitorize o desempenho do sistema operativo do hóspede da máquina conectada e descubra componentes de aplicação para monitorizar os seus processos e dependências com outros recursos que a aplicação comunica utilizando [o Azure Monitor para VMs](../../azure-monitor/insights/vminsights-overview.md).

- Simplificar a implementação com outros serviços Azure [Automation State Configuration](../../automation/automation-dsc-overview.md) e Azure Monitor Log Analytics utilizando as [extensões Azure VM](manage-vm-extensions.md) suportadas para as suas janelas não-Azure ou máquina Linux. Isto inclui a realização de configuração pós-implantação ou instalação de software utilizando a extensão de script personalizado.

- Utilize [a Gestão de Atualização](../../automation/update-management/update-mgmt-overview.md) na Azure Automation para gerir as atualizações do sistema operativo para os seus servidores Windows e Linux.

- Inclua os seus servidores não-Azure para deteção de ameaças e monitorize proativamente para potenciais ameaças à segurança usando [o Azure Security Center](../../security-center/security-center-introduction.md).

Registar dados recolhidos e armazenados num espaço de trabalho log Analytics da máquina híbrida agora contém propriedades específicas da máquina, como um ID de recursos. Isto pode ser usado para suportar o acesso ao registo [de contexto de recursos.](../../azure-monitor/platform/design-logs-deployment.md#access-mode)

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regiões suportadas

Para obter uma lista definitiva de regiões suportadas com servidores habilitados Azure Arc, consulte os [produtos Azure por página de região.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)

Na maioria dos casos, a localização selecionada quando cria o script de instalação deve ser a região Azure geograficamente mais próxima da localização da sua máquina. Os dados em repouso serão armazenados dentro da geografia Azure que contém a região especificada, o que também pode afetar a sua escolha de região se tiver requisitos de residência de dados. Se a região Azure a que a sua máquina está ligada for afetada por uma falha, a máquina ligada não é afetada, mas as operações de gestão utilizando o Azure podem não conseguir completar. Em caso de paragem regional, se tiver múltiplas localizações que suportam um serviço geograficamente redundante, o melhor é ligar as máquinas em cada local a uma região de Azure diferente.

### <a name="agent-status"></a>Estado do agente

O agente 'Máquina Conectada' envia uma mensagem de batimento cardíaco regular ao serviço a cada 5 minutos. Se o serviço deixar de receber estas mensagens de batimento cardíaco de uma máquina, essa máquina é considerada offline e o estado será automaticamente alterado para **Desligado** no portal dentro de 15 a 30 minutos. Ao receber uma mensagem de batimento cardíaco subsequente do agente Da Máquina Conectada, o seu estado será automaticamente alterado para **Connected**.

## <a name="next-steps"></a>Passos seguintes

Antes de avaliar ou ativar os servidores ativados pelo Arc em várias máquinas híbridas, [reveja a visão geral do agente da Máquina Conectada](agent-overview.md) para compreender os requisitos, detalhes técnicos sobre o agente e métodos de implementação.