---
title: O que é o Azure Monitor para VMs?
description: Visão geral do Azure Monitor para VMs, que monitoriza a saúde e desempenho dos VMs Azure e descobre automaticamente e mapeia componentes de aplicação e suas dependências.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 796f77a860f0c3303d85aaaae290225841da1b89
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619681"
---
# <a name="overview-of-azure-monitor-for-vms"></a>Descrição geral do Azure Monitor para VMs

O Azure Monitor for VMs monitoriza o desempenho e a saúde das suas máquinas virtuais e conjuntos de escala de máquinas virtuais, incluindo os seus processos de funcionamento e dependências de outros recursos. Pode ajudar a fornecer desempenho previsível e disponibilidade de aplicações vitais, identificando estrangulamentos de desempenho e problemas de rede e também pode ajudá-lo a entender se um problema está relacionado com outras dependências.

O Azure Monitor para VMs suporta sistemas operativos Windows e Linux no seguinte:

- Máquinas virtuais do Azure
- Conjuntos de dimensionamento de máquinas virtuais do Azure
- Máquinas virtuais híbridas ligadas ao Arco Azure
- Máquinas virtuais no local
- Máquinas virtuais hospedadas em outro ambiente de nuvem
  

O Azure Monitor para VMs armazena os seus dados em Registos monitores Azure, o que lhe permite fornecer agregação e filtragem poderosas e analisar as tendências de dados ao longo do tempo. Pode ver estes dados num único VM a partir da máquina virtual diretamente, ou pode utilizar o Azure Monitor para fornecer uma visão agregada de vários VMs.

![Perspetiva de insights de máquina virtual no portal Azure](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Preços
Não há custos diretos para o Azure Monitor para VMs, mas é cobrado pela sua atividade no espaço de trabalho do Log Analytics. Com base nos preços publicados na página de preços do [Azure Monitor,](https://azure.microsoft.com/pricing/details/monitor/)o Azure Monitor para VMs é faturado para:

- Dados ingeridos de agentes e armazenados no espaço de trabalho.
- Dados do estado de saúde recolhidos da saúde dos hóspedes (pré-visualização)
- Regras de alerta baseadas em dados de registo e saúde.
- Notificações enviadas de regras de alerta.

O tamanho do tronco varia em função dos comprimentos de cadeia dos contadores de desempenho, e pode aumentar com o número de discos lógicos e adaptadores de rede atribuídos ao VM. Se já estiver a utilizar o Mapa de Serviços, a única alteração que verá são os dados de desempenho adicionais enviados para o tipo de dados do Azure `InsightsMetrics` Monitor.


## <a name="configuring-azure-monitor-for-vms"></a>Monitor Azure configurado para VMs
Os passos para configurar o Monitor Azure para VMs são os seguintes. Siga cada link para obter orientações detalhadas em cada passo:

- [Criar espaço de trabalho Log Analytics.](../insights/vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Adicione a solução VMInsights ao espaço de trabalho.](../insights/vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Instale agentes na máquina virtual e na balança de máquinas virtual definida para ser monitorizada.](../insights/vminsights-enable-overview.md)



## <a name="next-steps"></a>Passos seguintes

- Consulte [o Monitor de Azure para os VMs](../insights/vminsights-enable-overview.md) para obter requisitos e métodos que permitam monitorizar as suas máquinas virtuais.

