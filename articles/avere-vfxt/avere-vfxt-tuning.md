---
title: Avere vFXT afinação de cluster - Azure
description: Visão geral das configurações personalizadas para otimizar o desempenho em Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152940"
---
# <a name="cluster-tuning"></a>Otimização do cluster

A maioria dos clusters vFXT pode beneficiar de configurações de desempenho personalizadas. Estas definições ajudam o cluster a funcionar melhor com o seu fluxo de trabalho, conjunto de dados e ferramentas específicas.

Esta personalização deve ser feita com a ajuda de um representante de suporte, pois pode envolver a configuração de funcionalidades que não estão disponíveis no Painel de Controlo de Avere.

Esta secção descreve algumas das afinações personalizadas que podem ser feitas.

## <a name="general-optimizations"></a>Otimizações gerais

Estas alterações podem ser recomendadas com base em qualidades de conjunto de dados ou estilo de fluxo de trabalho.

* Se a carga de trabalho for pesada, aumente o tamanho da cache de escrita a partir do seu padrão de 20%.
* Se o conjunto de dados envolver muitos pequenos ficheiros, aumente o limite de contagem de ficheiros do conjunto cache.
* Se o trabalho envolver a cópia ou a deslocação de dados entre dois repositórios, ajuste o número de fios utilizados para a movimentação de dados:
  * Para aumentar a velocidade, pode aumentar o número de fios paralelos utilizados.
  * Se o volume de armazenamento traseiro estiver a ficar sobrecarregado, poderá ter de diminuir o número de fios paralelos utilizados.
* Se o cluster caches dados para um ficheiro principal que utiliza OS NFSv4 ACLs, ative o modo de acesso para simplificar a autorização de ficheiro para determinados clientes.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Otimizações de cloud NAS ou cloud gateway

Num cenário de nas nuvem ou gateway, o cluster vFXT fornece acesso ao estilo NAS a um recipiente de nuvem. Para tirar partido de velocidades de dados mais elevadas entre o cluster vFXT e o armazenamento em nuvem, o seu representante pode recomendar a alteração das definições para empurrar os dados de forma mais agressiva para o volume de armazenamento a partir da cache. Por exemplo:

* Aumentar o número de ligações de TCP entre o cluster e o recipiente de armazenamento

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Cloud bursting ou otimizações híbridas WAN

Num cenário de rebentamento de nuvens ou cenário de otimização de WAN de armazenamento híbrido, o cluster vFXT proporciona integração entre o armazenamento de hardware em nuvem e no local. Estas alterações podem ser úteis:

* Aumentar o número de ligações TCP permitidas entre o cluster e o filer central
* Ativar a definição de otimização WAN para o filer de núcleo remoto (Esta definição pode ser utilizada para um filer remoto no local ou um filer de núcleo de nuvem numa região azure diferente.)
* Aumentar o tamanho do tampão da tomada TCP<sup>*</sup>
* Ativar a definição "sempre para a frente" para reduzir ficheiros com cache redundante<sup>*</sup>

<sup>*</sup>Estes ajustes podem não se aplicar a todos os sistemas, dependendo da carga de trabalho e das necessidades de desempenho.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Ajude a otimizar o seu Avere vFXT para o Azure

Para contactar a equipa de suporte sobre estas otimizações, utilize o procedimento descrito no [Get help with your system](avere-vfxt-open-ticket.md).
