---
title: Ajuste de cluster avere vFXT-Azure
description: Visão geral das configurações personalizadas para otimizar o desempenho no avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152940"
---
# <a name="cluster-tuning"></a>Ajuste de cluster

A maioria dos clusters vFXT pode se beneficiar das configurações de desempenho personalizadas. Essas configurações ajudam o cluster a funcionar melhor com seu fluxo de trabalho, conjunto de tarefas e ferramentas específicos.

Essa personalização deve ser feita com a ajuda de um representante de suporte, pois ela pode envolver a configuração de recursos que não estão disponíveis no painel de controle do avere.

Esta seção descreve alguns dos ajustes personalizados que podem ser feitos.

## <a name="general-optimizations"></a>Otimizações gerais

Essas alterações podem ser recomendadas com base nas qualidades ou no estilo do fluxo de trabalho.

* Se a carga de trabalho for de gravação intensa, aumente o tamanho do cache de gravação do padrão de 20%.
* Se o conjunto de conjuntos envolver muitos arquivos pequenos, aumente o limite de contagem de arquivos do cache do cluster.
* Se o trabalho envolve copiar ou mover dados entre dois repositórios, ajuste o número de threads usados para mover os dados:
  * Para aumentar a velocidade, você pode aumentar o número de threads paralelos usados.
  * Se o volume de armazenamento de back-end estiver ficando sobrecarregado, talvez seja necessário diminuir o número de threads paralelos usados.
* Se o cluster armazena dados em cache para um Filer principal que usa ACLs de NFSv4, habilite o cache do modo de acesso para simplificar a autorização de arquivos para clientes específicos.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Otimizações de NAS de nuvem ou de gateway de nuvem

Em um cenário de NAS ou gateway na nuvem, o cluster vFXT fornece acesso em estilo NAS a um contêiner de nuvem. Para aproveitar as maiores velocidades de dados entre o cluster vFXT e o armazenamento em nuvem, seu representante pode recomendar a alteração de configurações para enviar dados de forma mais agressiva para o volume de armazenamento do cache. Por exemplo:

* Aumentar o número de conexões TCP entre o cluster e o contêiner de armazenamento

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Interconexões de WAN híbrida ou de intermitência de nuvem

Em cenários de intermitência de nuvem ou cenário de otimização de WAN de armazenamento híbrido, o cluster vFXT fornece integração entre o armazenamento de hardware local e na nuvem. Essas alterações podem ser úteis:

* Aumentar o número de conexões TCP permitidas entre o cluster e o filer principal
* Habilite a configuração de otimização de WAN para o filer de núcleo remoto (essa configuração pode ser usada para um Filer remoto local ou um Filer do Cloud Core em uma região diferente do Azure.)
* Aumentar o tamanho do buffer de soquete TCP<sup>*</sup>
* Habilite a configuração "sempre encaminhar" para reduzir arquivos armazenados em cache com redundância<sup>*</sup>

<sup>*</sup> Esses ajustes podem não se aplicar a todos os sistemas, dependendo das necessidades de carga de trabalho e desempenho.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Ajude a otimizar seu avere vFXT para o Azure

Para entrar em contato com a equipe de suporte sobre essas otimizações, use o procedimento descrito em [obter ajuda com o sistema](avere-vfxt-open-ticket.md).
