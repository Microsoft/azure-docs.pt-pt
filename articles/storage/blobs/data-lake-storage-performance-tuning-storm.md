---
title: Diretrizes de otimização do desempenho de Storm de geração 2 do Data Lake Storage do Azure | Documentos da Microsoft
description: Diretrizes de otimização do desempenho de Storm de geração 2 do Data Lake Storage do Azure
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: 2401c74b55df78014a2f642b5166b4cf0017d87d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076212"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Guia para o Storm no HDInsight e geração 2 de armazenamento do Azure Data Lake de sintonização de desempenho

Compreenda os fatores que devem ser considerados quando ajustar o desempenho de uma topologia de Storm do Azure. Por exemplo, é importante compreender as características do trabalho realizado de spouts e bolts (se o trabalho é e/s ou elevado consumo de memória). Este artigo cobre uma variedade de diretrizes, incluindo a resolução de problemas comuns de ajuste de desempenho.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de geração 2 de armazenamento do Azure Data Lake**. Para obter instruções sobre como criar um, consulte [início rápido: Criar um armazenamento de conta para análise](data-lake-storage-quickstart-create-account.md).
* **Cluster de HDInsight do Azure** com acesso a uma conta de geração 2 de armazenamento do Data Lake. Ver [Use Azure Data Lake armazenamento Gen2 com o Azure HDInsight clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Certifique-se de que ativar o ambiente de trabalho remoto para o cluster.
* **Executar um cluster do Storm na geração 2 de armazenamento do Data Lake**. Para obter mais informações, consulte [Storm no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Diretrizes de geração 2 de armazenamento do Data Lake de ajuste de desempenho**.  Para os conceitos gerais de desempenho, consulte [Data Lake Storage Gen2 ajuste orientação de desempenho](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>Otimizar o paralelismo da topologia

Poderá conseguir melhorar o desempenho aumentando a simultaneidade de e/s para e de geração 2 de armazenamento do Data Lake. Uma topologia do Storm tem um conjunto de configurações que determinam o paralelismo:
* Número de processos de trabalho (os trabalhadores estão distribuídos uniformemente entre as VMs).
* Número de instâncias de executor de spout.
* Número de instâncias de executor bolt.
* Número de tarefas de spout.
* Número de tarefas de bolt.

Por exemplo, num cluster com 4 VMs e 4 processos de trabalho, executores de spout de 32 e 32 spout tarefas e executores 256 bolt e tarefas de 512 bolt, considere o seguinte:

Cada supervisor, que é um nó de trabalho, tem uma função de trabalho única processo da máquina virtual (JVM) de Java. Este processo JVM gerencia 4 spout threads e de 64 threads de bolt. Dentro de cada thread, as tarefas são executadas sequencialmente. Com a configuração anterior, cada thread de spout tem 1 tarefa e cada thread de bolt tem 2 tarefas.

No Storm, aqui estão os vários componentes envolvidos e como elas afetam o nível de paralelismo que tem:
* O nó principal (chamado de Nimbus no Storm) é utilizado para submeter e gerir tarefas. Estes nós tem nenhum impacto sobre o grau de paralelismo.
* Os nós de supervisor. No HDInsight, isso corresponde a um nó de trabalho de VM do Azure.
* As tarefas de trabalho são processos de Storm em execução nas VMs. Cada tarefa de trabalho corresponde a uma instância JVM. Storm distribui o número de processos de trabalho que especificou para os nós de trabalho mais uniformemente possível.
* Spout e bolt instâncias de executor. Cada instância de executor corresponde a um segmento executado dentro de funções de trabalho (JVMs).
* Tarefas do Storm. Tratam-se de que cada um desses threads de execução de tarefas lógicas. Isso não altera o nível de paralelismo, portanto, deve avaliar se precisar de várias tarefas por executor ou não.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Obter o melhor desempenho de geração 2 de armazenamento do Data Lake

Ao trabalhar com a geração 2 de armazenamento do Data Lake, receberá o melhor desempenho se faça o seguinte:
* Coalesce sua pequena acrescenta em tamanhos maiores.
* Como muitos pedidos em simultâneo à medida que pode. Uma vez que cada thread de bolt faz leituras de bloqueios, deseja ter-se em algum lugar no intervalo de 12 de 8 threads por núcleo. Este procedimento impede que a NIC e a CPU bem utilizado. Uma VM maior permite que os pedidos mais em simultâneo.  

### <a name="example-topology"></a>Topologia de exemplo

Vamos supor que tem um cluster de nó de 8 trabalho com uma VM do Azure D13v2. Esta VM tem 8 núcleos, para entre os nós de trabalho de 8, que tenha 64 total de núcleos.

Digamos que fazemos 8 threads de bolt por núcleo. Com base 64 núcleos, significa que queremos 512 instâncias de executor de total bolt (ou seja, threads). Neste caso, vamos supor que vamos começar com um JVM por VM e utilizam principalmente o de concorrência de thread o JVM para atingir a simultaneidade. Isso significa que precisamos de 8 tarefas de trabalho (um por VM do Azure) e 512 executores de bolt. Tendo em conta esta configuração, Storm tenta distribuir os trabalhadores uniformemente em nós de trabalho (também conhecido como nós de supervisor), dando a cada nó de trabalho 1 JVM. Agora, dentro de supervisores, Storm tenta distribuir executores uniformemente entre os supervisores, dando a cada supervisor (ou seja, JVM) 8 threads cada um.

## <a name="tune-additional-parameters"></a>Otimizar os parâmetros adicionais
Depois de ter a topologia básica, pode considerar se deseja ajustar qualquer um dos parâmetros:
* **Número de JVMs por nó de trabalho.** Se tiver uma estrutura de dados de grandes dimensões (por exemplo, uma tabela de referência) o anfitrião na memória, cada JVM requer uma cópia separada. Em alternativa, pode utilizar a estrutura de dados entre vários threads, se tiver menos JVMs. Para e/s do bolt, o número de JVMs não faz tanto de uma diferença, como o número de threads adicionado entre esses JVMs. Para simplificar, é uma boa idéia ter um JVM por função de trabalho. Consoante o que está fazendo o bolt ou o aplicativo de processamento necessitam, no entanto, poderá ter de alterar este número.
* **Número de spout executores.** Uma vez que o exemplo anterior utiliza os bolts para escrever a geração 2 de armazenamento do Data Lake, o número de spouts não é diretamente relevante para o desempenho de bolt. No entanto, dependendo da quantidade de processamento ou e/s do spout a acontecer, é uma boa idéia para otimizar os spouts para um melhor desempenho. Certifique-se de que tem suficiente spouts para poder manter os práticos ocupados. As taxas de saída dos spouts devem corresponder ao débito dos práticos. A configuração real depende de spout.
* **Número de tarefas.** Cada bolt é executado como um único thread. Tarefas adicionais por bolt não fornecem qualquer simultaneidade adicional. Só são do benefício é se o seu processo de confirmar a tupla tem uma grande proporção de seu tempo de execução de bolt. É uma boa idéia para o grupo de que tuplas muitas numa maior acrescentar antes de enviar uma confirmação do bolt. Então, na maioria dos casos, várias tarefas não fornecem nenhum benefício adicional.
* **Local ou shuffle agrupamento.** Quando esta definição estiver ativada, as tuplas são enviadas para os bolts dentro do mesmo processo de trabalho. Isso reduz a chamadas de rede e comunicação entre processos. Isto é recomendado para a maioria das topologias.

Este cenário básico é um ponto de partida. Teste com os seus dados para ajustar os parâmetros anteriores para obter um desempenho ideal.

## <a name="tune-the-spout"></a>Otimizar o spout

Pode modificar as seguintes definições para otimizar o spout.

- **Tempo limite de Tupla: topology.message.timeout.secs**. Esta definição determina a quantidade de tempo uma mensagem demora a concluir e receber confirmação, antes de ele é considerado uma falha.

- **Máx. de memória por processo de trabalho: worker.childopts**. Esta definição permite-lhe especificar os parâmetros da linha de comandos adicionais para os operadores de Java. A configuração mais comumente usada aqui é XmX, que determina o máximo de memória alocado para a área dinâmica para dados de um JVM.

- **Spout de Max pendentes: topology.max.spout.pending**. Esta definição determina o número de tuplas em podem ser um voo (ainda não reconhecido em todos os nós na topologia) por thread de spout em qualquer altura.

  Um bom cálculo para o fazer é estimar o tamanho de cada uma de suas cadeias de identificação. Em seguida, descobrir thread de um spout quanta memória tem. Total de memória atribuída a um thread, dividido por este valor, deverá dar-lhe o limite superior para o máximo spout pendentes parâmetro.

O bolt do Storm de geração 2 de armazenamento do Data Lake predefinida tem um parâmetro de política do sincronização tamanho (fileBufferSize) que pode ser utilizado para otimizar este parâmetro.

Em topologias de e/S intensivas, é uma boa idéia ter cada thread de bolt escrever seu próprio ficheiro e definir uma política de rotação de ficheiro (fileRotationSize). Quando o ficheiro atinge um determinado tamanho, o fluxo é descarregado automaticamente e um novo ficheiro é escrito. O tamanho do ficheiro recomendado para a rotação é de 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Monitorizar a topologia de Storm  
Enquanto a topologia está em execução, pode monitorizá-lo na interface de utilizador do Storm. Seguem-se os parâmetros de principais para ver:

* **Latência de execução do processo total.** Este é o tempo médio de uma tupla demora a ser emitida pelo spout, processados pelo bolt e confirmados.

* **Latência de processo total bolt.** Este é o tempo médio gasto pela tupla no bolt até receber uma confirmação.

* **Latência de execução total bolt.** Este é o tempo médio gasto por bolt no método execute.

* **Número de falhas.** Isso se refere ao número de tuplas que falharam ao ser totalmente processados antes de eles ultrapassou o tempo limite.

* **Capacidade.** Esta é uma medida de é o estado de disponibilidade do seu sistema. Se este número for 1, os bolts trabalhar o mais rápido que conseguirem. Se for inferior a 1, aumente o paralelismo. Se for superior a 1, reduza o paralelismo.

## <a name="troubleshoot-common-problems"></a>Resolver problemas comuns
Seguem-se alguns cenários de resolução de problemas comuns.
* **As tuplas muitos são exceder o tempo limite.** Ver todos os nós a topologia para determinar em que o afunilamento está. A razão mais comum disso é que não conseguem os práticos acompanhar os spouts. Isso nos leva a tuplas atrapalhando os buffers internos ao mesmo tempo a aguardar processamento. Considere aumentar o valor de tempo limite ou diminuindo o máximo spout pendentes.

* **Existe uma latência de execução do processo de total elevado, mas uma latência de processo bolt baixa.** Neste caso, é possível que as tuplas não estão a ser confirmadas fast suficiente. Verifique o que há um número suficiente de acknowledgers. Outra possibilidade é que eles estão a aguardar na fila durante muito tempo antes de começar aos práticos processá-los. Diminua o máximo spout pendentes.

* **Existe um bolt alta latência de execução.** Isso significa que o método Execute () de seu bolt está a demorar demasiado tempo. Otimizar o código, ou examinar a tamanhos de escrita e libere o comportamento.

### <a name="data-lake-storage-gen2-throttling"></a>Limitação do Data Lake Storage Gen2
Se atingir os limites de largura de banda fornecido pela geração 2 de armazenamento do Data Lake, poderá ver falhas de tarefas. Verifique os registos de tarefas para erros de limitação. Pode diminuir o paralelismo aumente o tamanho do contentor.    

Para verificar se estiver obtendo otimizado, ative a depuração de registo no lado do cliente:

1. Na **Ambari** > **Storm** > **Config** > **Advanced storm-trabalho-log4j**, alterar **&lt;nível de raiz = "informações"&gt;** para  **&lt;nível de raiz = "debug"&gt;**. Reinicie todos o nós/serviço para a configuração entrem em vigor.
2. Monitorizar a topologia de Storm faz logon em nós de trabalho (em /var/log/storm/worker-artifacts /&lt;TopologyName&gt;/&lt;porta&gt;/worker.log) para geração 2 de armazenamento do Data Lake exceções de limitação.

## <a name="next-steps"></a>Passos Seguintes
Ajuste de desempenho adicional para o Storm pode ser referenciado [este blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Por exemplo adicional executar, veja [este aqui no GitHub](https://github.com/hdinsight/storm-performance-automation).
