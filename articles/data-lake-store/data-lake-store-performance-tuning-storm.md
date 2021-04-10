---
title: Afinação de desempenho - Tempestade com Azure Data Lake Storage Gen1
description: Compreenda os fatores que devem ser considerados quando ajusta o desempenho de uma topologia da Tempestade Azure, incluindo a resolução de problemas em questões comuns.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 95619c75d332ec1bf68af97fc3dddbc67b6706ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97725042"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Orientação de afinação de desempenho para storm on HDInsight e Azure Data Lake Storage Gen1

Compreenda os fatores que devem ser considerados quando sintoniza o desempenho de uma topologia da Tempestade Azure. Por exemplo, é importante compreender as características do trabalho realizado pelos bicos e pelos parafusos (se o trabalho é I/O ou a memória intensiva). Este artigo abrange uma série de diretrizes de afinação de desempenho, incluindo a resolução de problemas em questões comuns.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar um, consulte [Começar com a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Um cluster Azure HDInsight** com acesso a uma conta Gen1 de armazenamento de data lake. Consulte [Criar um cluster HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativa o Ambiente de Trabalho Remoto para o cluster.
* **Executando um aglomerado de tempestade na Data Lake Storage Gen1.** Para obter mais informações, consulte [Storm on HDInsight](../hdinsight/storm/apache-storm-overview.md).
* **Diretrizes de afinação de desempenho sobre data lake storage gen1**.  Para obter conceitos gerais de desempenho, consulte [data lake storage Gen1 Performance Afinação de afinação.](./data-lake-store-performance-tuning-guidance.md)  

## <a name="tune-the-parallelism-of-the-topology"></a>Sintonize o paralelismo da topologia

Você pode ser capaz de melhorar o desempenho aumentando a concordância da I/O de e para data lake storage gen1. Uma topologia da tempestade tem um conjunto de configurações que determinam o paralelismo:
* Número de processos de trabalhadores (os trabalhadores são distribuídos uniformemente pelos VMs).
* Número de casos de executor de bicos.
* Número de instâncias executoras de parafusos.
* Número de tarefas de bico.
* Número de tarefas de parafuso.

Por exemplo, num cluster com 4 VMs e 4 processos de trabalhadores, 32 executores de bicos e 32 tarefas de bico, e 256 executores de parafusos e 512 tarefas de parafuso, considere o seguinte:

Cada supervisor, que é um nó de trabalhadores, tem um único processo de máquina virtual Java (JVM). Este processo JVM gere 4 fios de bico e 64 fios de parafuso. Dentro de cada fio, as tarefas são executadas sequencialmente. Com a configuração anterior, cada fio de bico tem uma tarefa, e cada fio de parafuso tem duas tarefas.

Em Storm, aqui estão os vários componentes envolvidos, e como eles afetam o nível de paralelismo que você tem:
* O nó de cabeça (chamado Nimbus in Storm) é usado para submeter e gerir empregos. Estes nós não têm impacto no grau de paralelismo.
* Os nós do supervisor. Em HDInsight, isto corresponde a um nó de trabalhador Azure VM.
* As tarefas dos trabalhadores são processos de tempestade em execução nos VMs. Cada tarefa de trabalhador corresponde a uma instância JVM. A tempestade distribui o número de processos de trabalhadores que especifica para os nós dos trabalhadores da forma mais uniforme possível.
* Casos de execução de bicos e parafusos. Cada instância executora corresponde a um fio que corre dentro dos trabalhadores (JVMs).
* Tarefas de tempestade. Estas são tarefas lógicas que cada um destes fios corre. Isto não altera o nível de paralelismo, por isso deve avaliar se precisa de várias tarefas por executor ou não.

### <a name="get-the-best-performance-from-data-lake-storage-gen1"></a>Obtenha o melhor desempenho da Data Lake Storage Gen1

Ao trabalhar com a Data Lake Storage Gen1, obtém o melhor desempenho se fizer o seguinte:
* Adense os seus pequenos apêndices em tamanhos maiores (idealmente 4 MB).
* Faça o máximo de pedidos simultâneos que puder. Como cada fio de parafuso está a fazer leituras de bloqueio, você quer ter em algum lugar no intervalo de 8-12 fios por núcleo. Isto mantém o NIC e o CPU bem utilizados. Um VM maior permite pedidos mais simultâneos.  

### <a name="example-topology"></a>Topologia exemplo

Vamos supor que tem um aglomerado de nó de oito trabalhadores com um D13v2 Azure VM. Este VM tem oito núcleos, por isso, entre os oito nós operários, tens 64 núcleos totais.

Digamos que fazemos oito fios de parafusos por núcleo. Tendo em conta 64 núcleos, isso significa que queremos 512 instâncias totais do executor de parafusos (isto é, fios). Neste caso, digamos que começamos com um JVM por VM, e usamos principalmente a concurrency de fio dentro do JVM para alcançar a concuência. Isto significa que precisamos de oito tarefas de trabalhadores (uma por Azure VM) e 512 executores de parafusos. Dada esta configuração, a Storm tenta distribuir os trabalhadores uniformemente pelos nós dos trabalhadores (também conhecidos como nós de supervisor), dando a cada trabalhador um JVM. Agora, dentro dos supervisores, Storm tenta distribuir os executores uniformemente entre supervisores, dando a cada supervisor (isto é, JVM) oito fios cada.

## <a name="tune-additional-parameters"></a>Sintonize parâmetros adicionais
Depois de ter a topologia básica, pode considerar se pretende ajustar algum dos parâmetros:
* **Número de JVMs por nó de trabalhador.** Se tiver uma grande estrutura de dados (por exemplo, uma tabela de procuração) que acolhe na memória, cada JVM requer uma cópia separada. Em alternativa, pode utilizar a estrutura de dados em muitos fios se tiver menos JVMs. Para o I/O do parafuso, o número de JVMs não faz tanta diferença como o número de fios adicionados através desses JVMs. Para simplificar, é uma boa ideia ter um JVM por trabalhador. Dependendo do que o seu parafuso está a fazer ou do processamento de aplicações que necessita, pode ter de alterar este número.
* **Número de executores de bico.** Como o exemplo anterior utiliza parafusos para escrever à Data Lake Storage Gen1, o número de bicos não é diretamente relevante para o desempenho do parafuso. No entanto, dependendo da quantidade de processamento ou I/O que acontece no bico, é uma boa ideia afinar os bicos para o melhor desempenho. Certifique-se de que tem bicos suficientes para manter os parafusos ocupados. As taxas de saída dos bicos devem coincidir com a produção dos parafusos. A configuração real depende do bico.
* **Número de tarefas.** Cada parafuso funciona como um único fio. Tarefas adicionais por parafuso não proporcionam qualquer concordância adicional. A única altura em que são benéficos é se o seu processo de reconhecimento da tuple requer uma grande parte do seu tempo de execução do raio. É uma boa ideia agrupar muitos tuples num apêndice maior antes de enviar um reconhecimento do parafuso. Assim, na maioria dos casos, múltiplas tarefas não oferecem nenhum benefício adicional.
* **Agrupamento local ou baralhado.** Quando esta definição estiver ativada, os tuples são enviados para parafusos dentro do mesmo processo de trabalho. Isto reduz a comunicação inter-processa e as chamadas de rede. Isto é recomendado para a maioria das topologias.

Este cenário básico é um bom ponto de partida. Teste com os seus próprios dados para ajustar os parâmetros anteriores para obter um desempenho ideal.

## <a name="tune-the-spout"></a>Sintonize o bico

Pode modificar as seguintes definições para sintonizar o bico.

- **Tempo limite de tuple: topology.message.timeout.secs**. Esta definição determina o tempo que uma mensagem demora a completar e receber reconhecimento, antes de ser considerada falhada.

- **Memória máxima por processo de trabalhador: trabalhador.childopts**. Esta definição permite especificar parâmetros adicionais de linha de comando para os trabalhadores java. A definição mais usada aqui é XmX, que determina a memória máxima atribuída à pilha de um JVM.

- **Bico máximo pendente: topologia.max.bico.pendente**. Esta definição determina o número de tuples que podem ser voo (ainda não reconhecidos em todos os nós na topologia) por linha de bico em qualquer momento.

  Um bom cálculo a fazer é estimar o tamanho de cada uma das suas tuples. Então descubra a quantidade de memória que um fio de bico tem. A memória total atribuída a um fio, dividido por este valor, deve dar-lhe o limite superior para o parâmetro pendente do bico máximo.

## <a name="tune-the-bolt"></a>Afinar o parafuso
Quando estiver a escrever para a Data Lake Storage Gen1, desace numa política de sincronização de tamanho (tampão do lado do cliente) para 4 MB. Uma descarga ou hsync() é então realizada apenas quando o tamanho do tampão está neste valor. O condutor da Data Lake Storage Gen1 no VM do trabalhador faz automaticamente este tampão, a menos que execute explicitamente um hsync().

O parafuso de tempestade Gen1 de armazenamento de dados padrão tem um parâmetro de política de sincronização de tamanho (fileBufferSize) que pode ser usado para sintonizar este parâmetro.

Nas topologias intensivas de I/O, é uma boa ideia ter cada fio de parafuso escrito no seu próprio ficheiro, e definir uma política de rotação de ficheiros (fileRotationSize). Quando o ficheiro atinge um determinado tamanho, o fluxo é automaticamente lavado e um novo ficheiro é escrito para. O tamanho recomendado do ficheiro para a rotação é de 1 GB.

### <a name="handle-tuple-data"></a>Lidar com dados de tuple

Em Storm, um bico mantém-se numa tuple até ser explicitamente reconhecido pelo parafuso. Se um tuple foi lido pelo parafuso mas ainda não foi reconhecido, o bico pode não ter persistido no fundo de data lake storage Gen1. Depois de um tuple ser reconhecido, o bico pode ser garantido persistência pelo parafuso, e pode então apagar os dados de origem de qualquer fonte que esteja lendo.  

Para melhor desempenho na Data Lake Storage Gen1, tenha o tampão de parafuso 4 MB de dados de tuple. Em seguida, escreva para o data lake storage Gen1 back end como um 4 MB escrever. Depois de os dados terem sido escritos com sucesso à loja (chamando hflush()), o parafuso pode reconhecer os dados de volta ao bico. Isto é o que o parafuso de exemplo fornecido aqui faz. Também é aceitável manter um maior número de tuples antes da chamada hflush() é feita e os tuples reconhecidos. No entanto, isto aumenta o número de tuples em voo que o bico precisa de manter, e, portanto, aumenta a quantidade de memória necessária por JVM.

> [!NOTE]
> As aplicações podem ter a obrigação de reconhecer os tuples com mais frequência (em tamanhos de dados inferiores a 4 MB) por outras razões de não desempenho. No entanto, isso pode afetar a produção de I/O para a parte de trás do armazenamento. Pese cuidadosamente esta troca contra o desempenho de I/O do parafuso.

Se a taxa de entrada de tuples não for alta, por isso o tampão de 4-MB demora muito tempo a preencher, considere atenuar esta medida:
* Reduzindo o número de parafusos, para que haja menos tampão para preencher.
* Ter uma política baseada no tempo ou na contagem, onde um hflush é acionado cada x flushes ou cada milissegundos, e os tuples acumulados até agora são reconhecidos de volta.

A produção neste caso é menor, mas com uma taxa lenta de eventos, a produção máxima não é o maior objetivo de qualquer maneira. Estas mitigações ajudam a reduzir o tempo total que um tuple leva para fluir até à loja. Isto pode importar se você quiser um oleoduto em tempo real mesmo com uma taxa de evento baixa. Note também que se a sua taxa de tuple de entrada for baixa, deve ajustar o parâmetro topology.message.timeout_secs, para que os tuples não se esfole enquanto estão a ser tamponados ou processados.

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

### <a name="data-lake-storage-gen1-throttling"></a>Estrangulamento da Gen1 de armazenamento de dados
Se atingir os limites de largura de banda fornecidos pela Data Lake Storage Gen1, poderá ver falhas de tarefa. Verifique os registos de tarefas para obter erros de estrangulamento. Pode diminuir o paralelismo aumentando o tamanho do recipiente.    

Para verificar se está a ser estrangulado, ative o registo de depurar do lado do cliente:

1. Em **Ambari**  >  **Storm**  >  **Config** Advanced  >  **storm-worker-log4j**, change root **&lt; level="info" &gt;** to root **&lt; level="debug". &gt;** Reinicie todos os nós/serviço para que a configuração entre em vigor.
2. Monitorize os registos de topologia da tempestade nos nós dos trabalhadores (em /var/log/storm/worker-artifacts/ &lt; TopologyName &gt; / &lt; port &gt; /worker.log) para as exceções de estrangulamento da Gen1 de armazenamento de dados.

## <a name="next-steps"></a>Passos seguintes
A afinação adicional de desempenho para Storm pode ser referenciada [neste blog](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs).

Para um exemplo adicional a correr, veja [este no GitHub.](https://github.com/hdinsight/storm-performance-automation)