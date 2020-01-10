---
title: Latência no armazenamento de BLOBs-armazenamento do Azure
description: Entenda e meça a latência para operações de armazenamento de BLOBs e saiba como projetar seus aplicativos de armazenamento de BLOBs para baixa latência.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749720"
---
# <a name="latency-in-blob-storage"></a>Latência no Armazenamento de blobs

Latência, às vezes referenciada como tempo de resposta, é a quantidade de tempo que um aplicativo deve aguardar até que uma solicitação seja concluída. A latência pode afetar diretamente o desempenho de um aplicativo. A baixa latência costuma ser importante para cenários com seres humanos no loop, como a condução de transações de cartão de crédito ou o carregamento de páginas da Web. Os sistemas que precisam processar eventos de entrada a altas taxas, como log de telemetria ou eventos de IoT, também exigem baixa latência. Este artigo descreve como entender e medir a latência de operações em blobs de blocos e como projetar seus aplicativos para baixa latência.

O armazenamento do Azure oferece duas opções de desempenho diferentes para BLOBs de blocos: Premium e Standard. Os blobs de blocos Premium oferecem latência significativamente menor e mais consistente do que os blobs de blocos padrão por meio de discos SSD de alto desempenho. Para obter mais informações, consulte **armazenamento de blobs de blocos de desempenho premium** no [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](storage-blob-storage-tiers.md).

## <a name="about-azure-storage-latency"></a>Sobre a latência de armazenamento do Azure

A latência de armazenamento do Azure está relacionada a taxas de solicitação para operações de armazenamento do Azure. As taxas de solicitação também são conhecidas como operações de entrada/saída por segundo (IOPS).

Para calcular a taxa de solicitação, primeiro determine o período de tempo que cada solicitação leva para ser concluída e, em seguida, calcule quantas solicitações podem ser processadas por segundo. Por exemplo, suponha que uma solicitação leva de 50 milissegundos (MS) para ser concluída. Um aplicativo que usa um thread com uma operação pendente de leitura ou gravação deve atingir 20 IOPS (1 segundo ou 1000 MS/50 ms por solicitação). Teoricamente, se a contagem de threads for dupla para duas, o aplicativo deverá ser capaz de alcançar 40 IOPS. Se as operações de leitura ou gravação assíncronas pendentes para cada thread forem duplicadas para duas, o aplicativo deverá ser capaz de alcançar 80 IOPS.

Na prática, as taxas de solicitação nem sempre são dimensionadas de forma linear, devido à sobrecarga no cliente do agendamento de tarefas, alternância de contexto e assim por diante. No lado do serviço, pode haver variabilidade em latência devido à pressão no sistema de armazenamento do Azure, diferenças na mídia de armazenamento usada, ruído de outras cargas de trabalho, tarefas de manutenção e outros fatores. Por fim, a conexão de rede entre o cliente e o servidor pode afetar a latência de armazenamento do Azure devido ao congestionamento, redirecionamento ou outras interrupções.

A largura de banda do armazenamento do Azure, também conhecida como taxa de transferência, está relacionada à taxa de solicitação e pode ser calculada multiplicando a taxa de solicitação (IOPS) pelo tamanho da solicitação. Por exemplo, supondo 160 solicitações por segundo, cada 256 KiB de dados resulta em taxa de transferência de 40.960 KiB por segundo ou 40 MiB por segundo.

## <a name="latency-metrics-for-block-blobs"></a>Métricas de latência para BLOBs de bloco

O armazenamento do Azure fornece duas métricas de latência para BLOBs de blocos. Essas métricas podem ser exibidas no portal do Azure:

- A **latência de ponta a ponta (e2e)** mede o intervalo de quando o armazenamento do Azure recebe o primeiro pacote da solicitação até que o armazenamento do Azure receba uma confirmação do cliente no último pacote da resposta.

- A **latência do servidor** mede o intervalo de quando o armazenamento do Azure recebe o último pacote da solicitação até que o primeiro pacote da resposta seja retornado do armazenamento do Azure.

A imagem a seguir mostra a **latência média de E2E de sucesso** e a **latência média do servidor de êxito** para uma carga de trabalho de exemplo que chama a operação de `Get Blob`:

![Captura de tela mostrando métricas de latência para operação de obtenção de BLOB](media/storage-blobs-latency/latency-metrics-get-blob.png)

Em condições normais, há pouco espaço entre a latência de ponta a ponta e a latência do servidor, que é a imagem mostrada para a carga de trabalho de exemplo.

Se você examinar suas métricas de latência de servidor e de ponta a ponta, e descobrir que a latência de ponta a ponta é significativamente maior do que a latência do servidor, investigue e resolva a origem da latência adicional.

Se sua latência de ponta a ponta e de servidor for semelhante, mas você precisar de latência mais baixa, considere migrar para o armazenamento de blob de blocos Premium.

## <a name="factors-influencing-latency"></a>Fatores que influenciam a latência

O fator principal que influencia a latência é o tamanho da operação. Leva mais tempo para concluir operações maiores, devido à quantidade de dados sendo transferidos pela rede e processada pelo armazenamento do Azure.

O diagrama a seguir mostra o tempo total para operações de vários tamanhos. Para pequenas quantidades de dados, o intervalo de latência é basicamente gasto tratando a solicitação, em vez de transferir dados. O intervalo de latência aumenta apenas um pouco assim que o tamanho da operação aumenta (marcado 1 no diagrama abaixo). À medida que aumenta ainda mais o tamanho da operação, mais tempo é gasto na transferência de dados, de modo que o intervalo de latência total seja dividido entre a manipulação de solicitação e a transferência de dados (marcado 2 no diagrama abaixo). Com tamanhos de operação maiores, o intervalo de latência é quase exclusivamente gasto na transferência de dados e o tratamento de solicitação é basicamente insignificante (marcado 3 no diagrama abaixo).

![Captura de tela mostrando o tempo total de operação por tamanho da operação](media/storage-blobs-latency/operation-time-size-chart.png)

Os fatores de configuração do cliente, como simultaneidade e threading, também afetam a latência. A taxa de transferência geral depende de quantas solicitações de armazenamento estão em trânsito em qualquer momento determinado e sobre como seu aplicativo lida com threads. Os recursos do cliente, incluindo CPU, memória, armazenamento local e interfaces de rede, também podem afetar a latência.

O processamento de solicitações de armazenamento do Azure requer recursos de CPU e memória do cliente. Se o cliente estiver sob pressão devido a uma máquina virtual de baixo consumo ou a algum processo de fuga no sistema, haverá menos recursos disponíveis para processar as solicitações de armazenamento do Azure. Qualquer contenção ou falta de recursos do cliente resultará em um aumento na latência de ponta a ponta sem um aumento na latência do servidor, aumentando a lacuna entre as duas métricas.

Igualmente importante é a interface de rede e o pipe de rede entre o cliente e o armazenamento do Azure. A distância física sozinha pode ser um fator significativo, por exemplo, se uma VM de cliente estiver em uma região do Azure diferente ou local. Outros fatores, como saltos de rede, roteamento de ISP e estado da Internet, podem influenciar a latência geral do armazenamento.

Para avaliar a latência, primeiro estabeleça métricas de linha de base para seu cenário. As métricas de linha de base fornecem a latência de ponta a ponta e de servidor esperada no contexto do seu ambiente de aplicativo, dependendo do perfil de carga de trabalho, das definições de configuração do aplicativo, dos recursos do cliente, do pipe de rede e de outros fatores. Quando você tem métricas de linha de base, pode identificar com mais facilidade as condições anormais versus normais. As métricas de linha de base também permitem que você observe os efeitos dos parâmetros alterados, como a configuração de aplicativo ou os tamanhos de VM.

## <a name="next-steps"></a>Passos seguintes

- [Escalabilidade e metas de desempenho para o armazenamento de BLOBs](scalability-targets.md)
- [Lista de verificação de desempenho e escalabilidade para armazenamento de BLOBs](storage-performance-checklist.md)
