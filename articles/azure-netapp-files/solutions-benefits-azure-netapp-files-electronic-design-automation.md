---
title: Benefícios da utilização de Ficheiros Azure NetApp para automatização de design eletrónico Microsoft Docs
description: Explica a solução que o Azure NetApp Files fornece para satisfazer as necessidades da indústria de design de semicondutores e chips. Apresenta cenários de teste que executam um referencial padrão da indústria para a automatização de design eletrónico (EDA) usando ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: fcede16619e8488796adc6f4c60af30643c1aadf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82160158"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>Benefícios da utilização do Azure NetApp Files para a automatização de design eletrónico

O time-to-market (TTM) é uma consideração crítica para a indústria de semicondutores e design de chips. A indústria tem alta largura de banda e baixa necessidade de latência para armazenamento. Este artigo explica a solução que o Azure NetApp Files fornece para satisfazer as necessidades do setor. Apresenta cenários de teste que executam um referencial padrão da indústria para a automatização de design eletrónico (EDA) utilizando ficheiros Azure NetApp. 

## <a name="test-scenario-configurations"></a>Configurações do cenário de teste

Os testes envolvem três cenários com as seguintes configurações. 

|    Cenário    |    Volumes    |    Clientes<br> SLES15 D16s_v3  |
|----------------|---------------|--------------------------------|
|    Um         |    1          |    1                           |
|    Dois         |    6          |    24                          |
|    Três       |    12         |    24                          |

O primeiro cenário aborda até que ponto um único volume pode ser conduzido.  

O segundo e o terceiro cenários avaliam os limites de um único ponto final do Azure NetApp Files. Investigam os potenciais benefícios dos limites máximos de I/O e da latência.

## <a name="test-scenario-results"></a>Resultados do cenário do teste

A tabela seguinte resume os resultados dos cenários de teste.

|    Cenário       |    Taxa de I/O<br>  às 2 ms     |    Taxa de I/O<br>  na borda     |    Débito<br>  às 2 ms     |    Débito<br>  na borda     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    1 volume       |    39,601                 |    49,502                      |    692 MiB/s                 |    866 MiB/s                      |
|    6 volumes      |    255,613                |    317,000                     |    4,577 MiB/s               |    5.568 MiB/s                    |
|    12 volumes     |    256,612                |    319,196                     |    4,577 MiB/s               |    5.709 MiB/s                    |

O cenário de volume único representa a configuração básica da aplicação. É o cenário básico para cenários de testes.  

O cenário de seis volumes demonstra um aumento linear (600%) em relação à carga de trabalho de volume único.  Todos os volumes dentro de uma única rede virtual são acedidos num único endereço IP.  

O cenário de 12 volumes demonstra uma diminuição geral da latência durante o cenário de seis volumes. Mas não tem um aumento correspondente na produção alcançável.   

O gráfico a seguir ilustra a taxa de latência e de operações para a carga de trabalho da EDA nos Ficheiros Azure NetApp.  

![Taxa de latência e de operações para a carga de trabalho da EDA nos ficheiros Azure NetApp](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

O gráfico a seguir ilustra a latência e a produção da carga de trabalho da EDA nos Ficheiros Azure NetApp.  

![Latência e produção para a carga de trabalho da EDA em Ficheiros Azure NetApp](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>Layout de cenários de teste 

A tabela abaixo resume o layout dos cenários de teste.

|    Cenário de teste     |    Número total de diretórios     |    Número total de ficheiros     |
|----------------------|------------------------------------|------------------------------|
|    1 volume          |    88,000                          |    880,000                   |
|    6 volumes         |    568,000                         |    5,680,000                 |
|    12 volumes        |    568,000                         |    5,680,000                 |

A carga de trabalho completa é uma mistura de fases funcionais e físicas simultaneamente executadas. Representa um fluxo típico de um conjunto de ferramentas EDA para outra.   

A fase funcional consiste em especificações iniciais e um design lógico. A fase física ocorre quando o design lógico é convertido em chip físico. Durante as fases de aprovação e de eliminação da fita, os controlos finais são concluídos e o desenho é entregue numa fundição para fabrico.  

A fase funcional inclui uma mistura de leitura sequencial e aleatória e escrita I/O. A fase funcional é intensiva de metadados, como estatísticas de ficheiros e chamadas de acesso. Embora as operações de metadados sejam efetivamente sem tamanho, as operações de leitura e escrita variam entre menos de 1 K e 16 K. A maioria das leituras são entre 4 K e 16 K.  A maioria das escritas são 4 K ou menos. A fase física é composta por operações de leitura e escrita sequencial inteiramente, com uma mistura de tamanhos op de 32 K e 64 K.  

Nos gráficos acima, a maior parte da produção provém da fase física sequencial da carga de trabalho. O E/S provém da pequena fase funcional aleatória e intensiva de metadados. Ambas as fases acontecem em paralelo. 

Em conclusão, pode emparelhar o cálculo Azure com os Ficheiros Azure NetApp para design EDA para obter largura de banda escalável. 

## <a name="next-steps"></a>Passos seguintes

- [Arquiteturas de solução com o Azure NetApp Files](azure-netapp-files-solution-architectures.md)
