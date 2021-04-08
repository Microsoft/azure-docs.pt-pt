---
title: Afinação do cluster Avere vFXT - Azure
description: Saiba mais sobre algumas das afinações personalizadas para clusters vFXT em Avere vFXT para Azure que você pode fazer, trabalhando com um representante de suporte.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 5d9f81c9438cb992f81bd3e6319532d67db75552
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272387"
---
# <a name="cluster-tuning"></a>Otimização do cluster

A maioria dos clusters vFXT pode beneficiar de configurações de desempenho personalizadas. Estas definições ajudam o cluster a trabalhar melhor com o seu fluxo de trabalho particular, conjunto de dados e ferramentas.

Esta personalização deve ser feita com a ajuda de um representante de suporte, pois pode envolver funcionalidades configurantes que não estão disponíveis no Painel de Controlo de Avere.

Esta secção descreve algumas das afinações personalizadas que podem ser feitas.

## <a name="general-optimizations"></a>Otimizações gerais

Estas alterações podem ser recomendadas com base nas qualidades do conjunto de dados ou no estilo de fluxo de trabalho.

* Se a carga de trabalho for pesada, aumente o tamanho da cache de escrita a partir do seu padrão de 20%.
* Se o conjunto de dados envolver muitos ficheiros pequenos, aumente o limite de contagem de ficheiros da cache de cluster.
* Se o trabalho envolver a cópia ou a movimentação de dados entre dois repositórios, ajuste o número de fios utilizados para os dados em movimento:
  * Para aumentar a velocidade, pode aumentar o número de fios paralelos utilizados.
  * Se o volume de armazenamento de back-end estiver a ficar sobrecarregado, poderá ser necessário diminuir o número de fios paralelos utilizados.
* Se o cluster caches dados para um ficheiro principal que utiliza NFSv4 ACLs, permitir o cache do modo de acesso para simplificar a autorização de ficheiros para determinados clientes.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Otimizações cloud NAS ou cloud gateway

Num cenário de NAS ou gateway em nuvem, o cluster vFXT proporciona acesso ao estilo NAS a um recipiente de nuvem. Para tirar partido de velocidades de dados mais elevadas entre o cluster vFXT e o armazenamento em nuvem, o seu representante pode recomendar a alteração das definições para empurrar mais agressivamente os dados para o volume de armazenamento a partir da cache. Por exemplo:

* Aumentar o número de ligações TCP entre o cluster e o contentor de armazenamento

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Otimizações de WAN em nuvem ou híbridas

Num cenário de explosão em nuvem ou cenário de otimização de armazenamento híbrido WAN, o cluster vFXT proporciona integração entre o armazenamento de hardware na nuvem e no local. Estas alterações podem ser úteis:

* Aumentar o número de ligações TCP permitidas entre o cluster e o ficheiro principal
* Ativar a definição de otimização WAN para o ficheiro de núcleo remoto (Esta definição pode ser usada para um ficheiro remoto no local ou para um ficheiro de núcleo de nuvem numa região de Azure diferente.)
* Aumente o tamanho do tampão da tomada TCP<sup>*</sup>
* Ative a definição "sempre para a frente" para reduzir os ficheiros em cache redundante<sup>*</sup>

<sup>*</sup>Estes ajustes podem não se aplicar a todos os sistemas, dependendo das necessidades de carga de trabalho e de desempenho.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Ajude a otimizar o seu Avere vFXT para Azure

Para contactar o pessoal de apoio sobre estas otimizações, utilize o procedimento descrito no [Get help with your system](avere-vfxt-open-ticket.md).
