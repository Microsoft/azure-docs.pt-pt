---
title: Descrição geral dos conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais informações sobre os conjuntos de dimensionamento de máquinas virtuais e como dimensionar automaticamente as suas aplicações
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/27/2018
ms.author: manayar
ms.openlocfilehash: 1ace0ddd3b29f844488eb7579934541fa8273fa0
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199907"
---
# <a name="what-are-virtual-machine-scale-sets"></a>O que são os conjuntos de dimensionamento de máquinas virtuais?
Os conjuntos de dimensionamento de máquinas virtuais do Azure permitem-lhe criar e gerir um grupo de VMs idênticas, com balanceamento de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente como resposta à procura ou a uma agenda definida. Os conjuntos de dimensionamento dão elevada disponibilidade às suas aplicações e permitem, de forma central, gerir, configurar e atualizar um grande número de VMs. Com os conjuntos de dimensionamento de máquinas virtuais, pode criar serviços em larga escala para áreas como computação, macrodados e cargas de trabalho de contentores.


## <a name="why-use-virtual-machine-scale-sets"></a>Porquê utilizar conjuntos de dimensionamento de máquinas virtuais?
Para fornecer redundância e melhorias em termos de desempenho, as aplicações são geralmente distribuídas por múltiplas instâncias. Os clientes podem aceder à sua aplicação através de um balanceador de carga que distribui pedidos a uma das instâncias de aplicações. Se precisar de efetuar manutenção ou atualizar uma instância de aplicação, os seus clientes terão de ser distribuídos para outra instância de aplicação disponível. Para acompanhar a procura adicional dos clientes, poderá precisar de aumentar o número de instâncias de aplicações que executam a sua aplicação.

Os conjuntos de dimensionamento de máquinas virtuais fornecem as capacidades de gestão para aplicações executadas em muitas VMs, [dimensionamento automático de recursos](virtual-machine-scale-sets-autoscale-overview.md) e balanceamento de carga do tráfego. Os conjuntos de dimensionamento fornecem as seguintes vantagens-chave:

- **Facilidade em criar e gerir múltiplas VMs**
    - Quando tiver muitas VMs a executar a sua aplicação, é importante manter uma configuração consistente em todo o ambiente. Para um desempenho fiável da sua aplicação, o tamanho das VMs, a configuração de discos e as instalações de aplicações deverão ser iguais em todas as VMs.
    - Com os conjuntos de dimensionamento, todas as instâncias de VMs são criadas a partir da mesma configuração e imagem do SO de base. Esta abordagem permite-lhe facilmente gerir centenas de VMs sem gestão de rede ou tarefas de configuração adicionais.
    - Os conjuntos de dimensionamento suportam a utilização do [balanceador de carga do Azure](../load-balancer/load-balancer-overview.md) para uma distribuição de tráfego de nível 4 básica e o [Gateway de Aplicação do Azure](../application-gateway/application-gateway-introduction.md) para terminação SSL e distribuição de tráfego de nível 7 mais avançadas.

- **Fornece disponibilidade elevada e resiliência de aplicações**
    - Os conjuntos de dimensionamento são utilizados para executar múltiplas instâncias da sua aplicação. Se uma destas instâncias de VM tiver um problema, os clientes continuarão a aceder à sua aplicação através de uma das outras instâncias de VMs com interrupções mínimas.
    - Para disponibilidade adicional, pode utilizar as [Zonas de Disponibilidade](../availability-zones/az-overview.md) para distribuir automaticamente instâncias de VMs num conjunto de dimensionamento num único centro de dados ou em diversos centros de dados.

- **Permite que a sua aplicação seja automaticamente dimensionada à medida que a procura de recursos mudar**
    - A procura de clientes da sua aplicação pode mudar ao longo do dia ou da semana. Para corresponder à procura dos clientes, os conjuntos de dimensionamento podem, de forma automática, aumentar o número de instâncias de VMs à medida que a procura de aplicações aumenta e, em seguida, reduzir o número de instâncias de VMs à medida que a procura diminui.
    - O dimensionamento automático também minimiza o número de instâncias de VM desnecessárias que executam a aplicação quando a procura está reduzida, ao passo que os clientes continuam a receber um nível de desempenho aceitável à medida que a procura aumenta e mais instâncias de VMs são automaticamente adicionadas. Esta capacidade ajuda a reduzir custos e, de forma eficiente, criar recursos do Azure conforme necessário.

- **Funciona em grande escala**
    - Os conjuntos de dimensionamento suportam até 1000 instâncias de VMs. Se criar e carregar as suas próprias imagens VM personalizadas, o limite é de 600 instâncias de VM.
    - Para obter o melhor desempenho com cargas de trabalho de produção, utilize [Managed Disks do Azure](../virtual-machines/windows/managed-disks-overview.md).


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Diferenças entre máquinas virtuais e conjuntos de dimensionamento
Os conjuntos de dimensionamento são criados a partir de máquinas virtuais. Nos conjuntos de dimensionamento, as camadas de gestão e automatização são fornecidas para executar e dimensionar as suas aplicações. Em alternativa, pode criar e gerir manualmente VMs individuais ou integrar as ferramentas existentes para criar um nível de automatização semelhante. A seguinte tabela descreve as vantagens dos conjuntos de dimensionamento em comparação com a gestão manual de múltiplas instâncias de VMs.

| Cenário                           | Grupo manual de VMs                                                                    | Conjuntos de dimensionamento de máquinas virtuais |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Adicionar mais instâncias de VMs        | Processo manual para criar, configurar e garantir a conformidade                             | Criar automaticamente a partir da configuração central |
| Distribuição e balanceamento de tráfego | Processo manual para criar e configurar o Gateway de Aplicação ou balanceador de carga do Azure      | Pode criar e integrar automaticamente no Gateway de Aplicação ou balanceador de carga do Azure |
| Elevada e disponibilidade e redundância   | Criar Conjuntos de Disponibilidade manualmente ou distribuir e controlar VMs nas diferentes Zonas de Disponibilidade | Distribuição automática de instâncias de VMs em Zonas de Disponibilidade ou Conjuntos de Disponibilidade |
| Dimensionamento de VMs                     | Monitorização manual e Automatização do Azure                                                 | Dimensionamento automático com base em métricas de anfitrião, métricas em convidados, Application Insights ou agendas |

Os conjuntos de dimensionamento não têm custos adicionais. Só tem de pagar os recursos de computação subjacentes, como as instâncias de VMs, o balanceador de carga ou o armazenamento de Discos Geridos. As funcionalidades de gestão e automatização, como o dimensionamento automático e a redundância, não implicam custos adicionais pela utilização de VMs.


## <a name="next-steps"></a>Passos Seguintes
Para começar, crie o seu primeiro conjunto de dimensionamento de máquinas virtuais no portal do Azure.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento no portal do Azure](quick-create-portal.md)
