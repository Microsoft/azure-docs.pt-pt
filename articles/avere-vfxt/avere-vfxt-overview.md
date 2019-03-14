---
title: Avere vFXT for Azure
description: Introdução ao Avere vFXT for Azure, uma camada de cache na cloud para HPC
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 724352ae9f7c66dfeab293473ea79438e3b36254
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56989324"
---
# <a name="what-is-avere-vfxt-for-azure"></a>O que é o Avere vFXT for Azure? 

O Avere vFXT for Azure é uma solução de colocação na cache do sistema de ficheiros para tarefas de computação de alto desempenho (HPC) intensivas em termos de dados. Permite tirar partido da escalabilidade de informática na cloud para tornar os seus dados acessíveis quando e onde forem necessários, até mesmo para dados armazenados no seu próprio hardware no local.

O Avere vFXT suporta os seguintes cenários de computação comuns: 

* Arquitetura de nuvem híbrida: Avere vFXT para o Azure pode trabalhar com um sistema de armazenamento de hardware, que oferece o benefício de computação em nuvem sem ter de mover ficheiros. 
* Segurança da cloud: Avere vFXT para o Azure pode ajudá-lo a mover seus dados para a cloud para um único projeto, ou "lift- and -shift" todo o fluxo de trabalho permanentemente. 

![Diagrama que mostra os detalhes do sistema Avere vFXT numa subscrição do Azure ligada ao armazenamento de Blobs e a um datacenter no local](media/avere-vfxt-hybrid.png)

O Avere vFXT for Azure é mais adequado para as seguintes situações: 

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

O Avere vFXT pode permitir que os investigadores executem os fluxos de trabalho de análise secundários na Computação do Azure e acedam a dados de genoma, independentemente da respetiva localização.

Na investigação farmacêutica, os clusters do Avere vFXT podem ser utilizados para agilizar a descoberta de medicamentos ao ajudar os investigadores a prever as interações com os medicamentos e a analisar os dados de investigação.

### <a name="financial-services-analytics"></a>Análise de serviços financeiros

Um cluster do Avere vFXT pode ajudar a acelerar cálculos de análise quantitativa, o que fornece às empresas de serviços financeiros uma melhor visão para tomar decisões estratégicas. 

## <a name="features-and-specifications"></a>Funcionalidades e especificações

O sistema Avere vFXT é constituído por três ou mais nós periféricos virtuais, configurados num cluster. Pode estar situado perto das máquinas cliente que montam o cluster em vez do armazenamento diretamente. 

O cluster do Avere vFXT coloca os ficheiros na cache à medida que são pedidos. Os pedidos repetidos podem ser servidos a partir da cache mais de 80% do tempo.

### <a name="compatibility"></a>Compatibilidade 

* Compatível com os sistemas NAS do hardware da NetApp ou da Dell EMC Isilon
* Compatível com o Blob do Azure
* Utiliza o protocolo NFSv3 ou SMB2

O Avere vFXT utiliza os seguintes recursos do Azure: 

|Componente do Azure|   |
|----------|-----------|
|Máquinas virtuais|E32s_v3 3 ou mais|
|Armazenamento SSD Premium|200 GB de espaço para SO e 1 a 4 TB de espaço na cache por nó |
|Conta de armazenamento (opcional) |v2|
|Armazenamento de back-end de dados (opcional) | Um contentor de Blobs LRS vazio |

## <a name="next-steps"></a>Passos Seguintes

Seguem-se algumas ligações para começar a criar a sua própria implementação do Avere vFXT. 

* [Planear o sistema](avere-vfxt-deploy-plan.md)
* [Descrição geral da implementação](avere-vfxt-deploy-overview.md)
* [Criar o vFXT](avere-vfxt-deploy.md)
