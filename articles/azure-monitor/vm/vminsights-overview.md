---
title: O que são os conhecimentos em VM?
description: Visão geral dos conhecimentos de VM, que monitoriza a saúde e desempenho dos VMs Azure e descobre automaticamente e mapeia componentes de aplicação e suas dependências.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 18e1fdcdee347a057c452f6170f36ec7f1f43244
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046419"
---
# <a name="overview-of-vm-insights"></a>Visão geral dos insights de VM

Os conhecimentos VM monitorizam o desempenho e a saúde das suas máquinas virtuais e conjuntos de escala de máquinas virtuais, incluindo os seus processos de execução e dependências de outros recursos. Pode ajudar a fornecer desempenho previsível e disponibilidade de aplicações vitais, identificando estrangulamentos de desempenho e problemas de rede e também pode ajudá-lo a entender se um problema está relacionado com outras dependências.

Os conhecimentos VM suportam sistemas operativos Windows e Linux nas seguintes máquinas:

- Máquinas virtuais do Azure
- Conjuntos de dimensionamento de máquinas virtuais do Azure
- Máquinas virtuais híbridas ligadas ao Arco Azure
- Máquinas virtuais no local
- Máquinas virtuais hospedadas em outro ambiente de nuvem
  

Os conhecimentos VM armazenam os seus dados em Registos monitores Azure, o que lhe permite fornecer agregação e filtragem poderosas e analisar as tendências de dados ao longo do tempo. Pode ver estes dados num único VM a partir da máquina virtual diretamente, ou pode utilizar o Azure Monitor para fornecer uma visão agregada de vários VMs.

![Perspetiva de insights de máquina virtual no portal Azure](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Preços
Não há custos diretos para insights de VM, mas você é cobrado pela sua atividade no espaço de trabalho Log Analytics. Com base nos preços publicados na página de preços do [Azure Monitor,](https://azure.microsoft.com/pricing/details/monitor/)os insights de VM são faturados para:

- Dados ingeridos de agentes e armazenados no espaço de trabalho.
- Dados do estado de saúde recolhidos da saúde dos hóspedes (pré-visualização)
- Regras de alerta baseadas em dados de registo e saúde.
- Notificações enviadas de regras de alerta.

O tamanho do tronco varia em função dos comprimentos de cadeia dos contadores de desempenho, e pode aumentar com o número de discos lógicos e adaptadores de rede atribuídos ao VM. Se já estiver a utilizar o Mapa de Serviços, a única alteração que verá é os dados de desempenho extra enviados para o tipo de dados do Azure `InsightsMetrics` Monitor.


## <a name="configuring-vm-insights"></a>Ideias vm configurantes
Os passos para configurar os conhecimentos do VM são os seguintes. Siga cada link para obter orientações detalhadas em cada passo:

- [Criar espaço de trabalho Log Analytics.](./vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Adicione a solução VMInsights ao espaço de trabalho.](./vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Instale agentes na máquina virtual e na balança de máquinas virtual definida para ser monitorizada.](./vminsights-enable-overview.md)



## <a name="next-steps"></a>Passos seguintes

- Consulte [introspeções em VM sobre](./vminsights-enable-overview.md) os requisitos e métodos que permitam a monitorização das suas máquinas virtuais.
