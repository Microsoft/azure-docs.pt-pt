---
title: O que é o Monitor Azure para VMs?
description: Visão geral do Monitor Azure para VMs que monitoriza a saúde e desempenho dos VMs Azure, além de descobrir e mapear automaticamente componentes de aplicações e suas dependências.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480492"
---
# <a name="what-is-azure-monitor-for-vms"></a>O que é o Monitor Azure para VMs?

O Monitor Azure para VMs monitoriza as suas máquinas virtuais Azure (VM) e conjuntos de escala de máquinas virtuais à escala. Analisa o desempenho e o estado de funcionamento das suas VMs do Windows e do Linux e monitoriza os respetivos processos e dependências noutros recursos e processos externos. Inclui suporte para monitorizar o desempenho e dependências de aplicações para VMs que estão hospedados no local ou em outro fornecedor de nuvem. As seguintes funcionalidades-chave fornecem uma visão aprofundada:

- **Gráficos de desempenho de tendência pré-definidos**: Mostrar métricas de desempenho core do sistema operativo VM convidado.

- **Mapa de dependência**: Exibe os componentes interligados com o VM de vários grupos de recursos e subscrições.  

>[!NOTE]
>Anunciámos recentemente [alterações](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) à funcionalidade Saúde com base no feedback que recebemos dos nossos clientes de pré-visualização pública. Dado o número de alterações que vamos fazer, vamos deixar de oferecer a funcionalidade Saúde a novos clientes. Os clientes existentes podem continuar a utilizar a funcionalidade de saúde. Para mais detalhes, consulte as nossas [FAQ](vminsights-ga-release-faq.md)de Disponibilidade Geral .  

A integração com o Azure Monitor Logs proporciona uma agregação e filtragem poderosas, permitindo que o Monitor Azure para VMs analise as tendências de dados ao longo do tempo. Pode visualizar estes dados num único VM da máquina virtual diretamente, ou pode utilizar o Monitor Azure para fornecer uma visão agregada dos seus VMs onde a vista suporta modos de contexto de recursos Azure ou de contexto de espaço de trabalho. Para mais informações, consulte a [visão geral dos modos](../platform/design-logs-deployment.md#access-mode)de acesso .

![Perspetiva de insights de máquina virtual no portal Azure](media/vminsights-overview/vminsights-azmon-directvm.png)

O Monitor Azure para VMs pode fornecer desempenho previsível e disponibilidade de aplicações vitais. Identifica estrangulamentos de desempenho e problemas de rede e também pode ajudá-lo a entender se um problema está relacionado com outras dependências.  

## <a name="data-usage"></a>Utilização de dados

Quando implementa o Monitor Azure para VMs, os dados recolhidos pelos seus VMs são ingeridos e armazenados no Monitor Azure. Os dados de desempenho e dependência recolhidos são armazenados num espaço de trabalho do Log Analytics. Com base nos preços publicados na página de preços do [Monitor Do Azure,](https://azure.microsoft.com/pricing/details/monitor/)o Monitor Azure para VMs é cobrado para:

- Os dados que são ingeridos e armazenados.
- As regras de alerta que são criadas.
- As notificações que são enviadas. 

O tamanho do registo varia pelos comprimentos das cordas dos contadores de desempenho, e pode aumentar com o número de discos lógicos e adaptadores de rede atribuídos ao VM. Se já tem um espaço de trabalho e está a recolher estes balcões, não são aplicadas taxas duplicadas. Se já está a utilizar o Service Map, a única alteração que verá são os dados adicionais de ligação que são enviados para o Monitor Azure.

## <a name="next-steps"></a>Passos seguintes

Para compreender os requisitos e métodos que o ajudam a monitorizar as suas máquinas virtuais, reveja o [Monitor De implantação de Dispositivos Azure para VMs](vminsights-enable-overview.md).
