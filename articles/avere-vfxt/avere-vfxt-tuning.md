---
title: Avere vFXT cluster ajuste - Azure
description: Descrição geral de configurações personalizadas para otimizar o desempenho no Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f5e780dcab20befe19ca34020908eee93c290516
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409172"
---
# <a name="cluster-tuning"></a>Otimização de cluster


A maioria dos clusters de vFXT podem beneficiar de definições de desempenho personalizados. Estas definições ajudam a cluster a funcionar melhor com o seu fluxo de trabalho específico, conjunto de dados e ferramentas. 

Essa personalização deve ser feita em conjunto com um representante de suporte, porque, normalmente, envolve a configuração das funcionalidades que não estão disponíveis no painel de controle Avere.

Esta secção explica algumas das personalizada de otimização que podem ser feitas.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Otimizações gerais

Estas alterações podem ser recomendadas com base em qualidades de conjunto de dados ou o estilo de fluxo de trabalho.

* Se a carga de trabalho pesadas de escrita, aumente o tamanho da cache de escrita da sua predefinição de 20%. 
* Se o conjunto de dados envolve muitos arquivos pequenos, aumente o limite de contagem de ficheiros da cache de cluster. 
* Se o trabalho envolve copiar ou mover dados entre dois repositórios, ajuste o número de threads utilizados para mover dados: 
  * Para aumentar a velocidade, pode aumentar o número de threads paralelos utilizado.
  * Se o volume de armazenamento de back-end está a ficar sobrecarregado, poderá ter reduzir o número de threads paralelos utilizado.
* Se o cluster coloca em cache os dados para um filtro de núcleos que utilize ACLs de NFSv4, ative o modo de acesso de colocação em cache para simplificar a autorização de ficheiros para clientes específicos.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Na cloud NAS ou otimizações de gateway de nuvem

Para tirar partido das maiores velocidades de dados entre o armazenamento de cluster e cloud vFXT num cenário NAS ou o gateway de cloud (em que o cluster vFXT fornece acesso de estilo para um contentor de cloud), seu representante poderá recomendar a alterar as definições como esses para muito mais agressivamente enviar dados por push para o volume de armazenamento da cache:

* Aumentar o número de conexões TCP entre o cluster e o contentor de armazenamento
* Diminuir o valor de tempo limite REST para a comunicação entre o cluster e o armazenamento para repetir a escrita mais cedo se forem imediatamente não bem sucedidos  
* Aumentar o tamanho do segmento, para que as transferências de segmento de escrita de cada back-end, uma parte de 8 MB de dados em vez de 1 MB

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Segurança da cloud ou otimizações de WAN híbrida

Numa cloud bursting cenário ou cenário de otimização de WAN de armazenamento híbrido (em que o cluster vFXT fornece integração entre a cloud e armazenamento de hardware no local), estas alterações podem ser úteis:

* Aumentar o número de ligações de TCP permitido entre o cluster e o filtro de núcleo
* Ativar a definição de otimização de WAN para o filtro de núcleo remoto (esta definição pode ser utilizada para um filtro de local remoto ou um filtro de núcleos na cloud numa região do Azure diferente.)
* Aumentar o tamanho de memória intermédia de socket TCP (consoante as necessidades de desempenho e carga de trabalho)
* Ativar a definição "sempre reencaminhar" reduzir os ficheiros de forma redundante em cache (consoante as necessidades de desempenho e carga de trabalho)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Ajudar a otimizar sua vFXT Avere para o Azure

Utilize o procedimento descrito em [obter ajuda com o seu sistema](avere-vfxt-open-ticket.md) contactem os técnicos de suporte sobre estas otimizações. 