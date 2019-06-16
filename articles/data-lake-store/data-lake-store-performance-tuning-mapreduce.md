---
title: Azure Data Lake Gen1 MapReduce o desempenho do armazenamento diretrizes de ajuste | Documentos da Microsoft
description: Azure Data Lake Gen1 MapReduce o desempenho do armazenamento diretrizes de ajuste
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
ms.openlocfilehash: b9e5d034db4711384d2ac8a1083da5c93ea11900
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61437247"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Guia para o MapReduce no HDInsight e Azure Data Lake Storage Gen1 de sintonização de desempenho

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de geração 1 de armazenamento do Azure Data Lake**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster de HDInsight do Azure** com acesso a uma conta de geração 1 de armazenamento do Data Lake. Ver [criar um cluster do HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativar o ambiente de trabalho remoto para o cluster.
* **Utilizar o MapReduce no HDInsight**.  Para obter mais informações, consulte [utilizar o MapReduce no Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Diretrizes sobre a geração 1 de armazenamento do Data Lake de ajuste de desempenho**.  Para os conceitos gerais de desempenho, consulte [Data Lake Storage Gen1 ajuste de orientação de desempenho](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parâmetros

Ao executar tarefas de MapReduce, aqui estão os parâmetros mais importantes que pode configurar para melhorar o desempenho Gen1 de armazenamento do Data Lake:

* **Mapreduce.Map.Memory.MB** – a quantidade de memória para atribuir a cada mapeador de pontos
* **Mapreduce.job.Maps** – o número de tarefas de mapa por tarefa
* **Mapreduce.reduce.Memory.MB** – a quantidade de memória para atribuir a cada reducer
* **Mapreduce.job.reduces** – o número de tarefas de reduza por tarefa

**Mapreduce.Map.Memory / Mapreduce.reduce.memory** este número deve ser ajustado com base na quantidade de memória é necessária para o mapa e/ou reduzir a tarefa.  Os valores predefinidos de mapreduce.map.memory e mapreduce.reduce.memory podem ser visualizados no Ambari através da configuração do Yarn.  No Ambari, navegue para o YARN e ver a guia configurações.  Será apresentada a memória YARN.  

**Mapreduce.job.Maps / Mapreduce.job.reduces** isso determinará o número máximo de mapeadores ou redutores a ser criada.  O número de divisões de irá determinar quantos mapeadores serão criados para a tarefa de MapReduce.  Por conseguinte, poderá obter menos mapeadores que pediu se existem menos divisões que o número de mapeadores solicitado.       

## <a name="guidance"></a>Orientação

**Passo 1: Determinar o número de tarefas em execução** -por predefinição, o MapReduce utilizará todo o cluster para a sua tarefa.  Pode utilizar o menor de cluster, utilizando menos mapeadores que existem contentores disponíveis.  As orientações neste documento parte do princípio de que seu aplicativo é o único aplicativo em execução no seu cluster.      

**Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory** – o tamanho da memória para o mapa e reduzir tarefas irão estar dependentes de seu trabalho específico.  Se pretender aumentar a simultaneidade, pode reduzir o tamanho da memória.  O número de tarefas em execução em simultâneo depende do número de contentores.  Ao reduzir a quantidade de memória por mapeador ou reducer, podem ser criadas mais contentores, que permitem mais mapeadores ou redutores para execução simultânea.  Diminuir a quantidade de memória muito pode fazer com que alguns processos a ficar sem memória.  Se obtiver um erro de área dinâmica para dados ao executar seu trabalho, deve aumentar a memória por mapeador ou reducer.  Deve considerar que a adição de mais contentores irá adicionar extra gerais para cada contentor adicional, que potencialmente pode degradar o desempenho.  Outra alternativa é obter mais memória ao utilizar um cluster com a maior quantidade de memória ou aumentar o número de nós do cluster.  Mais memória permitirá mais contentores a ser usado, que significa mais simultaneidade.  

**Passo 3: Determinar a memória de Total YARN** - para otimizar mapreduce.job.maps/mapreduce.job.reduces, deve considerar a quantidade de memória YARN total disponível para utilização.  Esta informação está disponível em Ambari.  Navegue para o YARN e ver a guia configurações.  A memória YARN é apresentada nesta janela.  Deve multiplicar a memória YARN com o número de nós do cluster para obter a memória YARN total.

    Total YARN memory = nodes * YARN memory per node
Se estiver a utilizar um cluster vazio, memória pode ser o total de memória YARN do seu cluster.  Se outros aplicativos estiver usando a memória, em seguida, pode optar por utilizar apenas uma parte da memória do seu cluster, reduzindo o número de mapeadores ou redutores para o número de contentores que pretende utilizar.  

**Passo 4: Calcular o número de contentores YARN** – contentores YARN ditam a quantidade de simultaneidade disponível para a tarefa.  Tire total de memória YARN e divido isso por mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduces** definido mapreduce.job.maps/mapreduce.job.reduces para, pelo menos, o número de contentores disponíveis.  Pode experimentar adicional do aumento do número de mapeadores e redutores para ver se consegue obter um melhor desempenho.  Tenha em atenção que mais mapeadores terão a sobrecarga adicional, de modo a ter demasiadas mapeadores pode degradar o desempenho.  

Agendamento de CPU e o isolamento de CPU são desativados por predefinição, para que o número de contentores YARN é restrita por memória.

## <a name="example-calculation"></a>Cálculo de exemplo

Digamos que tenha atualmente um composto por 8 D14 nós de cluster e pretender executar uma tarefa intensiva da e/s.  Seguem-se os cálculos que deve fazer:

**Passo 1: Determinar o número de tarefas em execução** -para o nosso exemplo, partimos do princípio de que o nosso trabalho é a execução apenas um.  

**Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory** – para o nosso exemplo, estiver a executar uma tarefa intensiva da e/s e decidir que 3 GB de memória para tarefas de mapa será suficiente.

    mapreduce.map.memory = 3GB
**Passo 3: Determinar a memória de Total YARN**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Passo 4: Calcular o n. º de contentores YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Limitações

**Limitação do Data Lake Storage Gen1**

Como um serviço de multi-inquilino, a geração 1 de armazenamento do Data Lake define limites de largura de banda ao nível da conta.  Se atingir estes limites, começa a ver falhas de tarefas. Isto pode ser identificado por erros de limitação a observar nos registos de tarefas.  Se precisar de mais largura de banda para a sua tarefa, entre em contato conosco.   

Para verificar se obter limitados, tem de ativar a depuração de registo no lado do cliente. Eis como pode fazer isso:

1. Coloque a seguinte propriedade nas propriedades do log4j no Ambari > YARN > configuração > Avançadas log4j do yarn: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Reinicie todos os o nós/serviço para a configuração entrem em vigor.

3. Se estiver obtendo otimizado, verá o código de erro HTTP 429 no ficheiro de registo YARN. O ficheiro de registo do YARN está em /tmp/&lt;utilizador&gt;/yarn.log

## <a name="examples-to-run"></a>Exemplos para execução

Para demonstrar como o MapReduce é executado na geração 1 de armazenamento do Data Lake, segue-se alguns exemplos de código que foi executado num cluster com as seguintes definições:

* nó de 16 D14v2
* Cluster do Hadoop em execução HDI 3.6

Para um ponto de partida, eis alguns comandos de exemplo para executar o MapReduce Teragen Terasort e Teravalidate.  Pode ajustar esses comandos com base nos seus recursos.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
