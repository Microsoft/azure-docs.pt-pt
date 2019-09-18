---
title: Visão geral do cache HPC do Azure
description: Descreve o cache do HPC do Azure, uma solução de acelerador de acesso a arquivo para computação de alto desempenho
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 77eca3ef58733a616705fdaac1d2880b5a990e8a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036874"
---
# <a name="what-is-azure-hpc-cache"></a>O que é o Azure HPC Cache?

O cache do HPC do Azure acelera o acesso aos seus dados para tarefas HPC (computação de alto desempenho). Ao armazenar arquivos em cache no Azure, ele torna a escalabilidade da computação em nuvem disponível mesmo para fluxos de trabalho em que os dados são armazenados em links de WAN, como em seu ambiente NAS (armazenamento conectado à rede) do datacenter local.

O cache HPC do Azure é fácil de iniciar e monitorar da portal do Azure. O armazenamento NFS existente ou novos contêineres de blob podem se tornar parte de seu namespace agregado, o que torna o acesso de cliente simples, mesmo se você alterar o destino de armazenamento de back-end.

## <a name="use-cases"></a>Casos de utilização

O cache HPC do Azure aprimora melhor a produtividade para fluxos de trabalho como estes:

* Fluxo de trabalho de acesso a arquivo intenso de leitura
* Dados armazenados no armazenamento acessível por NFS, BLOB do Azure ou ambos
* Farms de computação de até 75.000 núcleos de CPU

O cache do HPC do Azure pode ser adicionado a uma ampla variedade de fluxos de trabalho em vários setores. Qualquer sistema em que um grande número de máquinas precisa acessar um conjunto de arquivos em escala e com baixa latência se beneficiará desse serviço. As seções a seguir fornecem exemplos específicos.

### <a name="visual-effects-vfx-rendering"></a>Renderização de efeitos visuais (VFX)

Em mídia e entretenimento, o cache HPC do Azure pode acelerar o acesso a dados para projetos de renderização de tempo crítico. Os fluxos de trabalho de renderização VFX geralmente exigem o processamento de último minuto por um grande número de nós de computação. Os dados desses fluxos de trabalho normalmente estão localizados em um ambiente NAS local. O cache HPC do Azure pode armazenar em cache esses dados de arquivo na nuvem para reduzir a latência e aumentar a flexibilidade para renderização sob demanda.

### <a name="life-sciences"></a>Ciências da vida

Muitos fluxos de trabalho de ciências biomédicas podem se beneficiar do cache de arquivos de escalabilidade horizontal.

Um Instituto de pesquisa que deseja portar seus fluxos de trabalho de análise de genoma para o Azure pode facilmente deslocar usando o cache do HPC do Azure. Como o cache fornece acesso a arquivos POSIX, eles podem executar o fluxo de trabalho existente no lado do cliente na nuvem sem nenhuma alteração.

O cache HPC do Azure também pode ser aproveitado para melhorar a eficiência em tarefas como análise secundária, simulação de Pharmacological ou análise de imagem orientada por ia.

### <a name="financial-services-analytics"></a>Análise de serviços financeiros

Um cache HPC do Azure pode ajudar a acelerar cálculos de análise quantitativos, cargas de trabalho de análise de risco e simulações Monte Carlo para fornecer às empresas de serviços financeiros melhores insights para tomar decisões estratégicas.

## <a name="region-availability"></a>Disponibilidade de região

O cache do HPC do Azure está disponível nessas regiões do Azure:

* East US
* EUA Leste 2
* Europa do Norte
* Europa Ocidental
* Sudeste Asiático
* EUA Oeste 2

Verifique a [página do produto de cache HPC do Azure](https://azure.microsoft.com/services/hpc-cache) para obter as informações de disponibilidade mais recentes.

## <a name="next-steps"></a>Passos seguintes

* Leia a [página do produto cache HPC do Azure](https://azure.microsoft.com/services/hpc-cache) para saber mais sobre seus recursos
* Saiba mais sobre os [pré-requisitos](hpc-cache-prereqs.md) do produto
* [Criar um cache HPC do Azure](hpc-cache-create.md) do portal do Azure
