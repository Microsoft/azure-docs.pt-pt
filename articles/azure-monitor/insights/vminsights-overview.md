---
title: O que é Azure Monitor para VMs (versão prévia)? | Microsoft Docs
description: Azure Monitor para VMs é um recurso de Azure Monitor que combina o monitoramento de integridade e desempenho do sistema operacional da VM do Azure, bem como a descoberta automática de componentes e dependências de aplicativos com outros recursos e mapeia a comunicação entre eles. Este artigo fornece uma visão geral.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 2364218a9ccbde1cbfc276e3354cb93e42e5ce0b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553749"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>O que é Azure Monitor para VMs (versão prévia)?

O Azure Monitor para VMs monitora as máquinas virtuais (VM) do Azure e os conjuntos de dimensionamento de máquinas virtuais em escala. Analisa o desempenho e o estado de funcionamento das suas VMs do Windows e do Linux e monitoriza os respetivos processos e dependências noutros recursos e processos externos. 

Ele inclui suporte para monitoramento de desempenho e dependências de aplicativo para VMs que são hospedadas localmente ou em outro provedor de nuvem. Três recursos principais oferecem insights aprofundados:

- Os **componentes lógicos de VMs do Azure e conjuntos de dimensionamento de máquinas virtuais que executam Windows e Linux**: são medidos em relação aos critérios de integridade pré-configurados e os alertam quando a condição avaliada é atendida.  

- **Gráficos de desempenho de tendência predefinidos**: Exibir métricas de desempenho de núcleo do sistema operacional da VM convidada.

- **Mapa de dependências**: exibe os componentes interconectados com a VM de vários grupos de recursos e assinaturas.  

Os recursos são organizados em três perspectivas:

- Saúde
- Desempenho
- Mapa

>[!NOTE]
>Recentemente, [anunciamos alterações](https://azure.microsoft.com/updates/upcoming-changes-for-azure-monitor-for-vms-as-we-prepare-for-ga) que estamos fazendo alterações no recurso de integridade com base nos comentários que recebemos de nossos clientes de demonstração pública. Considerando o número de alterações que iremos fazer, vamos parar de oferecer o recurso de integridade para novos clientes. Os clientes existentes podem continuar a usar o recurso de integridade. Para obter mais detalhes, consulte nossas [perguntas frequentes sobre disponibilidade geral](vminsights-ga-release-faq.md).  

A integração com os logs de Azure Monitor fornece agregação e filtragem poderosas e pode analisar as tendências de dados ao longo do tempo. Esse monitoramento de carga de trabalho abrangente não pode ser obtido com Azure Monitor ou Mapa do Serviço sozinhos.  

Você pode exibir esses dados em uma única VM da máquina virtual diretamente ou pode usar Azure Monitor para fornecer uma exibição agregada de suas VMs. Essa exibição é baseada na perspectiva de cada recurso:

- **Integridade**: as VMs estão relacionadas a um grupo de recursos.
- **Mapa** e **desempenho**: as VMs são configuradas para relatar a um espaço de trabalho log Analytics específico.

![Perspectiva do insights de máquina virtual no portal do Azure](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure Monitor para VMs pode fornecer desempenho previsível e disponibilidade de aplicativos vitais. Ele identifica eventos críticos do sistema operacional, gargalos de desempenho e problemas de rede. Azure Monitor para VMs também pode ajudá-lo a entender se um problema está relacionado a outras dependências.  

## <a name="data-usage"></a>Uso de dados

Quando você implanta Azure Monitor para VMs, os dados coletados pelas VMs são ingeridos e armazenados em Azure Monitor. As métricas de critérios de integridade são armazenadas em Azure Monitor em um banco de dados de série temporal, os dados de desempenho e dependência coletados são armazenados em um espaço de trabalho Log Analytics. Com base no preço que é publicado na [página de preços Azure monitor](https://azure.microsoft.com/pricing/details/monitor/), Azure monitor para VMs é cobrado por:

- Os dados que são ingeridos e armazenados.
- O número de critérios de integridade da série temporal que são monitorados.
- As regras de alerta que são criadas.
- As notificações que são enviadas. 

O tamanho do log varia de acordo com os comprimentos de cadeia de caracteres de contadores de desempenho e pode aumentar com o número de discos lógicos e adaptadores de rede alocados para a VM. Se você já tiver um espaço de trabalho e estiver coletando esses contadores, nenhum encargo duplicado será aplicado. Se você já estiver usando Mapa do Serviço, a única alteração que você verá é os dados de conexão adicionais que são enviados para Azure Monitor.

## <a name="next-steps"></a>Passos seguintes

Para entender os requisitos e métodos que ajudam a monitorar suas máquinas virtuais, examine [implantar Azure monitor para VMs](vminsights-enable-overview.md).
