---
title: O que é o Azure Monitor para VMs (pré-visualização)? | Microsoft Docs
description: Monitor do Azure para VMs é um recurso do Azure Monitor, que combina o estado de funcionamento e desempenho de monitorização do sistema de operacional de VM do Azure, bem como detetar automaticamente os componentes da aplicação e dependências com outros recursos e mapeia a comunicação entre eles. Este artigo fornece uma visão geral.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: f7a0300619d82f760c0e307601efbd3987eb6067
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004587"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>O que é o Azure Monitor para VMs (pré-visualização)?

Monitor do Azure para VMs monitoriza máquinas de virtuais do Azure (VM) e conjuntos de dimensionamento de máquinas virtuais em escala. Ele analisa o desempenho e estado de funcionamento das suas VMs de Linux e Windows e monitoriza os seus processos e as dependências de outros recursos e processos externos. 

Como uma solução, o Azure Monitor para VMs inclui suporte para as dependências de desempenho e a aplicação de monitorização para as VMs que estão alojados no local ou em outro fornecedor de cloud. Três principais recursos fornecem informações detalhadas:

* **Componentes lógicos de VMs do Azure que executam o Windows e Linux**: São medidas em relação a critérios de estado de funcionamento pré-configuradas, e eles alertá-lo quando for cumprida a condição avaliada.  

* **Gráficos de desempenho populares predefinidas**: Apresentar as métricas de desempenho do núcleo do sistema de operativo da VM de convidado.

* **Mapa de dependência**: Mostra os componentes interconectados com a VM a partir de vários grupos de recursos e subscrições.  

As funcionalidades estão organizadas nas três perspectivas:

* Estado de Funcionamento
* Desempenho
* Mapa

>[!NOTE]
>Atualmente, a funcionalidade de estado de funcionamento é oferecida apenas para máquinas virtuais do Azure e o dimensionamento de máquinas virtuais conjuntos. Funcionalidades de mapa de desempenho e suportam VMs do Azure e máquinas virtuais que estão alojadas no seu ambiente ou de outro fornecedor de cloud.

Integração com os registos do Azure Monitor oferece poderosas agregação e a filtragem e ele pode analisar as tendências de dados ao longo do tempo. Essa carga de trabalho abrangente de monitorização não pode ser obtido com o Azure Monitor ou o mapa de serviço autónomo.  

Pode ver estes dados numa única VM da máquina virtual diretamente ou pode utilizar o Azure Monitor para fornecer uma exibição agregada das suas VMs. Esta vista é baseada no ponto de vista de cada funcionalidade:

* **Estado de funcionamento**: As VMs estão relacionados com um grupo de recursos.
* **Mapa** e **desempenho**: As VMs são configuradas para reportar a uma área de trabalho do Log Analytics específica.

![Perspetiva de informações da máquina virtual no portal do Azure](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

O Azure Monitor para as VMs pode fornecer um desempenho previsível e a disponibilidade de aplicações vitais. Identifica os eventos de sistema operativo críticas, afunilamentos de desempenho e problemas de rede. Monitor do Azure para VMs também pode ajudar a compreender se um problema está relacionado com outras dependências.  

## <a name="data-usage"></a>Utilização de dados 

Ao implementar o Azure Monitor para as VMs, os dados recolhidos pelas suas VMs são ingeridos e armazenados no Azure Monitor. Métricas de critérios de estado de funcionamento são armazenadas no Azure Monitor num banco de dados de séries de tempo, os dados de desempenho e dependência recolhidos são armazenados numa área de trabalho do Log Analytics. Com base nos preços publicados na [página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), do Azure Monitor para VMs é cobrado por:

* Os dados ingeridos e armazenados.
* O número de métrica de critérios de estado de funcionamento séries de tempo que são monitorizados.
* As regras de alerta que são criadas.
* As notificações que são enviadas. 

O tamanho do registo variam de acordo com os comprimentos de cadeia de caracteres de contadores de desempenho e pode ser aumentado com o número de discos lógicos e adaptadores de rede atribuídos à VM. Se já tiver uma área de trabalho e está a recolher estes contadores, é aplicada uma cobrança duplicada. Se já estiver a utilizar o mapa de serviço, a única alteração que verá é os dados de ligação adicional que são enviados para o Azure Monitor.

## <a name="next-steps"></a>Passos Seguintes
Para compreender os requisitos e métodos que o ajudam a monitorizar as máquinas virtuais, consulte [implementar o Azure Monitor para VMs](vminsights-onboard.md).
