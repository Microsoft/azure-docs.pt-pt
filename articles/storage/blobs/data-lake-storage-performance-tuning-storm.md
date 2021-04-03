---
title: 'Desempenho da sintonização: Storm, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Compreenda as diretrizes para afinar o desempenho de uma topologia da Tempestade Azure num cluster Azure HDInsight e na Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4db85357ee970d13d6b4fcce195cae66932bed18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912795"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>Desempenho da sintonização: Storm, HDInsight & Azure Data Lake Storage Gen2

Compreenda os fatores que devem ser considerados quando sintoniza o desempenho de uma topologia da Tempestade Azure. Por exemplo, é importante compreender as características do trabalho realizado pelos bicos e pelos parafusos (se o trabalho é I/O ou a memória intensiva). Este artigo abrange uma série de diretrizes de afinação de desempenho, incluindo a resolução de problemas em questões comuns.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Azure Data Lake Storage Gen2**. Para obter instruções sobre como criar um, consulte [Quickstart: Crie uma conta de armazenamento para analíticos](../common/storage-account-create.md).
* **Cluster Azure HDInsight** com acesso a uma conta Gen2 de armazenamento de data lake. Consulte [a Utilização Azure Data Lake Storage Gen2 com clusters Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md). Certifique-se de que ativa o Ambiente de Trabalho Remoto para o cluster.
* **Executando um cluster storm em Data Lake Storage Gen2**. Para obter mais informações, consulte [Storm on HDInsight](../../hdinsight/storm/apache-storm-overview.md).
* **Diretrizes de afinação de desempenho sobre data lake storage gen2**.  Para obter conceitos gerais de desempenho, consulte [data lake storage Gen2 Performance Afinação de afinação.](data-lake-storage-performance-tuning-guidance.md)   

## <a name="tune-the-parallelism-of-the-topology"></a>Sintonize o paralelismo da topologia

Você pode ser capaz de melhorar o desempenho aumentando a concordância do I/O de e para data lake storage gen2. Uma topologia da tempestade tem um conjunto de configurações que determinam o paralelismo:
* Número de processos de trabalhadores (os trabalhadores são distribuídos uniformemente pelos VMs).
* Número de casos de executor de bicos.
* Número de instâncias executoras de parafusos.
* Número de tarefas de bico.
* Número de tarefas de parafuso.

Por exemplo, num cluster com 4 VMs e 4 processos de trabalhadores, 32 executores de bicos e 32 tarefas de bico, e 256 executores de parafusos e 512 tarefas de parafuso, considere o seguinte:

Cada supervisor, que é um nó de trabalhadores, tem um único processo de máquina virtual Java (JVM). Este processo JVM gere 4 fios de bico e 64 fios de parafuso. Dentro de cada fio, as tarefas são executadas sequencialmente. Com a configuração anterior, cada fio de bico tem 1 tarefa, e cada fio de parafuso tem 2 tarefas.

Em Storm, aqui estão os vários componentes envolvidos, e como eles afetam o nível de paralelismo que você tem:
* O nó de cabeça (chamado Nimbus in Storm) é usado para submeter e gerir empregos. Estes nós não têm impacto no grau de paralelismo.
* Os nós do supervisor. Em HDInsight, isto corresponde a um nó de trabalhador Azure VM.
* As tarefas dos trabalhadores são processos de tempestade em execução nos VMs. Cada tarefa de trabalhador corresponde a uma instância JVM. A tempestade distribui o número de processos de trabalhadores que especifica para os nós dos trabalhadores da forma mais uniforme possível.
* Casos de execução de bicos e parafusos. Cada instância executora corresponde a um fio que corre dentro dos trabalhadores (JVMs).
* Tarefas de tempestade. Estas são tarefas lógicas que cada um destes fios corre. Isto não altera o nível de paralelismo, por isso deve avaliar se precisa de várias tarefas por executor ou não.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Obtenha o melhor desempenho da Data Lake Storage Gen2

Ao trabalhar com a Data Lake Storage Gen2, obtém o melhor desempenho se fizer o seguinte:
* Adense os seus pequenos apêndices em tamanhos maiores.
* Faça o máximo de pedidos simultâneos que puder. Como cada fio de parafuso está a fazer leituras de bloqueio, você quer ter em algum lugar no intervalo de 8-12 fios por núcleo. Isto mantém o NIC e o CPU bem utilizados. Um VM maior permite pedidos mais simultâneos.  

### <a name="example-topology"></a>Topologia exemplo

Vamos supor que tem um aglomerado de nó de 8 trabalhadores com um D13v2 Azure VM. Este VM tem 8 núcleos, por isso, entre os 8 nós de trabalhadores, você tem 64 núcleos totais.

Digamos que fazemos 8 fios de parafusos por núcleo. Tendo em conta 64 núcleos, isso significa que queremos 512 instâncias totais do executor de parafusos (isto é, fios). Neste caso, digamos que começamos com um JVM por VM, e usamos principalmente a concurrency de fio dentro do JVM para alcançar a concuência. Isto significa que precisamos de 8 tarefas de trabalhadores (uma por Azure VM) e 512 executores de parafusos. Dada esta configuração, a Storm tenta distribuir os trabalhadores uniformemente pelos nós dos trabalhadores (também conhecidos como nós de supervisor), dando a cada trabalhador um nó de 1 JVM. Agora, dentro dos supervisores, Storm tenta distribuir os executores uniformemente entre supervisores, dando a cada supervisor (isto é, JVM) 8 fios cada.

## <a name="tune-additional-parameters"></a>Sintonize parâmetros adicionais
Depois de ter a topologia básica, pode considerar se pretende ajustar algum dos parâmetros:
* **Número de JVMs por nó de trabalhador.** Se tiver uma grande estrutura de dados (por exemplo, uma tabela de procuração) que acolhe na memória, cada JVM requer uma cópia separada. Em alternativa, pode utilizar a estrutura de dados em muitos fios se tiver menos JVMs. Para o I/O do parafuso, o número de JVMs não faz tanta diferença como o número de fios adicionados através desses JVMs. Para simplificar, é uma boa ideia ter um JVM por trabalhador. Dependendo do que o seu parafuso está a fazer ou do processamento de aplicações que necessita, pode ter de alterar este número.
* **Número de executores de bico.** Como o exemplo anterior utiliza parafusos para escrever à Data Lake Storage Gen2, o número de bicos não é diretamente relevante para o desempenho do parafuso. No entanto, dependendo da quantidade de processamento ou I/O que acontece no bico, é uma boa ideia afinar os bicos para o melhor desempenho. Certifique-se de que tem bicos suficientes para manter os parafusos ocupados. As taxas de saída dos bicos devem coincidir com a produção dos parafusos. A configuração real depende do bico.
* **Número de tarefas.** Cada parafuso funciona como um único fio. Tarefas adicionais por parafuso não proporcionam qualquer concordância adicional. A única altura em que são benéficos é se o seu processo de reconhecimento da tuple requer uma grande parte do seu tempo de execução do raio. É uma boa ideia agrupar muitos tuples num apêndice maior antes de enviar um reconhecimento do parafuso. Assim, na maioria dos casos, múltiplas tarefas não oferecem nenhum benefício adicional.
* **Agrupamento local ou baralhado.** Quando esta definição estiver ativada, os tuples são enviados para parafusos dentro do mesmo processo de trabalho. Isto reduz a comunicação inter-processa e as chamadas de rede. Isto é recomendado para a maioria das topologias.

Este cenário básico é um bom ponto de partida. Teste com os seus próprios dados para ajustar os parâmetros anteriores para obter um desempenho ideal.

## <a name="tune-the-spout"></a>Sintonize o bico

Pode modificar as seguintes definições para sintonizar o bico.

- **Tempo limite de tuple: topology.message.timeout.secs**. Esta definição determina o tempo que uma mensagem demora a completar e receber reconhecimento, antes de ser considerada falhada.

- **Memória máxima por processo de trabalhador: trabalhador.childopts**. Esta definição permite especificar parâmetros adicionais de linha de comando para os trabalhadores java. A definição mais usada aqui é XmX, que determina a memória máxima atribuída à pilha de um JVM.

- **Bico máximo pendente: topologia.max.bico.pendente**. Esta definição determina o número de tuples que podem ser voo (ainda não reconhecidos em todos os nós na topologia) por linha de bico em qualquer momento.

  Um bom cálculo a fazer é estimar o tamanho de cada uma das suas tuples. Então descubra a quantidade de memória que um fio de bico tem. A memória total atribuída a um fio, dividido por este valor, deve dar-lhe o limite superior para o parâmetro pendente do bico máximo.

O parafuso de tempestade Gen2 de armazenamento de dados padrão tem um parâmetro de política de sincronização de tamanho (fileBufferSize) que pode ser usado para sintonizar este parâmetro.

Nas topologias intensivas de I/O, é uma boa ideia ter cada fio de parafuso escrito no seu próprio ficheiro, e definir uma política de rotação de ficheiros (fileRotationSize). Quando o ficheiro atinge um determinado tamanho, o fluxo é automaticamente lavado e um novo ficheiro é escrito para. O tamanho recomendado do ficheiro para a rotação é de 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Monitorize a sua topologia em Tempestade  
Enquanto a sua topologia está em funcionamento, pode monitorá-la na interface do utilizador Storm. Aqui estão os principais parâmetros a analisar:

* **Latência total da execução do processo.** Este é o tempo médio que um tuple leva a ser emitido pelo bico, processado pelo parafuso, e reconhecido.

* **Latência total do processo do parafuso.** Este é o tempo médio gasto pela tuple no parafuso até receber um reconhecimento.

* **Bolt total executar latência.** Este é o tempo médio gasto pelo parafuso no método de execução.

* **Número de falhas.** Isto refere-se ao número de tuples que não foram totalmente processados antes de terem sido eliminados.

* **Capacidade.** Esta é uma medida de como o seu sistema está ocupado. Se este número for 1, os seus parafusos estão a funcionar o mais rápido que podem. Se for menos de 1, aumente o paralelismo. Se for maior que 1, reduza o paralelismo.

## <a name="troubleshoot-common-problems"></a>Resolver problemas comuns
Aqui estão alguns cenários comuns de resolução de problemas.
* **Muitos tuples estão a cronometrar.** Olhe para cada nó na topologia para determinar onde está o estrangulamento. A razão mais comum para isso é que os parafusos não são capazes de acompanhar os bicos. Isto leva a que os tuples obstruam os amortecedores internos enquanto esperam para serem processados. Considere aumentar o valor de tempo limite ou diminuir o bico máximo pendente.

* **Há uma alta latência total de execução do processo, mas uma latência de processo de baixo parafuso.** Neste caso, é possível que os tuples não estejam a ser reconhecidos suficientemente rápido. Verifique se há um número suficiente de reconhecedores. Outra possibilidade é que eles estejam à espera na fila por muito tempo antes que os parafusos comecem a processá-los. Diminua o bico máximo pendente.

* **Há uma alta execução da latência.** Isto significa que o método de execução do seu parafuso está a demorar demasiado tempo. Otimize o código, ou olhe para os tamanhos de escrita e o comportamento de descarga.

### <a name="data-lake-storage-gen2-throttling"></a>Estrangulamento da Gen2 de armazenamento de dados
Se atingir os limites de largura de banda fornecidos pela Data Lake Storage Gen2, poderá ver falhas de tarefa. Verifique os registos de tarefas para obter erros de estrangulamento. Pode diminuir o paralelismo aumentando o tamanho do recipiente.    

Para verificar se está a ser estrangulado, ative o registo de depurar do lado do cliente:

1. Em **Ambari**  >  **Storm**  >  **Config** Advanced  >  **storm-worker-log4j**, change root **&lt; level="info" &gt;** to root **&lt; level="debug". &gt;** Reinicie todos os nós/serviço para que a configuração entre em vigor.
2. Monitorize os registos de topologia da tempestade nos nós dos trabalhadores (em /var/log/storm/worker-artifacts/ &lt; TopologyName &gt; / &lt; port &gt; /worker.log) para as exceções de estrangulamento da Gen2 de armazenamento de dados.

## <a name="next-steps"></a>Passos seguintes
A afinação adicional de desempenho para Storm pode ser referenciada [neste blog](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs).

Para um exemplo adicional a correr, veja [este no GitHub.](https://github.com/hdinsight/storm-performance-automation)