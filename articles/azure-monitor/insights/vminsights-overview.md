---
title: O que é o Azure Monitor para VMs (pré-visualização)? | Microsoft Docs
description: Visão geral do Monitor Azure para VMs que monitoriza a saúde e desempenho dos VMs Azure, além de descobrir e mapear automaticamente componentes de aplicações e suas dependências.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 1dcce3ab9f975fcf5910c382df3489d5d4ed425a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670671"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>O que é o Azure Monitor para VMs (pré-visualização)?

Monitor do Azure para VMs monitoriza máquinas de virtuais do Azure (VM) e conjuntos de dimensionamento de máquinas virtuais em escala. Analisa o desempenho e o estado de funcionamento das suas VMs do Windows e do Linux e monitoriza os respetivos processos e dependências noutros recursos e processos externos. 

Inclui suporte para monitorizar o desempenho e dependências de aplicações para VMs que estão hospedados no local ou em outro fornecedor de nuvem. As seguintes funcionalidades-chave fornecem uma visão aprofundada:

- **Gráficos de desempenho de tendência pré-definidos**: Mostrar métricas de desempenho core do sistema operativo VM convidado.

- **Mapa de dependência**: Exibe os componentes interligados com o VM de vários grupos de recursos e subscrições.  

>[!NOTE]
>Anunciámos recentemente [alterações](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) à funcionalidade Saúde com base no feedback que recebemos dos nossos clientes de pré-visualização pública. Dado o número de alterações que vamos fazer, vamos deixar de oferecer a funcionalidade Saúde a novos clientes. Os clientes existentes podem continuar a utilizar a funcionalidade de saúde. Para mais detalhes, consulte as nossas [FAQ](vminsights-ga-release-faq.md)de Disponibilidade Geral .  

A integração com os registos do Monitor Azure proporciona uma agregação e filtragem poderosas, e pode analisar as tendências dos dados ao longo do tempo. Tal monitorização abrangente da carga de trabalho não pode ser alcançada apenas com o Monitor Azure ou o Mapa de Serviços.  

Pode visualizar estes dados num único VM da máquina virtual diretamente, ou pode utilizar o Monitor Azure para fornecer uma visão agregada dos seus VMs onde a vista suporta modos de contexto de recursos Azure ou de contexto de espaço de trabalho. Para mais informações, consulte a [visão geral dos modos](../platform/design-logs-deployment.md#access-mode)de acesso .

![Perspetiva de informações da máquina virtual no portal do Azure](./media/vminsights-overview/vminsights-azmon-directvm.png)

O Monitor Azure para VMs pode fornecer desempenho previsível e disponibilidade de aplicações vitais. Identifica estrangulamentos de desempenho e problemas de rede. O Monitor Azure para VMs também pode ajudá-lo a entender se um problema está relacionado com outras dependências.  

## <a name="data-usage"></a>Utilização de dados

Ao implementar o Azure Monitor para as VMs, os dados recolhidos pelas suas VMs são ingeridos e armazenados no Azure Monitor. Os dados de desempenho e dependência recolhidos são armazenados num espaço de trabalho do Log Analytics. Com base nos preços publicados na página de preços do [Monitor Do Azure,](https://azure.microsoft.com/pricing/details/monitor/)o Monitor Azure para VMs é cobrado para:

- Os dados ingeridos e armazenados.
- As regras de alerta que são criadas.
- As notificações que são enviadas. 

O tamanho do registo varia pelos comprimentos das cordas dos contadores de desempenho, e pode aumentar com o número de discos lógicos e adaptadores de rede atribuídos ao VM. Se já tiver uma área de trabalho e está a recolher estes contadores, é aplicada uma cobrança duplicada. Se já estiver a utilizar o mapa de serviço, a única alteração que verá é os dados de ligação adicional que são enviados para o Azure Monitor.

## <a name="next-steps"></a>Passos seguintes

Para compreender os requisitos e métodos que o ajudam a monitorizar as suas máquinas virtuais, reveja o [Monitor De implantação de Dispositivos Azure para VMs](vminsights-enable-overview.md).
