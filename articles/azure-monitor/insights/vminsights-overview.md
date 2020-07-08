---
title: O que é Azure Monitor para VMs?
description: Visão geral do Azure Monitor para VMs que monitoriza a saúde e desempenho dos VMs Azure, além de descobrir e mapear automaticamente componentes de aplicações e suas dependências.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: f058ce1ae5b7328c6864684994a74f2fd118ca6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506995"
---
# <a name="what-is-azure-monitor-for-vms"></a>O que é Azure Monitor para VMs?

O Azure Monitor para VMs monitoriza as suas máquinas virtuais, conjuntos de balança de máquinas virtuais e máquinas Azure Arc em escala. Analisa o desempenho e o estado de funcionamento das suas VMs do Windows e do Linux e monitoriza os respetivos processos e dependências noutros recursos e processos externos. Inclui suporte para monitorizar o desempenho e as dependências da aplicação para VMs alojadas no local ou noutro fornecedor de cloud. As seguintes características-chave fornecem uma visão aprofundada:

- **Gráficos de desempenho de tendência pré-definidos**: Mostrar métricas de desempenho do núcleo do sistema operativo VM convidado.

- **Mapa de dependência**: Exibe os componentes interligados com o VM de vários grupos de recursos e subscrições.  

>[!NOTE]
>Recentemente [anunciamos alterações](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) que estamos a fazer à funcionalidade Saúde com base no feedback que recebemos dos nossos clientes de pré-visualização pública. Dado o número de mudanças que vamos fazer, vamos deixar de oferecer a funcionalidade Saúde para novos clientes. Os clientes existentes podem continuar a utilizar a funcionalidade de saúde. Para mais detalhes, consulte as [nossas FAQ de Disponibilidade Geral.](vminsights-ga-release-faq.md)  

A integração com os Registos do Monitor Azure proporciona uma agregação e filtragem poderosas, permitindo ao Azure Monitor para VMs analisar as tendências dos dados ao longo do tempo. Pode ver estes dados num único VM a partir da máquina virtual diretamente, ou pode utilizar o Azure Monitor para fornecer uma visão agregada dos seus VMs onde a vista suporta o contexto de recursos Azure ou modos de contexto de espaço de trabalho. Para obter mais informações, consulte [a visão geral dos modos de acesso.](../platform/design-logs-deployment.md#access-mode)

![Perspetiva de insights de máquina virtual no portal Azure](media/vminsights-overview/vminsights-azmon-directvm.png)

O Azure Monitor para VMs pode fornecer desempenho previsível e disponibilidade de aplicações vitais. Identifica estrangulamentos de desempenho e problemas de rede e também pode ajudá-lo a entender se um problema está relacionado com outras dependências.  

## <a name="data-usage"></a>Utilização de dados

Quando implementa o Azure Monitor para VMs, os dados recolhidos pelos seus VMs são ingeridos e armazenados no Azure Monitor. Os dados de desempenho e dependência recolhidos são armazenados num espaço de trabalho log analytics. Com base nos preços publicados na página de preços do [Azure Monitor,](https://azure.microsoft.com/pricing/details/monitor/)o Azure Monitor para VMs é faturado para:

- Os dados que são ingeridos e armazenados.
- As regras de alerta que são criadas.
- As notificações que são enviadas. 

O tamanho do tronco varia em função dos comprimentos de cadeia dos contadores de desempenho, e pode aumentar com o número de discos lógicos e adaptadores de rede atribuídos ao VM. Se já tem um espaço de trabalho e está a recolher estes balcões, não são aplicadas cobranças duplicadas. Se já estiver a utilizar o Mapa de Serviços, a única alteração que verá são os dados adicionais de ligação enviados ao Azure Monitor.

## <a name="next-steps"></a>Próximos passos

Para compreender os requisitos e métodos que o ajudam a monitorizar as suas máquinas virtuais, [reveja o Monitor de Azure para VMs](vminsights-enable-overview.md).
