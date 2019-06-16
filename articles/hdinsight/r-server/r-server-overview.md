---
title: Introdução aos serviços de ML no Azure HDInsight
description: Saiba como utilizar os serviços de ML no HDInsight para criar aplicativos para análise de macrodados.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/12/2019
ms.openlocfilehash: d24686a094c524c5ce913eee4b711daf1c60100d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67130619"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>O que é o serviços de ML no Azure HDInsight

Microsoft Machine Learning Server está disponível como uma opção de implementação quando criar clusters do HDInsight no Azure. O tipo de cluster que fornece esta opção é chamado **serviços ML**. Esta capacidade fornece cientistas de dados, estatísticos e programadores de R com acesso a pedido dimensionável, distribuído métodos de análise no HDInsight.

Serviços de ML no HDInsight oferece as capacidades mais recentes para análise baseada em R em conjuntos de dados de virtualmente qualquer tamanho, carregado para o armazenamento de Blobs do Azure ou o Data Lake. Uma vez que o cluster de serviços de ML baseia-se em R de código aberto, os aplicativos baseados em R que criar podem tirar partido de qualquer os 8000 + aberto pacotes R. As rotinas de ScaleR, o pacote de análise de macrodados da Microsoft também estão disponíveis.

Nó de extremidade de um cluster fornece um local conveniente para ligar ao cluster e para executar os scripts R. Com um nó de extremidade, tem a opção de executar as funções em paralelo distribuídas de ScaleR entre os núcleos do servidor de nó edge. Também pode executá-los em todos os nós do cluster com o Hadoop mapeamento redução do ScaleR ou contextos de cálculo do Apache Spark.

Os modelos ou predições resultantes de análise podem ser transferidas para utilização no local. Eles podem também serem operacionalizados noutro local no Azure, em particular através de [Azure Machine Learning Studio](https://studio.azureml.net) [serviço web](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Introdução aos serviços de ML no HDInsight

Para criar um cluster de serviços de ML no Azure HDInsight, selecione o **serviços ML** tipo de cluster, ao criar um cluster do HDInsight com o portal do Azure. O tipo de cluster de serviços de ML inclui ML Server em nós de dados do cluster e num nó de extremidade, que serve como uma zona de destino para a análise baseada em serviços de ML. Ver [introdução aos serviços de ML no HDInsight](r-server-get-started.md) para obter instruções sobre como criar o cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Por quê escolher o ML serviços no HDInsight?

Serviços de ML no HDInsight oferece as seguintes vantagens:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Inovações de IA da Microsoft e de código-fonte aberto

  Serviços de ML inclui um conjunto altamente dimensionável e distribuído de algoritmos como [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package), e [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) que possa trabalhar em tamanhos de dados maiores do que o tamanho da memória física e execute numa grande variedade de plataformas de uma maneira distribuída. Saiba mais sobre a coleção de Microsoft personalizado da [pacotes de R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) e [pacotes Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) incluído com o produto.
  
  Serviços de ML preenche essas contribuições proveniente da Comunidade de open source (R, Python e IA Kits de ferramentas) tudo por cima de uma plataforma de nível empresarial única e inovações da Microsoft. Qualquer pacote de aprendizado de máquina de código-fonte aberto R ou Python pode trabalhar lado a lado com qualquer inovação proprietária da Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Operacionalização de Simple, segura e altamente dimensionável e de administração

  As empresas dependem paradigmas tradicionais e ambientes de investem muito tempo e esforço em direção à operacionalização. Isso resulta em custos inflacionados e atrasa, incluindo o tempo de tradução para modelos, iterações para mantê-los válido e aprovação atual, normas e de gerenciamento de permissões por meio de operacionalização.

  Serviços de ML oferece de nível empresarial [operacionalização](https://docs.microsoft.com/machine-learning-server/what-is-operationalization), em que, depois de concluída um modelo de machine learning, que demora apenas alguns cliques para gerar as APIs de serviços web. Estes [serviços da web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) são hospedados numa grade de servidor na cloud e pode ser integrado com aplicações de linha de negócio. A capacidade de implementar um permite de grade elástico dimensionar de forma totalmente integrada com as necessidades da sua empresa, tanto para batch e de classificação em tempo real. Para obter instruções, consulte [Operacionalize serviços de ML no HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Principais recursos de serviços de ML no HDInsight

As seguintes funcionalidades estão incluídas nos serviços de ML no HDInsight.

| Categoria de funcionalidades | Descrição |
|------------------|-------------|
| R-ativada | [Pacotes de R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) para soluções escritas em R, com uma distribuição de código-fonte aberto de R e a infraestrutura de tempo de execução para a execução do script. |
| Capacidade para Python | [Módulos de Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) para soluções de escrita em Python, com uma distribuição de código-fonte aberto de Python e a infraestrutura de tempo de execução para a execução do script.
| [Modelos com formação prévia](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Para análise visual e análise de sentimentos de texto, pronto para classificar dados fornecer. |
| [Implementar e consumir](r-server-operationalize.md) | Operacionalizar o seu servidor e implemente soluções como um serviço web. |
| [Execução remota](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Inicie sessões remotas no cluster de ML serviços na sua rede da sua estação de trabalho do cliente. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opções de armazenamento de dados para serviços de ML no HDInsight

Armazenamento de predefinido para o sistema de ficheiros HDFS de clusters do HDInsight pode ser associado uma conta de armazenamento do Azure ou um armazenamento do Azure Data Lake. Esta associação garante que os dados que são carregados para o cluster de armazenamento durante a análise é efetuado, persistente e os dados estão disponíveis mesmo após o cluster é eliminado. Existem várias ferramentas para lidar com a transferência de dados para a opção de armazenamento que selecionar, incluindo o recurso de carregamento com base no portal da conta de armazenamento e o [AzCopy](../../storage/common/storage-use-azcopy.md) utilitário.

Tem a opção de permitir o acesso aos BLOBs adicionais e o Data lake armazena durante o processo, independentemente da opção de armazenamento primário utilizado de aprovisionamento de clusters. Ver [introdução aos serviços de ML no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) para obter informações sobre como adicionar o acesso a contas adicionais. Ver [opções de armazenamento do Azure para serviços de ML no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) artigo para saber mais sobre como utilizar várias contas de armazenamento.

Também pode utilizar [ficheiros do Azure](../../storage/files/storage-how-to-use-files-linux.md) como uma opção de armazenamento para utilização no nó de extremidade. Os ficheiros do Azure permite-lhe montar uma partilha de ficheiros que foi criada no armazenamento do Azure para o sistema de ficheiros do Linux. Para obter mais informações sobre estas opções de armazenamento de dados para os serviços de ML no cluster do HDInsight, consulte [opções de armazenamento do Azure para serviços de ML no HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Nó de extremidade de serviços de ML de acesso

Pode ligar para o Microsoft ML Server no nó de extremidade usando um navegador. Ele é instalado por padrão durante a criação do cluster. Para obter mais informações, consulte [obter stared com os serviços de ML no HDInsight](r-server-get-started.md). Também pode ligar ao nó de extremidade do cluster da linha de comandos utilizando SSH/PuTTY para aceder à consola de R.

## <a name="develop-and-run-r-scripts"></a>Desenvolver e executar R scripts

Os scripts R, criar e executar podem utilizar qualquer um dos 8000 + aberto pacotes de R, além das rotinas em paralelo e distribuídos disponíveis na biblioteca do ScaleR. Em geral, um script que é executado com os serviços de ML no nó de extremidade é executado dentro do interpretador de R nesse nó. As exceções são os passos que precisam para chamar uma função de ScaleR com o contexto de cálculo que está definido para o Hadoop mapeamento redução (RxHadoopMR) ou do Spark (RxSpark). Neste caso, a função é executada de maneira distribuída entre os nós de dados (tarefa) do cluster que estão associados com os dados referenciados. Para obter mais informações sobre as opções de contexto de computação diferentes, consulte [opções de contexto para os serviços de ML no HDInsight de computação](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operacionalizar um modelo

Quando a modelação de dados estiver concluída, pode operacionalizar o modelo para fazer previsões para novos dados a partir do Azure ou no local. Este processo é conhecido como de classificação. Classificação pode ser feita no HDInsight, Azure Machine Learning ou no local.

### <a name="score-in-hdinsight"></a>Pontuação no HDInsight

Para classificar no HDInsight, escreva uma função de R que chama o seu modelo para fazer previsões para um novo ficheiro de dados que carregou para a sua conta de armazenamento. Em seguida, guarde as previsões de volta para a conta de armazenamento. Pode executar esta rotina a pedido no nó de extremidade do seu cluster ou utilizando uma tarefa agendada.

### <a name="score-in-azure-machine-learning-aml"></a>Pontuação no Azure Machine Learning (AML)

Para classificar com o Azure Machine Learning, utilize o pacote do Azure Machine Learning R de código-fonte aberto conhecido como [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) para publicar o seu modelo como um serviço web do Azure. Para sua comodidade, este pacote é pré-instalado no nó de extremidade. Em seguida, usar os recursos no Azure Machine Learning para criar uma interface de utilizador para o serviço web e, em seguida, chamar o serviço web, conforme necessário para a classificação.

Se escolher esta opção, tem de converter os objetos de modelo de ScaleR para objetos de modelo equivalente do código-fonte aberto para uso com o serviço web. Utilize as funções de coerção de ScaleR, tais como `as.randomForest()` para modelos baseados em ensemble, para esta conversão.

### <a name="score-on-premises"></a>Pontuação no local

Para classificar no local depois de criar o seu modelo, pode serializar o modelo de R, transferi-lo, anular a serialização e, em seguida, utilizá-lo para novos dados de classificação. Pode classificar dados novos ao utilizar a abordagem descrita anteriormente no [pontuação no HDInsight](#score-in-hdinsight) ou utilizando [serviços web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Manter o cluster

### <a name="install-and-maintain-r-packages"></a>Instalar e manter os pacotes de R

A maioria dos pacotes de R que utiliza é necessárias no nó de extremidade desde a maioria destes passos, os scripts R são executar lá. Para instalar pacotes de R adicionais no nó de extremidade, pode usar o `install.packages()` método em R.

Se estiver apenas a utilizar rotinas da biblioteca de ScaleR no cluster, não normalmente necessário instalar pacotes de R adicionais em nós de dados. No entanto, poderá ter pacotes adicionais para suportar a utilização de **rxExec** ou **RxDataStep** execução em nós de dados.

Nesses casos, os pacotes adicionais podem ser instalados com uma ação de script depois de criar o cluster. Para obter mais informações, consulte [gerir serviços de ML no cluster de HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Alterar as definições de memória do Apache Hadoop MapReduce

Um cluster pode ser modificado para alterar a quantidade de memória que está disponível para os serviços de ML quando está a executar uma tarefa de MapReduce. Para modificar um cluster, utilize a IU do Apache Ambari está disponível através do painel do portal do Azure para o seu cluster. Para obter instruções sobre como aceder à IU do Ambari para o seu cluster, consulte [clusters do HDInsight gerir com a interface do Usuário da Web de Ambari](../hdinsight-hadoop-manage-ambari.md).

Também é possível alterar a quantidade de memória que está disponível nos serviços de ML utilizando comutadores do Hadoop na chamada para **RxHadoopMR** da seguinte forma:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Dimensionar o cluster

Um cluster de serviços de ML existente no HDInsight pode ser aumentado vertical ou horizontalmente, através do portal. Aumente verticalmente, pode obter a capacidade adicional que poderá precisar para tarefas de processamento maiores ou pode reduzir um cluster quando está ociosa. Para obter instruções sobre como dimensionar um cluster, consulte [clusters do HDInsight gerir](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Manter o sistema

Manutenção para aplicar patches de SO e outras atualizações é executada nas VMs subjacentes do Linux num cluster do HDInsight fora do horário comercial. Normalmente, a manutenção é feita em 3H30 (com base na hora local para a VM) cada segunda-feira e quinta-feira. As atualizações são executadas de forma que eles não causam impacto mais de um trimestre do cluster de cada vez.

Uma vez que os nós principais são redundantes e nem todos os nós de dados são afetados, os trabalhos que estão em execução durante este período podem retardar a. No entanto, ainda deve executar para conclusão. Qualquer software personalizado ou dados locais que precisa são preservados entre esses eventos de manutenção, a menos que ocorrer uma falha catastrófica que necessita de reconstrução de um cluster.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opções de IDE para serviços de ML no HDInsight

O nó de extremidade do Linux de um cluster do HDInsight é a zona de destino para a análise baseada em R. Versões recentes do HDInsight fornecem uma instalação padrão do RStudio Server no nó de extremidade como um IDE baseada no browser. Uso do RStudio Server como um IDE para o desenvolvimento e execução de R scripts podem ser consideravelmente mais produtivas do que apenas utilizando a consola de R.

Além disso, pode instalar um IDE de ambiente de trabalho e utilizá-lo para aceder ao cluster através da utilização de um contexto de cálculo remoto MapReduce ou do Spark. As opções incluem da Microsoft [R Tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), do RStudio e Walware baseados no Eclipse [StatET](http://www.walware.de/goto/statet).

Além disso, podem aceder a consola de R no nó de extremidade, escrevendo **R** no prompt de comando do Linux depois de se ligar através de SSH ou PuTTY. Ao utilizar a interface do console, é conveniente executar um editor de texto para o desenvolvimento de script do R na outra janela e recortar e colar o secções do seu script para a consola de R conforme necessário.

## <a name="pricing"></a>Preços

Os preços que estão associados um cluster do HDInsight de serviços do ML estão estruturados do mesmo modo para os preços para outros tipos de cluster do HDInsight. Eles baseiam-se o dimensionamento de VMs subjacentes entre o nome, dados e nós de extremidade, com a adição de um incremento de núcleo-hora. Para obter mais informações, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como utilizar os serviços de ML em clusters do HDInsight, consulte os seguintes tópicos:

* [Introdução ao cluster de serviços de ML no HDInsight](r-server-get-started.md)
* [Opções do contexto de cálculo para o cluster dos Serviços ML no HDInsight](r-server-compute-contexts.md)
* [Opções de armazenamento para os serviços de ML de cluster no HDInsight](r-server-storage.md)
