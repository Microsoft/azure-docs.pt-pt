---
title: Latência no armazenamento blob - Azure Storage
description: Compreenda e meça a latência para as operações de armazenamento blob e aprenda a desenhar as suas aplicações de armazenamento Blob para baixa latência.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75749720"
---
# <a name="latency-in-blob-storage"></a>Latência no Armazenamento de blobs

A latência, por vezes referida como tempo de resposta, é o tempo que um pedido deve esperar para que um pedido seja concluído. A latência pode afetar diretamente o desempenho de uma aplicação. A baixa latência é muitas vezes importante para cenários com humanos no loop, como a realização de transações de cartões de crédito ou o carregamento de páginas web. Os sistemas que precisam de processar eventos de entrada a taxas elevadas, como a exploração de telemetria ou eventos de IoT, também requerem baixa latência. Este artigo descreve como compreender e medir a latência para operações em blobs de blocos, e como desenhar as suas aplicações para baixa latência.

O Azure Storage oferece duas opções de desempenho diferentes para blobs de bloco: premium e standard. As bolhas de bloco premium oferecem uma latência significativamente mais baixa e mais consistente do que as bolhas de bloco padrão através de discos SSD de alto desempenho. Para obter mais informações, consulte **o armazenamento de blob de bloco de desempenho Premium** no armazenamento [Azure Blob: níveis de acesso quentes, frescos e de arquivo](storage-blob-storage-tiers.md).

## <a name="about-azure-storage-latency"></a>Sobre a latência do armazenamento Azure

A latência do Armazenamento Azure está relacionada com as taxas de pedido para as operações de armazenamento Azure. As taxas de pedido também são conhecidas como operações de entrada/saída por segundo (IOPS).

Para calcular a taxa de pedido, primeiro determinar o tempo que cada pedido demora a completar, depois calcular quantos pedidos podem ser processados por segundo. Por exemplo, assuma que um pedido leva 50 milissegundos (ms) para ser concluído. Uma aplicação utilizando um fio com uma operação de leitura ou escrita pendente deve atingir 20 IOPS (1 segundo ou 1000 ms / 50 ms por pedido). Teoricamente, se a contagem de fios for duplicada para dois, então a aplicação deve ser capaz de alcançar 40 IOPS. Se as operações de leitura ou escrita assíncroneas pendentes para cada fio forem duplicadas para duas, então a aplicação deve ser capaz de alcançar 80 IOPS.

Na prática, as taxas de pedido nem sempre escalam tão linearmente, devido à sobrecarga no cliente a partir do agendamento de tarefas, comutação de contexto, e assim por diante. Do lado do serviço, pode haver variabilidade na latência devido à pressão sobre o sistema de armazenamento Azure, diferenças nos suportes de armazenamento utilizados, ruído de outras cargas de trabalho, tarefas de manutenção e outros fatores. Finalmente, a ligação de rede entre o cliente e o servidor pode afetar a latência do Armazenamento Azure devido a congestionamento, reencaminhamento ou outras perturbações.

A largura de banda do Azure Storage, também referida como produção, está relacionada com a taxa de pedido e pode ser calculada multiplicando a taxa de pedido (IOPS) pela dimensão do pedido. Por exemplo, assumindo 160 pedidos por segundo, cada 256 KiB de dados resulta num resultado de 40.960 KiB por segundo ou 40 MiB por segundo.

## <a name="latency-metrics-for-block-blobs"></a>Métricas de latência para bolhas de bloco

O Azure Storage fornece duas métricas de latência para bolhas de bloco. Estas métricas podem ser vistas no portal Azure:

- **A latência de ponta a ponta (E2E)** mede o intervalo a partir do momento em que o Azure Storage recebe o primeiro pacote do pedido até que o Azure Storage receba um reconhecimento do cliente no último pacote da resposta.

- **A latência** do servidor mede o intervalo a partir do momento em que o Azure Storage recebe o último pacote do pedido até que o primeiro pacote da resposta seja devolvido do Azure Storage.

A imagem a seguir mostra a Latência média do **servidor de sucesso E2E** e **o servidor de sucesso médio** para uma amostra de carga de trabalho que chama a `Get Blob` operação:

![Screenshot mostrando métricas de latência para obter operação Blob](media/storage-blobs-latency/latency-metrics-get-blob.png)

Em condições normais, há pouco espaço entre a latência de ponta a ponta e a latência do servidor, que é o que a imagem mostra para a carga de trabalho da amostra.

Se analisar as suas métricas de latência de ponta a ponta e servidor, e descobrir que a latência de ponta a ponta é significativamente maior do que a latência do servidor, então investigue e aborde a origem da latência adicional.

Se a sua latência de ponta a ponta e servidor for semelhante, mas necessitar de menor latência, então considere migrar para o armazenamento de blob de bloco premium.

## <a name="factors-influencing-latency"></a>Fatores que influenciam a latência

O principal fator que influencia a latência é o tamanho da operação. Demora mais tempo a concluir operações maiores, devido à quantidade de dados que são transferidos através da rede e processados pela Azure Storage.

O diagrama seguinte mostra o tempo total para operações de vários tamanhos. Para pequenas quantidades de dados, o intervalo de latência é predominantemente gasto a lidar com o pedido, em vez de transferir dados. O intervalo de latência aumenta apenas ligeiramente à medida que o tamanho da operação aumenta (marcado 1 no diagrama abaixo). À medida que o tamanho da operação aumenta, mais tempo é gasto na transferência de dados, de modo que o intervalo total de latência é dividido entre o tratamento de pedidos e a transferência de dados (marcado 2 no diagrama abaixo). Com tamanhos de funcionamento maiores, o intervalo de latência é quase exclusivamente gasto na transferência de dados e o tratamento do pedido é em grande parte insignificante (marcado 3 no diagrama abaixo).

![Screenshot mostrando o tempo total de operação pelo tamanho da operação](media/storage-blobs-latency/operation-time-size-chart.png)

Os fatores de configuração do cliente, tais como a concordância e a rosca, também afetam a latência. A produção geral depende de quantos pedidos de armazenamento estão em voo em qualquer momento e de como a sua aplicação lida com a rosca. Os recursos dos clientes, incluindo CPU, memória, armazenamento local e interfaces de rede também podem afetar a latência.

Processar pedidos de Armazenamento Azure requer CPU cliente e recursos de memória. Se o cliente estiver sob pressão devido a uma máquina virtual desatada ou a algum processo de fuga no sistema, existem menos recursos disponíveis para processar pedidos de Armazenamento Azure. Qualquer contenção ou falta de recursos dos clientes resultará num aumento da latência de ponta a ponta sem um aumento da latência do servidor, aumentando o fosso entre as duas métricas.

Igualmente importante é a interface de rede e o tubo de rede entre o cliente e o Azure Storage. A distância física por si só pode ser um fator significativo, por exemplo, se um cliente VM estiver em uma região de Azure diferente ou no local. Outros fatores, como o lúpulo de rede, o encaminhamento ISP e o estado da Internet podem influenciar a latência geral do armazenamento.

Para avaliar a latência, primeiro estabeleça métricas de base para o seu cenário. As métricas de base fornecem-lhe a latência esperada de ponta a ponta e servidor no contexto do ambiente da sua aplicação, dependendo do seu perfil de carga de trabalho, configurações de configuração de aplicações, recursos do cliente, tubo de rede e outros fatores. Quando se tem métricas de base, pode identificar mais facilmente condições anormais versus normais. As métricas de base também permitem observar os efeitos de parâmetros alterados, tais como configuração de aplicação ou tamanhos VM.

## <a name="next-steps"></a>Passos seguintes

- [Metas de escalabilidade e desempenho para armazenamento blob](scalability-targets.md)
- [Lista de verificação de desempenho e escalabilidade para armazenamento blob](storage-performance-checklist.md)
