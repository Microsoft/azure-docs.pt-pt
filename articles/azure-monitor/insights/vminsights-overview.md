---
title: O que é o Azure Monitor para VMs (pré-visualização)? | Microsoft Docs
description: Visão geral de Azure Monitor para VMs que monitora a integridade e o desempenho das VMs do Azure, além de descobrir e mapear automaticamente os componentes do aplicativo e suas dependências.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: e9a3df0172f276800a3049675f6e858db7f0370c
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307301"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>O que é o Azure Monitor para VMs (pré-visualização)?

Monitor do Azure para VMs monitoriza máquinas de virtuais do Azure (VM) e conjuntos de dimensionamento de máquinas virtuais em escala. Analisa o desempenho e o estado de funcionamento das suas VMs do Windows e do Linux e monitoriza os respetivos processos e dependências noutros recursos e processos externos. 

Ele inclui suporte para monitoramento de desempenho e dependências de aplicativo para VMs que são hospedadas localmente ou em outro provedor de nuvem. Os principais recursos a seguir fornecem informações aprofundadas:

- **Gráficos de desempenho de tendência predefinidos**: Exibir métricas de desempenho de núcleo do sistema operacional da VM convidada.

- **Mapa de dependências**: exibe os componentes interconectados com a VM de vários grupos de recursos e assinaturas.  

>[!NOTE]
>Recentemente, [anunciamos alterações](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) que estamos fazendo no recurso de integridade com base nos comentários que recebemos de nossos clientes de demonstração pública. Considerando o número de alterações que iremos fazer, vamos parar de oferecer o recurso de integridade para novos clientes. Os clientes existentes podem continuar a usar o recurso de integridade. Para obter mais detalhes, consulte nossas [perguntas frequentes sobre disponibilidade geral](vminsights-ga-release-faq.md).  

A integração com os logs de Azure Monitor fornece agregação e filtragem poderosas e pode analisar as tendências de dados ao longo do tempo. Esse monitoramento de carga de trabalho abrangente não pode ser obtido com Azure Monitor ou Mapa do Serviço sozinhos.  

Você pode exibir esses dados em uma única VM a partir da máquina virtual diretamente ou pode usar Azure Monitor para fornecer uma exibição agregada de suas VMs em que a exibição dá suporte aos modos de contexto de recurso do Azure ou de espaço de trabalho. Para obter mais informações, consulte [visão geral de modos de acesso](../platform/design-logs-deployment.md#access-mode).

![Perspetiva de informações da máquina virtual no portal do Azure](./media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor para VMs pode fornecer desempenho previsível e disponibilidade de aplicativos vitais. Ele identifica gargalos de desempenho e problemas de rede. Azure Monitor para VMs também pode ajudá-lo a entender se um problema está relacionado a outras dependências.  

## <a name="data-usage"></a>Utilização de dados

Ao implementar o Azure Monitor para as VMs, os dados recolhidos pelas suas VMs são ingeridos e armazenados no Azure Monitor. Os dados de desempenho e dependência coletados são armazenados em um espaço de trabalho Log Analytics. Com base no preço que é publicado na [página de preços Azure monitor](https://azure.microsoft.com/pricing/details/monitor/), Azure monitor para VMs é cobrado por:

- Os dados ingeridos e armazenados.
- As regras de alerta que são criadas.
- As notificações que são enviadas. 

O tamanho do log varia de acordo com os comprimentos de cadeia de caracteres de contadores de desempenho e pode aumentar com o número de discos lógicos e adaptadores de rede alocados para a VM. Se já tiver uma área de trabalho e está a recolher estes contadores, é aplicada uma cobrança duplicada. Se já estiver a utilizar o mapa de serviço, a única alteração que verá é os dados de ligação adicional que são enviados para o Azure Monitor.

## <a name="next-steps"></a>Passos seguintes

Para entender os requisitos e métodos que ajudam a monitorar suas máquinas virtuais, examine [implantar Azure monitor para VMs](vminsights-enable-overview.md).
