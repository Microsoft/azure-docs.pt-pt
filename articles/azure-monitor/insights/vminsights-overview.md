---
title: O que é Azure Monitor para VMs?
description: Visão geral do Azure Monitor para VMs, que monitoriza a saúde e desempenho dos VMs Azure e descobre automaticamente e mapeia componentes de aplicação e suas dependências.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: f9ad39b88ad2212ea2cdceb40e61fbc0a2d1a764
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320498"
---
# <a name="what-is-azure-monitor-for-vms"></a>O que é Azure Monitor para VMs?

O Azure Monitor for VMs monitoriza o desempenho e a saúde das suas máquinas virtuais e conjuntos de escala de máquinas virtuais, incluindo os seus processos de funcionamento e dependências de outros recursos. Pode ajudar a fornecer desempenho previsível e disponibilidade de aplicações vitais, identificando estrangulamentos de desempenho e problemas de rede e também pode ajudá-lo a entender se um problema está relacionado com outras dependências.

O Azure Monitor para VMs suporta sistemas operativos Windows e Linux no seguinte:

- Máquinas virtuais do Azure
- Conjuntos de dimensionamento de máquinas virtuais do Azure
- Máquinas virtuais híbridas ligadas ao Arco Azure
- Máquinas virtuais no local
- Máquinas virtuais hospedadas em outro ambiente de nuvem
  


>[!NOTE]
>Recentemente [anunciamos alterações](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) que estamos a fazer à funcionalidade Saúde com base no feedback que recebemos dos nossos clientes de pré-visualização pública. Dado o número de mudanças que vamos fazer, vamos deixar de oferecer a funcionalidade Saúde para novos clientes. Os clientes existentes podem continuar a utilizar a funcionalidade de saúde. Para mais detalhes, consulte as [nossas FAQ de Disponibilidade Geral.](vminsights-ga-release-faq.md)  


O Azure Monitor para VMs armazena os seus dados em Registos monitores Azure, o que lhe permite fornecer agregação e filtragem poderosas e analisar as tendências de dados ao longo do tempo. Pode ver estes dados num único VM a partir da máquina virtual diretamente, ou pode utilizar o Azure Monitor para fornecer uma visão agregada de vários VMs.

![Perspetiva de insights de máquina virtual no portal Azure](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Preços
Não há custos diretos para o Azure Monitor para VMs, mas é cobrado pela sua atividade no espaço de trabalho do Log Analytics. Com base nos preços publicados na página de preços do [Azure Monitor,](https://azure.microsoft.com/pricing/details/monitor/)o Azure Monitor para VMs é faturado para:

- Dados ingeridos de agentes e armazenados no espaço de trabalho.
- Regras de alerta baseadas em dados de registo e saúde.
- Notificações enviadas de regras de alerta.

O tamanho do tronco varia em função dos comprimentos de cadeia dos contadores de desempenho, e pode aumentar com o número de discos lógicos e adaptadores de rede atribuídos ao VM. Se já estiver a utilizar o Mapa de Serviços, a única alteração que verá são os dados de desempenho adicionais enviados para o tipo de dados do Azure `InsightsMetrics` Monitor.


## <a name="configuring-azure-monitor-for-vms"></a>Monitor Azure configurado para VMs
Os passos para configurar o Monitor Azure para VMs são os seguintes. Siga cada link para obter orientações detalhadas em cada passo:

- [Criar espaço de trabalho Log Analytics.](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Adicione a solução VMInsights ao espaço de trabalho.](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Instale agentes na máquina virtual e na balança de máquinas virtual definida para ser monitorizada.](vminsights-enable-overview.md)



## <a name="next-steps"></a>Passos seguintes

- Consulte [o Monitor de Azure para os VMs](vminsights-enable-overview.md) para obter requisitos e métodos que permitam monitorizar as suas máquinas virtuais.

