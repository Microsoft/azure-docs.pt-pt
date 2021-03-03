---
title: Azure escala de máquina virtual define visão geral
description: Saiba mais informações sobre os conjuntos de dimensionamento de máquinas virtuais e como dimensionar automaticamente as suas aplicações
author: mimckitt
ms.author: mimckitt
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: ''
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 76d3bc1e1736e648316bcd81bf8897d1d2f272a2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672586"
---
# <a name="what-are-virtual-machine-scale-sets"></a>O que são os conjuntos de dimensionamento de máquinas virtuais?
Conjuntos de escala de máquina virtual Azure permitem criar e gerir um grupo de VMs equilibrados de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente como resposta à procura ou horário definido. Os conjuntos de dimensionamento dão elevada disponibilidade às suas aplicações e permitem, de forma central, gerir, configurar e atualizar um grande número de VMs. Com os conjuntos de dimensionamento de máquinas virtuais, pode criar serviços em larga escala para áreas como computação, macrodados e cargas de trabalho de contentores.


## <a name="why-use-virtual-machine-scale-sets"></a>Porquê utilizar conjuntos de dimensionamento de máquinas virtuais?
Para fornecer redundância e melhorias em termos de desempenho, as aplicações são geralmente distribuídas por múltiplas instâncias. Os clientes podem aceder à sua aplicação através de um balanceador de carga que distribui pedidos a uma das instâncias de aplicações. Se precisar de efetuar manutenção ou atualizar uma instância de aplicação, os seus clientes terão de ser distribuídos para outra instância de aplicação disponível. Para acompanhar a procura adicional dos clientes, poderá precisar de aumentar o número de instâncias de aplicações que executam a sua aplicação.

Os conjuntos de dimensionamento de máquinas virtuais fornecem as capacidades de gestão para aplicações executadas em muitas VMs, [dimensionamento automático de recursos](virtual-machine-scale-sets-autoscale-overview.md) e balanceamento de carga do tráfego. Os conjuntos de dimensionamento fornecem as seguintes vantagens-chave:

- **Facilidade em criar e gerir múltiplas VMs**
    - Quando tiver muitas VMs a executar a sua aplicação, é importante manter uma configuração consistente em todo o ambiente. Para um desempenho fiável da sua aplicação, o tamanho das VMs, a configuração de discos e as instalações de aplicações deverão ser iguais em todas as VMs.
    - Com os conjuntos de dimensionamento, todas as instâncias de VMs são criadas a partir da mesma configuração e imagem do SO de base. Esta abordagem permite-lhe facilmente gerir centenas de VMs sem gestão de rede ou tarefas de configuração adicionais.
    - Os conjuntos de escala suportam a utilização do equilibrador de [carga Azure](../load-balancer/load-balancer-overview.md) para distribuição básica de tráfego em camadas 4, e [gateway de aplicação Azure](../application-gateway/overview.md) para uma distribuição de tráfego de camadas 7 mais avançada e terminação TLS.

- **Fornece disponibilidade elevada e resiliência de aplicações**
    - Os conjuntos de dimensionamento são utilizados para executar múltiplas instâncias da sua aplicação. Se uma destas instâncias de VM tiver um problema, os clientes continuarão a aceder à sua aplicação através de uma das outras instâncias de VMs com interrupções mínimas.
    - Para disponibilidade adicional, pode utilizar as [Zonas de Disponibilidade](../availability-zones/az-overview.md) para distribuir automaticamente instâncias de VMs num conjunto de dimensionamento num único centro de dados ou em diversos centros de dados.

- **Permite que a sua aplicação seja automaticamente dimensionada à medida que a procura de recursos mudar**
    - A procura de clientes da sua aplicação pode mudar ao longo do dia ou da semana. Para corresponder à procura dos clientes, os conjuntos de dimensionamento podem, de forma automática, aumentar o número de instâncias de VMs à medida que a procura de aplicações aumenta e, em seguida, reduzir o número de instâncias de VMs à medida que a procura diminui.
    - O dimensionamento automático também minimiza o número de instâncias de VM desnecessárias que executam a aplicação quando a procura está reduzida, ao passo que os clientes continuam a receber um nível de desempenho aceitável à medida que a procura aumenta e mais instâncias de VMs são automaticamente adicionadas. Esta capacidade ajuda a reduzir custos e, de forma eficiente, criar recursos do Azure conforme necessário.

- **Funciona em grande escala**
    - Os conjuntos de dimensionamento suportam até 1000 instâncias de VMs. Se criar e carregar as suas próprias imagens VM personalizadas, o limite é de 600 VM.
    - Para obter o melhor desempenho com cargas de trabalho de produção, utilize [discos geridos Azure](../virtual-machines/managed-disks-overview.md).


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Diferenças entre máquinas virtuais e conjuntos de dimensionamento
Os conjuntos de dimensionamento são criados a partir de máquinas virtuais. Nos conjuntos de dimensionamento, as camadas de gestão e automatização são fornecidas para executar e dimensionar as suas aplicações. Em alternativa, pode criar e gerir manualmente VMs individuais ou integrar as ferramentas existentes para criar um nível de automatização semelhante. A seguinte tabela descreve as vantagens dos conjuntos de dimensionamento em comparação com a gestão manual de múltiplas instâncias de VMs.

| Scenario                           | Grupo manual de VMs                                                                    | Conjuntos de dimensionamento de máquinas virtuais |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Adicionar mais instâncias de VMs        | Processo manual para criar, configurar e garantir a conformidade                             | Criar automaticamente a partir da configuração central |
| Distribuição e balanceamento de tráfego | Processo manual para criar e configurar o Gateway de Aplicação ou balanceador de carga do Azure      | Pode criar e integrar automaticamente no Gateway de Aplicação ou balanceador de carga do Azure |
| Elevada e disponibilidade e redundância   | Criar Conjuntos de Disponibilidade manualmente ou distribuir e controlar VMs nas diferentes Zonas de Disponibilidade | Distribuição automática de instâncias de VMs em Zonas de Disponibilidade ou Conjuntos de Disponibilidade |
| Dimensionamento de VMs                     | Monitorização manual e Automatização do Azure                                                 | Dimensionamento automático com base em métricas de anfitrião, métricas em convidados, Application Insights ou agendas |

Os conjuntos de dimensionamento não têm custos adicionais. Só tem de pagar os recursos de computação subjacentes, como as instâncias de VMs, o balanceador de carga ou o armazenamento de Discos Geridos. As funcionalidades de gestão e automatização, como o dimensionamento automático e a redundância, não implicam custos adicionais pela utilização de VMs.

## <a name="how-to-monitor-your-scale-sets"></a>Como monitorizar os seus conjuntos de escala

Utilize [o Azure Monitor para VMs,](../azure-monitor/vm/vminsights-overview.md)que tem um processo de embarque simples e automatizará a recolha de importantes contadores de desempenho cpu, memória, disco e rede dos VMs no seu conjunto de escala. Também inclui capacidades de monitorização adicionais e visualizações pré-definidas que o ajudam a concentrar-se na disponibilidade e desempenho dos seus conjuntos de escala.

Habilitar a monitorização da [sua aplicação de conjunto de escala de máquina virtual](../azure-monitor/app/azure-vm-vmss-apps.md) com Application Insights para recolher informações detalhadas sobre a sua aplicação, incluindo visualizações de páginas, pedidos de aplicações e exceções. Verifique ainda a disponibilidade da sua aplicação configurando um [teste de disponibilidade](../azure-monitor/app/monitor-web-app-availability.md) para simular o tráfego do utilizador.

## <a name="data-residency"></a>Residência dos dados

Em Azure, a funcionalidade para permitir o armazenamento de dados de clientes numa única região está atualmente disponível apenas na Região do Sudeste Asiático (Singapura) da Região Ásia-Pacífico Geo e Brasil Sul (Estado de São Paulo) do Brasil Geo. Para todas as outras regiões, os dados dos clientes são armazenados na Geo. Para mais informações, consulte [o Trust Center.](https://azure.microsoft.com/global-infrastructure/data-residency/)

## <a name="next-steps"></a>Passos seguintes
Para começar, crie o seu primeiro conjunto de dimensionamento de máquinas virtuais no portal do Azure.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento no portal do Azure](quick-create-portal.md)
