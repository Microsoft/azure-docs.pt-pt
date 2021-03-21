---
title: Avere vFXT for Azure
description: Saiba mais sobre Avere vFXT para Azure, uma solução de caching de sistema de ficheiros baseada em nuvem para tarefas de computação de alto desempenho intensivas em dados.
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 5095f05b0c9ce9061781c3558ca6c7091ef37aa5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88270961"
---
# <a name="what-is-avere-vfxt-for-azure"></a>O que é o Avere vFXT for Azure?

O Avere vFXT for Azure é uma solução de colocação na cache do sistema de ficheiros para tarefas de computação de alto desempenho (HPC) intensivas em termos de dados. Permite tirar partido da escalabilidade de computação na cloud para tornar os seus dados acessíveis quando e onde forem necessários, até mesmo para dados armazenados no seu próprio hardware no local.

O Avere vFXT suporta os seguintes cenários de computação comuns:

* Arquitetura de nuvem híbrida - Avere vFXT para Azure pode trabalhar com um sistema de armazenamento de hardware, que proporciona o benefício da computação em nuvem sem ter que mover ficheiros.

* Cloud bursting - Avere vFXT for Azure pode ajudá-lo a mover os seus dados para a nuvem para um único projeto, ou "levantar e deslocar" todo o fluxo de trabalho permanentemente.

![Diagrama que mostra os detalhes do sistema Avere vFXT numa subscrição do Azure ligada ao armazenamento de Blobs e a um datacenter no local](media/avere-vfxt-hybrid.png)

Avere vFXT para Azure é o mais adequado para estas situações:

* Operações de leitura intensiva para cargas de trabalho HPC
* Aplicações que utilizam o protocolo NFS comum
* Farms de computação de 1000 a 40 000 núcleos de CPU
* Integração com o NAS do hardware no local, o armazenamento de Blobs do Azure ou ambos

Para mais informações, visite <https://azure.microsoft.com/services/storage/avere-vfxt/>

## <a name="who-uses-avere-vfxt-for-azure"></a>Quem utiliza o Avere vFXT for Azure?

O Avere vFXT pode ajudar em todos os tipos de tarefas de computação de leitura intensiva:

### <a name="visual-effects-rendering"></a>Composição de efeitos visuais

Em multimédia e entretenimento, o cluster do Avere vFXT pode acelerar o acesso aos dados para projetos de composição prementes. Uma vez que pode adicionar mais espaço de cache e nós de computação no Azure, tem a flexibilidade para lidar com grandes projetos com eficiência.

### <a name="life-sciences"></a>Ciências da vida

Avere vFXT permite que os investigadores executem fluxos de trabalho de análise secundária em Azure Compute, e acedam a dados genômicos independentemente da sua localização.

Na investigação farmacêutica, os clusters Avere vFXT podem acelerar a descoberta de fármacos, ajudando os investigadores a prever interações de alvos de drogas e a analisar dados de investigação.

### <a name="financial-services-analytics"></a>Análise de serviços financeiros

Um cluster do Avere vFXT pode ajudar a acelerar cálculos de análise quantitativa, o que fornece às empresas de serviços financeiros uma melhor visão para tomar decisões estratégicas.

## <a name="features-and-specifications"></a>Funcionalidades e especificações

O sistema Avere vFXT é constituído por três ou mais nós periféricos virtuais, configurados num cluster. Pode estar situado perto das máquinas cliente que montam o cluster em vez do armazenamento diretamente.

O cluster do Avere vFXT coloca os ficheiros na cache à medida que são pedidos. Os pedidos repetidos podem ser servidos a partir da cache mais de 80% do tempo.

### <a name="compatibility"></a>Compatibilidade

* Compatível com os sistemas NAS do hardware da NetApp ou da Dell EMC Isilon
* Compatível com o Blob do Azure
* Utiliza o protocolo NFSv3 ou SMB2

Avere vFXT para Azure utiliza os seguintes recursos Azure:

|Componente do Azure| Recurso |
|----------|-----------|
|Máquinas virtuais|3 ou mais E32s_v3|
|Armazenamento SSD Premium|200 GB de espaço para SO e 1 a 4 TB de espaço na cache por nó |
|Conta de armazenamento (opcional) |v2|
|Armazenamento de back-end de dados (opcional) | Um contentor de Blobs LRS vazio |

## <a name="next-steps"></a>Passos seguintes

Leia estes artigos para planear e criar o seu próprio Avere vFXT para implantação do Azure.

* [Planeie o seu sistema](avere-vfxt-deploy-plan.md)
* [Descrição geral da implementação](avere-vfxt-deploy-overview.md)
* [Criar o vFXT](avere-vfxt-deploy.md)
