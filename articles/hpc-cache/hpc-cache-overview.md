---
title: Visão geral da Cache Azure HPC
description: Descreve a Azure HPC Cache, uma solução de acelerador de acesso a ficheiros para computação de alto desempenho
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 2085efc5f38b2252e40f4aeb1ebfa16bf38147b4
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256145"
---
# <a name="what-is-azure-hpc-cache"></a>O que é o Azure HPC Cache?

A azure HPC Cache acelera o acesso aos seus dados para tarefas de computação de alto desempenho (HPC). Ao cache de ficheiros em Azure, a Cache Azure HPC traz a escalabilidade da computação em nuvem ao seu fluxo de trabalho existente. Este serviço pode ser utilizado mesmo para fluxos de trabalho onde os seus dados são armazenados através de links WAN, como no ambiente de armazenamento (NAS) ligado à rede de datacenter local.

A Azure HPC Cache é fácil de lançar e monitorizar a partir do portal Azure. O armazenamento NFS existente ou novos recipientes Blob podem tornar-se parte do seu espaço de nome agregado, o que torna o acesso do cliente simples mesmo que altere o alvo de armazenamento back-end.

## <a name="overview-video"></a>Vídeo de descrição geral

[![miniatura de vídeo: Azure HPC Cache visão geral - clique para visitar a página de vídeo](media/video-1-overview.png)](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)

Clique na imagem acima para ver uma [breve visão geral da Cache Azure HPC](https://azure.microsoft.com/resources/videos/hpc-cache-overview/).

## <a name="use-cases"></a>Casos de utilização

AZure HPC Cache melhora a produtividade para fluxos de trabalho como estes:

* Fluxo de trabalho de acesso a ficheiros pesados
* Dados armazenados em armazenamento acessível a NFS, Azure Blob, ou ambos
* Explorações de computação até 75.000 núcleos de CPU

A Azure HPC Cache pode ser adicionado a uma grande variedade de fluxos de trabalho em muitas indústrias. Qualquer sistema em que um grande número de máquinas precise de aceder a um conjunto de ficheiros em escala e com baixa latência beneficiará deste serviço. As secções abaixo dão exemplos específicos.

### <a name="visual-effects-vfx-rendering"></a>Renderização de efeitos visuais (VFX)

Nos meios de comunicação e entretenimento, a Azure HPC Cache pode acelerar o acesso de dados para projetos de renderização críticos do tempo. Os fluxos de trabalho de renderização VFX requerem frequentemente o processamento de última hora por um grande número de nós de computação. Os dados para estes fluxos de trabalho estão tipicamente localizados num ambiente NAS no local. A Azure HPC Cache pode cache esses dados de arquivo na nuvem para reduzir a latência e aumentar a flexibilidade para renderização a pedido.

### <a name="life-sciences"></a>Ciências da vida

Muitos fluxos de trabalho de ciências da vida podem beneficiar do caching de ficheiros de escala.

Um instituto de investigação que queira levar os seus fluxos de trabalho de análise genómica para o Azure pode facilmente mudá-los usando a cache Azure HPC. Como a cache fornece acesso a ficheiros POSIX, não são necessárias alterações do lado do cliente para executar o fluxo de trabalho do cliente existente na nuvem.

A Azure HPC Cache também pode ser alavancado para melhorar a eficiência em tarefas como análise secundária, simulação farmacológica ou análise de imagem orientada pela IA.

### <a name="financial-services-analytics"></a>Análise de serviços financeiros

Uma implementação de Cache Azure HPC pode ajudar a acelerar os cálculos de análise quantitativa, as cargas de trabalho de análise de risco e as simulações de Monte Carlo para dar às empresas de serviços financeiros uma melhor visão para tomarem decisões estratégicas.

## <a name="region-availability"></a>Disponibilidade de região

Visite os [produtos da Infraestrutura Global Azure por página](https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache) para saber onde está disponível a Cache Azure HPC.

Azure HPC Cache reside numa única região. Pode aceder aos dados armazenados noutras regiões se os ligar aos contentores Blob aí localizados. A cache não armazena permanentemente os dados do cliente.

## <a name="next-steps"></a>Passos seguintes

* Leia a página de [produto Azure HPC Cache](https://azure.microsoft.com/services/hpc-cache) para saber mais sobre as suas capacidades
* Conheça os [pré-requisitos do](hpc-cache-prerequisites.md) produto
* [Criar uma Cache Azure HPC](hpc-cache-create.md) a partir do portal Azure
