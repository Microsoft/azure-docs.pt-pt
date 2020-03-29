---
title: Azure Data Lake Storage Gen1 Storm performance guidelines [ Linhas de afinação de desempenho do Lago Azure] Microsoft Docs
description: Diretrizes de afinação de desempenho do desempenho do lago Azure Data Gen1
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 8066a759cf80be6e9ca232bcd3693a5fa4d2f2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436482"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Orientação de afinação de desempenho para tempestade em HDInsight e Azure Data Lake Storage Gen1

Compreenda os fatores que devem ser considerados quando afinar o desempenho de uma topologia da tempestade azure. Por exemplo, é importante compreender as características do trabalho feito pelos bicos e parafusos (se o trabalho é I/O ou memória intensiva). Este artigo abrange uma série de diretrizes de afinação de desempenho, incluindo problemas comuns de resolução de problemas.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta De Armazenamento de **Lago Azure Gen1.** Para obter instruções sobre como criar um, consulte Iniciar com [Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Um cluster Azure HDInsight** com acesso a uma conta Gen1 de Armazenamento de Data Lake. Consulte [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativa o Ambiente de Trabalho Remoto para o cluster.
* **Executando um aglomerado de tempestade em Data Lake Storage Gen1**. Para mais informações, consulte [Storm on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Diretrizes de afinação de desempenho sobre data lake storage Gen1**.  Para conceitos gerais de desempenho, consulte [Data Lake Storage Gen1 Performance Tuning Guidance](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

## <a name="tune-the-parallelism-of-the-topology"></a>Afinar o paralelismo da topologia

Você pode ser capaz de melhorar o desempenho aumentando a conmoeda do I/O de e para data Lake Storage Gen1. Uma topologia storm tem um conjunto de configurações que determinam o paralelismo:
* Número de processos de trabalhador (os trabalhadores são distribuídos uniformemente pelos VMs).
* Número de casos de executor de bico.
* Número de casos de executor de parafusos.
* Número de tarefas de bico.
* Número de tarefas de parafusos.

Por exemplo, num cluster com 4 VMs e 4 processos de trabalhador, 32 executores de bico e 32 tarefas de bico, e 256 executores de parafusos e 512 tarefas de parafusos, considere o seguinte:

Cada supervisor, que é um nó de trabalhador, tem um único trabalhador da máquina virtual Java (JVM). Este processo JVM gere 4 fios de bico e 64 fios de parafuso. Dentro de cada fio, as tarefas são executadas sequencialmente. Com a configuração anterior, cada fio de bico tem 1 tarefa, e cada fio de parafuso tem 2 tarefas.

Na Tempestade, aqui estão os vários componentes envolvidos, e como eles afetam o nível de paralelismo que você tem:
* O nó de cabeça (chamado Nimbus in Storm) é usado para submeter e gerir empregos. Estes nódosos não têm impacto no grau de paralelismo.
* O supervisor não acena. No HDInsight, isto corresponde a um nó de trabalhador Azure VM.
* As tarefas dos trabalhadores são processos de tempestade em execução nos VMs. Cada tarefa do trabalhador corresponde a uma instância JVM. A tempestade distribui o número de processos de trabalhador que especifica aos nós dos trabalhadores da forma mais uniforme possível.
* Spout e bolt executor casos. Cada instância executora corresponde a um fio que funciona dentro dos trabalhadores (JVMs).
* Tarefas de tempestade. Estas são tarefas lógicas que cada um destes fios executa. Isto não altera o nível de paralelismo, pelo que deve avaliar se precisa de múltiplas tarefas por executor ou não.

### <a name="get-the-best-performance-from-data-lake-storage-gen1"></a>Obtenha o melhor desempenho do Data Lake Storage Gen1

Ao trabalhar com data Lake Storage Gen1, obtém o melhor desempenho se fizer o seguinte:
* Adere os seus pequenos apêndices em tamanhos maiores (idealmente 4 MB).
* Faça o máximo de pedidos simultâneos que puder. Como cada fio de parafuso está a fazer leituras de bloqueio, você quer ter em algum lugar na gama de 8-12 fios por núcleo. Isto mantém o NIC e o CPU bem utilizados. Um VM maior permite pedidos mais simultâneos.  

### <a name="example-topology"></a>Exemplo de topologia

Vamos supor que tem um cluster de nó de 8 trabalhadores com um D13v2 Azure VM. Este VM tem 8 núcleos, por isso, entre os 8 nós operários, tem 64 núcleos totais.

Digamos que fazemos 8 fios por núcleo. Tendo em conta 64 núcleos, isso significa que queremos 512 instâncias executoras de parafusos totais (isto é, fios). Neste caso, digamos que começamos com um JVM por VM, e usamos principalmente a conmoeda de fio dentro do JVM para alcançar a conmoeda. Isto significa que precisamos de 8 tarefas de trabalhador (uma por VM Azure) e 512 executores de parafusos. Dada esta configuração, storm tenta distribuir os trabalhadores uniformemente através de nós de trabalhador (também conhecidos como nós de supervisor), dando a cada trabalhador nó 1 JVM. Agora dentro dos supervisores, Storm tenta distribuir os executores uniformemente entre supervisores, dando a cada supervisor (isto é, JVM) 8 fios cada.

## <a name="tune-additional-parameters"></a>Afinar parâmetros adicionais
Depois de ter a topologia básica, pode considerar se quer ajustar algum dos parâmetros:
* **Número de JVMs por nó de trabalhador.** Se tiver uma grande estrutura de dados (por exemplo, uma mesa de lookup) que hospeda na memória, cada JVM requer uma cópia separada. Em alternativa, pode utilizar a estrutura de dados em muitos fios se tiver menos JVMs. Para o I/O do parafuso, o número de JVMs não faz tanta diferença como o número de fios adicionados através desses JVMs. Para a simplicidade, é uma boa ideia ter um JVM por trabalhador. Dependendo do que o seu parafuso está a fazer ou do processamento de aplicações que necessita, no entanto, poderá ter de alterar este número.
* **Número de executores de bico.** Como o exemplo anterior utiliza parafusos para escrever para data Lake Storage Gen1, o número de bicos não é diretamente relevante para o desempenho do parafuso. No entanto, dependendo da quantidade de processamento ou I/O que acontece no bico, é uma boa ideia afinar os bicos para o melhor desempenho. Certifique-se de que tem bicos suficientes para manter os parafusos ocupados. As taxas de saída dos bicos devem corresponder à entrada dos parafusos. A configuração real depende do bico.
* **Número de tarefas.** Cada parafuso corre como um único fio. Tarefas adicionais por parafuso não fornecem qualquer conmoeda adicional. A única altura em que são benéficos é se o seu processo de reconhecimento da tuple levar uma grande parte do seu tempo de execução do parafuso. É uma boa ideia agrupar muitos tuples num apêndice maior antes de enviar um reconhecimento do parafuso. Assim, na maioria dos casos, várias tarefas não oferecem nenhum benefício adicional.
* **Agrupamento local ou baralhado.** Quando esta definição está ativada, os tuples são enviados para parafusos dentro do mesmo processo de trabalhador. Isto reduz as chamadas de comunicação inter-processo e de rede. Isto é recomendado para a maioria das topoologias.

Este cenário básico é um bom ponto de partida. Teste com os seus próprios dados para ajustar os parâmetros anteriores para obter um desempenho ótimo.

## <a name="tune-the-spout"></a>Sintonize o bico

Pode modificar as seguintes definições para afinar o bico.

- Tempo de tempo de **tuple: topologia.message.timeout.secs**. Esta definição determina o tempo que uma mensagem leva para completar e receber reconhecimento, antes de ser considerada falhada.

- **Memória máxima por processo de trabalhador: trabalhador.childopts**. Esta definição permite especificar parâmetros adicionais de linha de comando para os trabalhadores java. A configuração mais usada aqui é xmX, que determina a memória máxima atribuída a um monte de JVM.

- **Bico max pendente: topology.max.spout.pending**. Esta definição determina o número de tuples que podem ser voos (ainda não reconhecidos em todos os nós da topologia) por fio de bico a qualquer momento.

  Um bom cálculo a fazer é estimar o tamanho de cada um dos seus tuples. Então descubra a quantidade de memória que um fio de bico tem. A memória total atribuída a um fio, dividido por este valor, deve dar-lhe o limite superior para o parâmetro pendente do bico máximo.

## <a name="tune-the-bolt"></a>Sintonize o parafuso
Quando estiver a escrever para data Lake Storage Gen1, delineie uma política de sincronização de tamanho (tampão do lado do cliente) para 4 MB. Uma descarga ou hsync() só é realizada quando o tamanho do tampão for a este valor. O condutor do Data Lake Storage Gen1 no VM do trabalhador faz automaticamente este tampão, a menos que realize explicitamente um sincron().

O parafuso de armazenamento de tamanho sincronia de Dados Gen1 Storm tem um parâmetro de política de sincronização de tamanho (fileBufferSize) que pode ser usado para afinar este parâmetro.

Em topoologias intensivas em I/O, é uma boa ideia ter cada fio de parafuso escrito para o seu próprio arquivo, e definir uma política de rotação de ficheiros (fileRotationSize). Quando o ficheiro atinge um determinado tamanho, o fluxo é automaticamente lavado e um novo ficheiro é escrito. O tamanho do ficheiro recomendado para rotação é de 1 GB.

### <a name="handle-tuple-data"></a>Lidar com os dados de tuple

Em Storm, um bico agarra-se a uma tuple até ser explicitamente reconhecido pelo parafuso. Se um tuple foi lido pelo parafuso mas ainda não foi reconhecido, o bico pode não ter permanecido no backback do Data Lake Storage Gen1. Depois de um tuple ser reconhecido, o bico pode ser garantido persistência pelo parafuso, e pode então apagar os dados de origem de qualquer fonte de que esteja lendo.  

Para um melhor desempenho no Data Lake Storage Gen1, tenha o tampão de parafuso 4 MB de dados de tuple. Em seguida, escreva para o Data Lake Storage Gen1 back end como um 4-MB escrever. Depois de os dados terem sido escritos com sucesso na loja (chamando hflush(), o parafuso pode reconhecer os dados de volta ao bico. Isto é o que o exemplo fornecido aqui faz. Também é aceitável manter um maior número de tuples antes da chamada hflush() e os tuples reconhecidos. No entanto, isto aumenta o número de tuples em voo que o bico precisa de segurar, aumentando assim a quantidade de memória necessária por JVM.

> [!NOTE]
> As aplicações podem ter a obrigação de reconhecer tuples com mais frequência (em tamanhos de dados inferiores a 4 MB) por outras razões de não desempenho. No entanto, isso pode afetar a entrada de I/S na parte de trás do armazenamento. Pesar cuidadosamente esta troca contra o desempenho do I/S do parafuso.

Se a taxa de entrada de tuples não for elevada, então o tampão de 4 MB leva muito tempo a preencher, considere atenuar isto por:
* Reduzindo o número de parafusos, há menos tampão para preencher.
* Ter uma política baseada no tempo ou na contagem, em que um hflush() é desencadeado cada x flushes ou cada y milissegundos, e os tuples acumulados até agora são reconhecidos de volta.

Note que a entrada neste caso é mais baixa, mas com uma taxa lenta de eventos, a entrada máxima não é o maior objetivo de qualquer maneira. Estas mitigações ajudam-no a reduzir o tempo total que leva para que um tuple flua até à loja. Isto pode importar se você quiser um oleoduto em tempo real mesmo com uma baixa taxa de eventos. Note também que se a sua taxa de túnica de entrada for baixa, deve ajustar o parâmetro topology.message.timeout_secs, para que os tuples não se esgotem enquanto estão a ser tamponados ou processados.

## <a name="monitor-your-topology-in-storm"></a>Monitorize a sua topologia em Storm  
Enquanto a sua topologia está em execução, pode monitorizá-la na interface de utilizador storm. Aqui estão os principais parâmetros para olhar:

* **Latência total de execução do processo.** Este é o tempo médio que uma tuple leva para ser emitida pelo bico, processado pelo parafuso, e reconhecido.

* **Latência total do processo do parafuso.** Este é o tempo médio gasto pelo tuple no parafuso até receber um reconhecimento.

* **Bolt total executar latência.** Este é o tempo médio gasto pelo parafuso no método de execução.

* **Número de falhanços.** Isto refere-se ao número de tuples que não foram totalmente processados antes de serem esgotados.

* **Capacidade.** Esta é uma medida de como o seu sistema está ocupado. Se este número for 1, os seus parafusos estão a funcionar o mais rápido que podem. Se for menos de 1, aumente o paralelismo. Se for maior que 1, reduza o paralelismo.

## <a name="troubleshoot-common-problems"></a>Problemas comuns de resolução de problemas
Aqui estão alguns cenários comuns de resolução de problemas.
* **Muitas tuples estão a cronometrar.** Olhe cada nó na topologia para determinar onde está o estrangulamento. A razão mais comum para isso é que os parafusos não são capazes de acompanhar os bicos. Isto leva a que os tuples entupam os amortecedores internos enquanto esperam para serem processados. Considere aumentar o valor do tempo limite ou diminuir o bico máximo pendente.

* **Há uma elevada latência total de execução do processo, mas uma latência de processo de parafuso baixo.** Neste caso, é possível que os tuples não estejam a ser reconhecidos suficientemente depressa. Verifique se há um número suficiente de reconhecidores. Outra possibilidade é que eles estejam à espera na fila por muito tempo antes que os parafusos comecem a processá-los. Diminua o bico máximo pendente.

* **Há uma latência de execução de parafusos elevados.** Isto significa que o método de execução do seu parafuso está a demorar demasiado tempo. Otimize o código, ou olhe para os tamanhos de escrita e flush comportamento.

### <a name="data-lake-storage-gen1-throttling"></a>Estrangulamento de Armazenamento de Lago de Dados Gen1
Se atingir os limites de largura de banda fornecidos pela Data Lake Storage Gen1, poderá ver falhas de tarefa. Verifique os registos de tarefas para verificar se há erros de estrangulamento. Pode diminuir o paralelismo aumentando o tamanho do recipiente.    

Para verificar se está a ser estrangulado, ative o depurador de registo do lado do cliente:

1. Em **Ambari** > **Storm** > **Config** > Advanced**storm-worker-log4j**, altere ** &lt;o nível de raiz="info"&gt; ** para ** &lt;o nível raiz="depurado".&gt;** Reinicie todos os nós/serviço para que a configuração faça efeito.
2. Monitorize os registos de topologia da tempestade em nós de trabalhador (em&lt;/var/log/storm/worker-artifacts/ TopologyName&gt;/&lt;port&gt;/worker.log) para data Lake Storage Gen1 acelerando exceções.

## <a name="next-steps"></a>Passos seguintes
A sintonização adicional de desempenho para storm pode ser referenciada [neste blog.](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/)

Para um exemplo adicional para correr, veja [este no GitHub](https://github.com/hdinsight/storm-performance-automation).
