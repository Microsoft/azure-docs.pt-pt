---
title: Planeamento da capacidade do Azure Stack | Documentos da Microsoft
description: Saiba mais sobre o planeamento da capacidade para implementações do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 09/18/2018
ms.openlocfilehash: 3d825a0f8a23380b4d9cf453076ab4b18ee67831
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58095522"
---
# <a name="azure-stack-capacity-planning"></a>Planeamento da capacidade de pilha do Azure
Ao avaliar uma solução de pilha do Azure, existem opções de configuração de hardware que têm um impacto direto na capacidade geral da Cloud do Azure Stack. Estas são as escolhas clássicas de CPU, densidade de memória, configuração de armazenamento e geral escala de solução ou número de servidores. Ao contrário de uma solução de Virtualização tradicionais, não é aplicável a aritmética simple desses componentes para determinar a capacidade utilizável. O primeiro motivo para isso é que o Azure Stack foi concebido para alojar os componentes de infraestrutura ou de gestão dentro da solução em si. O segundo motivo é que alguns da capacidade da solução está reservado para oferecer suporte a resiliência; a atualização de software da solução de forma a minimizar a interrupção das cargas de trabalho de inquilino.

> [!IMPORTANT]
> As informações de planeamento de capacidade fornecida e a folha de cálculo que acompanha este artigo, destinam-se apenas como um guia para ajudar a facilitar o planejamento do Azure Stack e decisões de configuração. Estas não recomendações pretendem servir como um substituto para sua própria investigação e análise. 

## <a name="compute-and-storage-capacity-planning"></a>Planeamento de capacidade de armazenamento e computação
Uma solução do Azure Stack foi concebida como um cluster hiperconvergido de computação, redes e armazenamento. Isto permite o uso Efetivo ou compartilhamento de todas as capacidade de hardware do cluster, conhecido como uma unidade de escala para o Azure Stack, de forma que fornece disponibilidade e escalabilidade. Todos os softwares de infraestrutura é hospedado dentro de um conjunto de máquinas virtuais (VMs) e compartilha os mesmos servidores físicos, como as VMs do inquilino. Todas as VMs são geridas por tecnologias de clustering do Windows Server a unidade de escala e instâncias individuais do Hyper-V. Esta abordagem simplifica a aquisição e gestão de uma solução do Azure Stack e permite o movimento e a escalabilidade de todos os serviços (infraestrutura e de inquilino) em todo a unidade de escala.

O recurso físico apenas que não está aprovisionado excesso numa solução do Azure Stack é a memória do servidor. Os outros recursos da CPU núcleos, largura de banda de rede e capacidade de armazenamento, irão ser overprovisioned para fazer o melhor uso de recursos disponíveis. Ao calcular a capacidade disponível para uma solução, a memória de servidor físico é o principal Contribuidor. A utilização de outros recursos é, em seguida, compreender a proporção de aprovisionar em excesso que é possível e qual será aceitável para a carga de trabalho pretendida.

Aproximadamente 28 VMs são utilizadas para alojar a infraestrutura do Azure Stack e, no total, consumir cerca de 208 GB de memória e 124 núcleos virtuais.  A lógica para este número de VMs é para satisfazer a separação de serviço necessário para satisfazer necessidades de segurança, escalabilidade, manutenção e aplicação de patches de requisitos. Esta estrutura de serviço interno permite a futura introdução de novos serviços de infraestrutura como eles são desenvolvidos.

Para suportar a atualização automática de toda a infraestrutura de servidor físico e componentes de software de infraestrutura, ou o patch e atualização e o utilizador VM posicionamentos não irão consumir todos os recursos de memória de unidade de escala. Uma quantidade de memória total em todos os servidores de uma unidade de escala será não alocada para oferecer suporte a requisitos de resiliência da solução. Por exemplo, quando é atualizada a imagem do Windows Server do servidor físico, as VMs alojadas no servidor são movidas em outro lugar dentro da unidade de escala enquanto imagens do Windows Server do servidor é atualizado. Quando a atualização estiver concluída, o servidor é reiniciado e retornado para cargas de trabalho de manutenção. O objetivo de patch e atualização de uma solução do Azure Stack é para evitar a necessidade de parar VMs alojadas. No lutando para cumprir esse objetivo, o mínimo de capacidade de memória de um servidor é não alocado para permitir o movimento de VMs dentro da unidade de escala. Esta colocação e movimento se aplica a VMs de infraestrutura e as VMs criadas em nome de utilizador ou do inquilino da solução do Azure Stack. Os resultados de implementação final são, de modo a que a quantidade de memória reservada para suportar o movimento de VM necessário pode ser muito mais do que a capacidade de um único servidor devido a desafios de posicionamento quando as VMs têm diferentes requisitos de memória. Um custo adicional na categoria de utilização de memória é que a própria instância do Windows Server. A instância de base do sistema operativo para cada servidor irá consumir a memória para o sistema operativo e suas tabelas de página virtual juntamente com a memória usada pelo Hyper-V no gerenciamento de cada uma das VMs alojadas.

Uma descrição mais detalhada sobre as complexidades de cálculos de capacidade é descrever posteriormente nesta secção. Nesta Introdução, os exemplos seguintes são fornecidos para ajudar a compreender a capacidade disponível de diversos tamanhos de solução. Estes são estimativas e assim fazem suposições sobre o uso de memória VM que não poderá ser verdadeiro para instalações de produção do inquilino. Para esta tabela, o tamanho de VM do Azure de D2 padrão é utilizado. VMs do Azure de D2 padrão são definidas com 2 CPUs virtuais e 7 GB de memória.

|     |Por capacidade do servidor|| Capacidade de unidade de escala|  |  |||
|-----|-----|-----|-----|-----|-----|-----|-----|
|     | Memória | Núcleos de CPU | Número de servidores | Memória | Núcleos de CPU | As VMs do inquilino<sup>1</sup>     | / Núcleo elevada<sup>2</sup>    |
|Exemplo 1|256 GB|28|4|1024 GB| 112 | 54 |4:3|
|Exemplo 2|512 GB|28|4|2024 GB|112|144|4:1|
|Exemplo 3|384 GB|28|12|4608 GB|336|432|3:1|
|     |     |     |     |     |     |     |     |

> <sup>1</sup> VMs D2 padrão.
> 
> <sup>2</sup> núcleo Virtual a proporção de núcleos físicos.

Conforme mencionado acima, a capacidade VM é determinada com a memória disponível. Os virtual núcleos para rácios de núcleos físicos exemplificar como a densidade VM irá alterar a capacidade de CPU disponível, a menos que a solução é construída com um grande número de núcleos físicos (outra CPU é escolhida). O mesmo é verdadeiro para capacidade de armazenamento e capacidade de cache de armazenamento.

Os exemplos acima de densidade de VM destinam-se apenas a fins de explicação. Existe mais complexidade nos cálculos de suporte. É necessário para compreender melhor as opções de planeamento de capacidade e a capacidade de resultante, disponível contacto com a Microsoft ou de um parceiro de solução.

O resto desta secção descreve os requisitos de implementação do Azure Stack para computação e armazenamento. Utilize estas informações para obter uma base de compreensão de quais componentes são necessários e os respetivos valores de configuração mínima. Informações adicionais também são fornecidas para descrever como a solução está configurada com respeito a capacidade disponível e os limites de possíveis do sistema em relação à capacidade de capacidade e desempenho do inquilino.

> [!NOTE]
> Os requisitos do planeamento de capacidade para funcionamento em rede são mínimos como apenas o tamanho do VIP público é configurável. Para obter informações sobre como adicionar mais endereços de IP público para o Azure Stack, veja [adicionar endereços IP públicos](azure-stack-add-ips.md).


## <a name="next-steps"></a>Passos Seguintes
[Planeamento da capacidade de computação](capacity-planning-compute.md)
