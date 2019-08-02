---
title: Ajuste de cluster avere vFXT-Azure
description: Visão geral das configurações personalizadas para otimizar o desempenho no avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 17e55dbe84cda87ee902c94e0024c9a3aad8b31b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698329"
---
# <a name="cluster-tuning"></a>Ajuste de cluster


A maioria dos clusters vFXT pode se beneficiar das configurações de desempenho personalizadas. Essas configurações ajudam o cluster a funcionar melhor com seu fluxo de trabalho, conjunto de tarefas e ferramentas específicos. 

Essa personalização deve ser feita junto com um representante de suporte, porque geralmente envolve a configuração de recursos que não estão disponíveis no painel de controle do avere.

Esta seção explica alguns dos ajustes personalizados que podem ser feitos.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Otimizações gerais

Essas alterações podem ser recomendadas com base nas qualidades ou no estilo do fluxo de trabalho.

* Se a carga de trabalho for de gravação intensa, aumente o tamanho do cache de gravação do padrão de 20%. 
* Se o conjunto de conjuntos envolver muitos arquivos pequenos, aumente o limite de contagem de arquivos do cache do cluster. 
* Se o trabalho envolve copiar ou mover dados entre dois repositórios, ajuste o número de threads usados para mover os dados: 
  * Para aumentar a velocidade, você pode aumentar o número de threads paralelos usados.
  * Se o volume de armazenamento de back-end estiver ficando sobrecarregado, talvez seja necessário diminuir o número de threads paralelos usados.
* Se o cluster armazena dados em cache para um Filer principal que usa ACLs de NFSv4, habilite o cache do modo de acesso para simplificar a autorização de arquivos para clientes específicos.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Otimizações de NAS de nuvem ou de gateway de nuvem

Para aproveitar as maiores velocidades de dados entre o cluster vFXT e o armazenamento em nuvem em um cenário de NAS ou gateway de nuvem (em que o cluster vFXT fornece acesso em estilo NAS a um contêiner de nuvem), seu representante pode recomendar a alteração de configurações como essas para mais Envie dados agressivamente para o volume de armazenamento do cache:

* Aumentar o número de conexões TCP entre o cluster e o contêiner de armazenamento

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Interconexões de WAN híbrida ou de intermitência de nuvem

Em um cenário de intermitência de nuvem ou cenário de otimização de WAN de armazenamento híbrido (em que o cluster vFXT fornece integração entre o armazenamento de hardware local e na nuvem), essas alterações podem ser úteis:

* Aumentar o número de conexões TCP permitidas entre o cluster e o filer principal
* Habilite a configuração de otimização de WAN para o filer de núcleo remoto (essa configuração pode ser usada para um Filer remoto local ou um Filer do Cloud Core em uma região diferente do Azure.)
* Aumentar o tamanho do buffer de soquete TCP (dependendo das necessidades de carga de trabalho e desempenho)
* Habilitar a configuração "sempre encaminhar" para reduzir arquivos armazenados em cache com redundância (dependendo das necessidades de carga de trabalho e desempenho)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Ajude a otimizar seu avere vFXT para o Azure

Use o procedimento descrito em [obter ajuda com o sistema](avere-vfxt-open-ticket.md) para entrar em contato com a equipe de suporte sobre essas otimizações. 