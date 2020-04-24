---
title: Visão geral do Cache Azure HPC
description: Descreve o Azure HPC Cache, uma solução aceleradora de acesso a ficheiros para computação de alto desempenho
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 02013abe7050f7f193b6aeb1a441e70f46a5df48
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106462"
---
# <a name="what-is-azure-hpc-cache"></a>O que é o Azure HPC Cache?

O Cache Azure HPC acelera o acesso aos seus dados para tarefas de computação de alto desempenho (HPC). Ao arquivar ficheiros em Azure, o Azure HPC Cache traz a escalabilidade da computação em nuvem ao seu fluxo de trabalho existente. Este serviço pode ser utilizado mesmo para fluxos de trabalho onde os seus dados são armazenados através de links WAN, como no seu ambiente local de armazenamento ligado à rede de dados (NAS).

A Azure HPC Cache é fácil de lançar e monitorizar a partir do portal Azure. O armazenamento nfs existente ou novos recipientes Blob podem tornar-se parte do seu espaço de nome agregado, o que torna o acesso ao cliente simples mesmo que altere o alvo de armazenamento final.

## <a name="use-cases"></a>Casos de utilização

A Azure HPC Cache melhor aumenta a produtividade para fluxos de trabalho como estes:

* Fluxo de trabalho de acesso a ficheiros pesados de leitura
* Dados armazenados em armazenamento acessível à NFS, Azure Blob, ou ambos
* Fazendas de computação de até 75.000 núcleos de CPU

A Azure HPC Cache pode ser adicionada a uma grande variedade de fluxos de trabalho em muitas indústrias. Qualquer sistema onde um grande número de máquinas precise de aceder a um conjunto de ficheiros em escala e com baixa latência beneficiará deste serviço. As secções abaixo dão exemplos específicos.

### <a name="visual-effects-vfx-rendering"></a>Prestação de efeitos visuais (VFX)

Nos meios de comunicação e entretenimento, o Azure HPC Cache pode acelerar o acesso de dados a projetos de renderização críticos de tempo. Os fluxos de trabalho de renderização VFX exigem frequentemente processamento de última hora por um grande número de nódos de cálculo. Os dados para estes fluxos de trabalho estão tipicamente localizados num ambiente NAS no local. A Cache Azure HPC pode cache que os dados de ficheiros na nuvem para reduzir a latência e aumentar a flexibilidade para a renderização a pedido.

### <a name="life-sciences"></a>Ciências da vida

Muitos fluxos de trabalho em ciências da vida podem beneficiar do cachede arquivo scale-out.

Um instituto de investigação que quer colocar os seus fluxos de trabalho de análise genómica em Azure pode facilmente deslocá-los usando azure HPC Cache. Como a cache fornece acesso a ficheiros POSIX, não são necessárias alterações do lado do cliente para executar o fluxo de trabalho do cliente existente na nuvem.

A Cache Azure HPC também pode ser alavancada para melhorar a eficiência em tarefas como análise secundária, simulação farmacológica ou análise de imagem orientada por IA.

### <a name="financial-services-analytics"></a>Análise de serviços financeiros

Uma implantação de Cache Azure HPC pode ajudar a acelerar os cálculos de análise quantitativa, as cargas de trabalho de análise de risco e as simulações de Monte Carlo para dar às empresas de serviços financeiros uma melhor visão para tomarem decisões estratégicas.

## <a name="region-availability"></a>Disponibilidade de região

Azure HPC Cache está disponível nestas regiões azure:

| América do Norte      | Europa         | Ásia            | Austrália      |
|--------------------|----------------|-----------------|----------------|
| E.U.A. Leste            | Europa do Norte   | Coreia do Sul Central   | Leste da Austrália |
| E.U.A. Leste 2          | Europa ocidental    | Ásia Sudeste  |               |
| E.U.A. Centro-Sul | | | |
| E.U.A.Oeste 2        | | | |

A [função de chaves gerida pelo cliente](customer-keys.md) é suportada apenas nestas regiões:

* E.U.A. Leste
* E.U.A. Centro-Sul
* E.U.A.Oeste 2

Consulte a página do [produto Azure HPC Cache](https://azure.microsoft.com/services/hpc-cache) para obter as informações mais recentes sobre a disponibilidade.

## <a name="service-availability"></a>Disponibilidade de serviço

Tem de solicitar acesso para cada subscrição que utilizará com o Azure HPC Cache. Esta restrição ajuda a garantir a qualidade do serviço.

Solicite acesso preenchendo [este formulário](https://aka.ms/onboard-hpc-cache). Depois de a sua subscrição ser adicionada à lista de acesso, pode criar caches.

## <a name="next-steps"></a>Passos seguintes

* Leia a página de [produto Azure HPC Cache](https://azure.microsoft.com/services/hpc-cache) para saber mais sobre as suas capacidades
* Conheça os [pré-requisitos](hpc-cache-prereqs.md) do produto
* [Crie um Cache Azure HPC](hpc-cache-create.md) do portal Azure
