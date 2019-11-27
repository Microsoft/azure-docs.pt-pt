---
title: 'Ajustar o desempenho: o Storm, o HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Diretrizes de ajuste de desempenho do Azure Data Lake Storage Gen2 Storm
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 125c583512f6bae34c2dd3c3dd76a1b96a181ac1
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327908"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>Ajustar o desempenho: o Storm, o HDInsight & Azure Data Lake Storage Gen2

Entenda os fatores que devem ser considerados ao ajustar o desempenho de uma topologia do Azure Storm. Por exemplo, é importante entender as características do trabalho feito pelos limites e os parafusos (se o trabalho tem uso intensivo de e/s ou memória). Este artigo aborda uma variedade de diretrizes de ajuste de desempenho, incluindo a solução de problemas comuns.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de Azure data Lake Storage Gen2**. Para obter instruções sobre como criar uma, consulte [início rápido: criar uma conta de armazenamento para análise](data-lake-storage-quickstart-create-account.md).
* **Cluster HDInsight do Azure** com acesso a uma conta de data Lake Storage Gen2. Consulte [usar Azure data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Certifique-se de habilitar Área de Trabalho Remota para o cluster.
* **Executando um cluster Storm no data Lake Storage Gen2**. Para obter mais informações, consulte [Storm no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Diretrizes de ajuste de desempenho em data Lake Storage Gen2**.  Para obter conceitos gerais de desempenho, consulte [Data Lake Storage Gen2 diretrizes de ajuste de desempenho](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>Ajustar o paralelismo da topologia

Talvez seja possível melhorar o desempenho aumentando a simultaneidade da e/s de e para a Data Lake Storage Gen2. Uma topologia Storm tem um conjunto de configurações que determinam o paralelismo:
* Número de processos de trabalho (os trabalhadores são distribuídos uniformemente entre as VMs).
* Número de instâncias de executor Spout.
* Número de instâncias de executor de raio.
* Número de tarefas Spout.
* Número de tarefas de raio.

Por exemplo, em um cluster com 4 VMs e quatro processos de trabalho, 32 executores de Spout e 32 tarefas de Spout, e as tarefas de executores de 256 e 512, considere o seguinte:

Cada supervisor, que é um nó de trabalho, tem um processo de máquina virtual Java (JVM) de trabalho único. Este processo de JVM gerencia 4 threads Spout e threads de 64 pinos. Em cada thread, as tarefas são executadas em sequência. Com a configuração anterior, cada thread Spout tem uma tarefa, e cada thread de raio tem duas tarefas.

No Storm, aqui estão os vários componentes envolvidos e como eles afetam o nível de paralelismo que você tem:
* O nó principal (chamado Nimbus no Storm) é usado para enviar e gerenciar trabalhos. Esses nós não têm impacto sobre o grau de paralelismo.
* Os nós de supervisor. No HDInsight, isso corresponde a uma VM do Azure no nó de trabalho.
* As tarefas de trabalho são processos do Storm em execução nas VMs. Cada tarefa de trabalho corresponde a uma instância de JVM. O Storm distribui o número de processos de trabalho que você especifica para os nós de trabalho o mais uniforme possível.
* Spout e instâncias de executor de raio. Cada instância de executor corresponde a um thread em execução dentro dos trabalhadores (JVMs).
* Tarefas do Storm. Essas são tarefas lógicas que cada um desses threads executa. Isso não altera o nível de paralelismo, portanto, você deve avaliar se precisa de várias tarefas por executor ou não.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Obtenha o melhor desempenho de Data Lake Storage Gen2

Ao trabalhar com Data Lake Storage Gen2, você obterá o melhor desempenho se fizer o seguinte:
* Reúna seus pequenos acréscimos em tamanhos maiores.
* Faça o máximo possível de solicitações simultâneas. Como cada thread de raio está fazendo leituras de bloqueio, você deseja ter algum lugar no intervalo de 8-12 threads por núcleo. Isso mantém a NIC e a CPU bem utilizadas. Uma VM maior permite mais solicitações simultâneas.  

### <a name="example-topology"></a>Topologia de exemplo

Vamos supor que você tenha um cluster de 8 nós de trabalho com uma VM do Azure D13v2. Essa VM tem 8 núcleos, portanto, entre os 8 nós de trabalho, você tem 64 núcleos no total.

Digamos que tenhamos oito threads por núcleo. Dadas 64 núcleos, isso significa que desejamos que desejamos 512 instâncias de executor de raio (ou seja, threads). Nesse caso, vamos dizer que começamos com uma JVM por VM e, principalmente, usamos a simultaneidade de thread dentro da JVM para obter simultaneidade. Isso significa que precisamos de oito tarefas de trabalho (uma por VM do Azure) e executores de raio de 512. Devido a essa configuração, o Storm tenta distribuir os operadores uniformemente entre os nós de trabalho (também conhecidos como nós de supervisor), dando a cada nó de trabalho 1 JVM. Agora, nos supervisores, o Storm tenta distribuir os executores uniformemente entre supervisores, fornecendo a cada Supervisor (ou seja, JVM) 8 threads.

## <a name="tune-additional-parameters"></a>Ajustar parâmetros adicionais
Depois de ter a topologia básica, você pode considerar se deseja ajustar qualquer um dos parâmetros:
* **Número de JVMs por nó de trabalho.** Se você tiver uma estrutura de dados grande (por exemplo, uma tabela de pesquisa) que você hospeda na memória, cada JVM exigirá uma cópia separada. Como alternativa, você pode usar a estrutura de dados em vários threads se tiver menos JVMs. Para a e/s do parafuso, o número de JVMs não faz tanta diferença quanto o número de threads adicionados entre esses JVMs. Para simplificar, é uma boa ideia ter uma JVM por trabalho. No entanto, dependendo do que seu raio está fazendo ou do processamento de aplicativos que você precisa, você pode precisar alterar esse número.
* **Número de executores de Spout.** Como o exemplo anterior usa parafusos para gravar em Data Lake Storage Gen2, o número de esgotamentos não é diretamente relevante para o desempenho do raio. No entanto, dependendo da quantidade de processamento ou de e/s acontecendo no Spout, é uma boa ideia ajustar as esgotamentos para o melhor desempenho. Verifique se você tem esgotamentos suficientes para poder manter os parafusos ocupados. As taxas de saída dos limites devem corresponder à taxa de transferência dos parafusos. A configuração real depende do Spout.
* **Número de tarefas.** Cada raio é executado como um único thread. Tarefas adicionais por raio não fornecem nenhuma simultaneidade adicional. A única vez que eles são vantajosos é que o processo de confirmar a tupla é uma grande proporção do tempo de execução do seu raio. É uma boa ideia agrupar várias tuplas em um acréscimo maior antes de enviar uma confirmação do parafuso. Portanto, na maioria dos casos, várias tarefas não fornecem nenhum benefício adicional.
* **Agrupamento local ou em ordem aleatória.** Quando essa configuração é habilitada, as tuplas são enviadas para os parafusos dentro do mesmo processo de trabalho. Isso reduz as chamadas de rede e de comunicação entre processos. Isso é recomendado para a maioria das topologias.

Esse cenário básico é um bom ponto de partida. Teste com seus próprios dados para ajustar os parâmetros anteriores para obter um desempenho ideal.

## <a name="tune-the-spout"></a>Ajustar o Spout

Você pode modificar as configurações a seguir para ajustar o Spout.

- **Tempo limite de tupla: topologia. mensagem. Timeout. segundos**. Essa configuração determina a quantidade de tempo que uma mensagem leva para ser concluída e recebe a confirmação antes de ser considerada falha.

- **Máximo de memória por processo de trabalho: Worker. childopts**. Essa configuração permite especificar parâmetros de linha de comando adicionais para os trabalhos Java. A configuração mais comumente usada aqui é XmX, que determina a memória máxima alocada para o heap de uma JVM.

- **Máximo de Spout pendentes: topologia. Max. Spout. Pending**. Essa configuração determina o número de tuplas que podem ser comprovadas (ainda não confirmadas em todos os nós na topologia) por thread Spout a qualquer momento.

  Um bom cálculo a fazer é estimar o tamanho de cada uma de suas tuplas. Em seguida, descubra a quantidade de memória que um thread Spout tem. A memória total alocada para um thread, dividida por esse valor, deve fornecer o limite superior para o parâmetro Max Spout Pending.

O padrão Data Lake Storage Gen2 Storm tem um parâmetro de política de sincronização de tamanho (filebuffersize) que pode ser usado para ajustar esse parâmetro.

Em topologias com uso intensivo de e/s, é uma boa ideia fazer com que cada thread de raio grave em seu próprio arquivo e defina uma fileRotationSize (política de rotação de arquivo). Quando o arquivo atinge um determinado tamanho, o fluxo é liberado automaticamente e um novo arquivo é gravado nele. O tamanho de arquivo recomendado para a rotação é 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Monitore sua topologia no Storm  
Enquanto sua topologia estiver em execução, você poderá monitorá-la na interface do usuário do Storm. Estes são os principais parâmetros a serem examinados:

* **Latência total de execução do processo.** Esse é o tempo médio que uma tupla leva para ser emitida pelo Spout, processada pelo parafuso e confirmada.

* **Latência de processo de raio total.** Este é o tempo médio gasto pela tupla no raio até receber uma confirmação.

* **Latência de execução do raio total.** Este é o tempo médio gasto pelo parafuso no método execute.

* **Número de falhas.** Isso se refere ao número de tuplas que não puderam ser totalmente processadas antes de atingirem o tempo limite.

* **Recurso.** Essa é uma medida da ocupação do sistema. Se esse número for 1, seus parafusos estão funcionando tão rapidamente quanto podem. Se for menor que 1, aumente o paralelismo. Se for maior que 1, reduza o paralelismo.

## <a name="troubleshoot-common-problems"></a>Solucionar problemas comuns
Aqui estão alguns cenários de solução de problemas comuns.
* **Muitas tuplas estão atingindo o tempo limite.** Examine cada nó na topologia para determinar onde está o afunilamento. O motivo mais comum para isso é que os parafusos não conseguem acompanhar os esgotamentos. Isso leva a tuplas que sobrecarregam os buffers internos enquanto aguardam para serem processados. Considere aumentar o valor de tempo limite ou diminuir o máximo de Spout pendentes.

* **Há uma alta latência de execução de processo total, mas uma latência de processo de raio baixo.** Nesse caso, é possível que as tuplas não sejam confirmadas com rapidez suficiente. Verifique se há um número suficiente de Confirmadores. Outra possibilidade é que eles estejam aguardando na fila por muito tempo antes que os parafusos comecem a processá-los. Diminuir o máximo de Spout pendentes.

* **Há uma latência de execução de alto parafuso.** Isso significa que o método Execute () do seu raio está demorando muito. Otimize o código ou examine os tamanhos de gravação e o comportamento de liberação.

### <a name="data-lake-storage-gen2-throttling"></a>Limitação de Data Lake Storage Gen2
Se você atingir os limites de largura de banda fornecidos pelo Data Lake Storage Gen2, poderá ver falhas de tarefas. Verifique os logs de tarefa para obter erros de limitação. Você pode diminuir o paralelismo aumentando o tamanho do contêiner.    

Para verificar se você está ficando limitado, habilite o log de depuração no lado do cliente:

1. No **Ambari** > **Storm** > **config** > **avançado storm-Worker-Log4J**, altere **&lt;raiz Level = "info"&gt;** para **&lt;raiz Level = "debug"&gt;** . Reinicie todos os nós/serviços para que a configuração entre em vigor.
2. Monitore os logs de topologia do Storm em nós de trabalho (em/var/log/Storm/Worker-Artifacts/&lt;Topologianame&gt;/&lt;porta&gt;/Worker.log) para exceções de limitação de Data Lake Storage Gen2.

## <a name="next-steps"></a>Passos seguintes
O ajuste de desempenho adicional para o Storm pode ser referenciado neste [blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Para obter um exemplo adicional a ser executado, consulte [este no GitHub](https://github.com/hdinsight/storm-performance-automation).
