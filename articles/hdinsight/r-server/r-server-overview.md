---
title: Introdução aos serviços do ML no Azure HDInsight
description: Saiba como usar os serviços do ML no HDInsight para criar aplicativos para análise de Big Data.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/12/2019
ms.openlocfilehash: 5108424c4e39c1c47710c0e25e4e22c4474e68ad
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68941699"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>O que são os serviços do ML no Azure HDInsight

Microsoft Machine Learning Server está disponível como uma opção de implantação quando você cria clusters HDInsight no Azure. O tipo de cluster que fornece essa opção é chamado de **Serviços ml**. Esse recurso fornece aos cientistas de dados, estatísticos e programadores de R o acesso sob demanda a métodos escalonáveis e distribuídos de análise no HDInsight.

Os serviços de ML no HDInsight fornecem os recursos mais recentes para análise baseada em R em conjuntos de valores de praticamente qualquer tamanho, carregados para o blob do Azure ou para o armazenamento de Data Lake. Como o cluster de serviços do ML é criado em R de software livre, os aplicativos baseados em R que você cria podem aproveitar qualquer um dos mais de 8000 pacotes de R de software livre. As rotinas no scaler, o pacote de análise Big Data da Microsoft também estão disponíveis.

O nó de borda de um cluster fornece um local conveniente para se conectar ao cluster e executar os scripts do R. Com um nó de borda, você tem a opção de executar as funções distribuídas paralelizadas do scaler nos núcleos do servidor do nó de borda. Você também pode executá-los em todos os nós do cluster usando a redução de mapa do Hadoop do scaler ou Apache Spark contextos de computação.

Os modelos ou previsões que resultam da análise podem ser baixados para uso local. Eles também podem ser operados em outro lugar no Azure, particularmente por meio do [Azure Machine Learning Studio](https://studio.azureml.net) [Web Service](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Introdução aos serviços de ML no HDInsight

Para criar um cluster de serviços am no Azure HDInsight, selecione o tipo de cluster **serviços de ml** ao criar um cluster HDInsight usando o portal do Azure. O tipo de cluster de serviços ML inclui ML Server nos nós de dados do cluster e em um nó de borda, que serve como uma zona de aterrissagem para análise baseada em serviços ML. Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) para obter instruções sobre como criar o cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Por que escolher os serviços do ML no HDInsight?

Os serviços de ML no HDInsight oferecem os seguintes benefícios:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Inovação de ia da Microsoft e código-fonte aberto

  Os serviços ML incluem um conjunto de algoritmos distribuído e altamente escalonável, como [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)e [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) , que podem funcionar em tamanhos de dados maiores que o tamanho da memória física e ser executados em uma ampla variedade de plataformas em um maneira distribuída. Saiba mais sobre a coleção de pacotes [R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) e [pacotes do Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) personalizados da Microsoft incluídos no produto.
  
  Os serviços de ML se ligam a essas inovações e contribuições da Microsoft provenientes da comunidade de software livre (R, Python e AI kits de notas) tudo sobre uma única plataforma de nível empresarial. Qualquer pacote de aprendizado de máquina aberto em R ou Python pode funcionar lado a lado com qualquer inovação proprietária da Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Administração e operacionalização simples, segura e de grande escala

  As empresas que dependem de paradigmas e ambientes tradicionais investem muito tempo e esforço em direção à operacionalização. Isso resulta em custos inoportunos e atrasos, incluindo o tempo de tradução para modelos, iterações para mantê-los válidos e atuais, aprovação regulatória e gerenciamento de permissões por meio de operacionalização.

  Os serviços de ML oferecem [operacionalização](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)de nível empresarial, nesse caso, depois que um modelo de aprendizado de máquina é concluído, ele leva apenas alguns cliques para gerar APIs de serviços da Web. Esses [Serviços Web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) são hospedados em uma grade de servidor na nuvem e podem ser integrados a aplicativos de linha de negócios. A capacidade de implantar em uma grade elástica permite que você Escale diretamente com as necessidades de sua empresa, tanto para pontuação em lote quanto em tempo real. Para obter instruções, consulte operacionalize os [serviços de ml no HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Principais recursos dos serviços de ML no HDInsight

Os recursos a seguir estão incluídos nos serviços de ML no HDInsight.

| Categoria de recurso | Descrição |
|------------------|-------------|
| Habilitado para R | [Pacotes de r](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) para soluções escritas em r, com uma distribuição de software livre de r e infraestrutura de tempo de execução para execução de script. |
| Habilitado para Python | [Módulos Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) para soluções escritas em Python, com uma distribuição de software livre de Python e infraestrutura de tempo de execução para execução de scripts.
| [Modelos pré-treinados](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Para análise visual e análise de sentimentos de texto, pronto para pontuar os dados fornecidos. |
| [Implantar e consumir](r-server-operationalize.md) | Operacionalize seu servidor e implante soluções como um serviço Web. |
| [Execução remota](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Inicie sessões remotas no cluster de serviços ML na sua rede da estação de trabalho cliente. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opções de armazenamento de dados para serviços de ML no HDInsight

O armazenamento padrão para o sistema de arquivos HDFS de clusters HDInsight pode ser associado a uma conta de armazenamento do Azure ou a uma Azure Data Lake Storage. Essa associação garante que os dados carregados no armazenamento de cluster durante a análise sejam persistentes e os dados estejam disponíveis mesmo depois que o cluster for excluído. Há várias ferramentas para lidar com a transferência de dados para a opção de armazenamento que você seleciona, incluindo o recurso de carregamento baseado em portal da conta de armazenamento e o utilitário [AzCopy](../../storage/common/storage-use-azcopy.md) .

Você tem a opção de habilitar o acesso a armazenamentos de BLOB e data Lake adicionais durante o processo de provisionamento de cluster, independentemente da opção de armazenamento primário em uso.  Consulte o artigo [Opções de armazenamento do Azure para serviços do ml no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) para saber mais sobre como usar várias contas de armazenamento.

Você também pode usar os [arquivos do Azure](../../storage/files/storage-how-to-use-files-linux.md) como uma opção de armazenamento para uso no nó de borda. Os arquivos do Azure permitem montar um compartilhamento de arquivos que foi criado no armazenamento do Azure para o sistema de arquivos do Linux. Para obter mais informações sobre essas opções de armazenamento de dados para serviços de ML no cluster HDInsight, consulte [Opções de armazenamento do Azure para serviços de ml no HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Nó de borda de serviços ML de acesso

Você pode se conectar ao Microsoft ML Server no nó de borda usando um navegador. Ele é instalado por padrão durante a criação do cluster.  Você também pode se conectar ao nó de borda do cluster por meio da linha de comando usando SSH/de saída para acessar o console do R.

## <a name="develop-and-run-r-scripts"></a>Desenvolver e executar scripts do R

Os scripts R que você cria e executa podem usar qualquer um dos mais de 8000 pacotes de software livre do R, além das rotinas paralelizadas e distribuídas disponíveis na biblioteca do scaler. Em geral, um script que é executado com os serviços de ML no nó de borda é executado no intérprete de R nesse nó. As exceções são essas etapas que precisam chamar uma função de scaler com um contexto de computação definido para o RxHadoopMR (redução de mapa do Hadoop) ou Spark (RxSpark). Nesse caso, a função é executada de maneira distribuída entre os nós de dados (tarefa) do cluster que estão associados aos dados referenciados. Para obter mais informações sobre as diferentes opções de contexto de computação, consulte [Opções de contexto de computação para serviços de ml no HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operacionalizar um modelo

Quando a modelagem de dados for concluída, você poderá colocar o modelo em operação para fazer previsões para novos dados do Azure ou do local. Esse processo é conhecido como pontuação. A pontuação pode ser feita no HDInsight, Azure Machine Learning ou no local.

### <a name="score-in-hdinsight"></a>Pontuação no HDInsight

Para pontuar no HDInsight, escreva uma função do R que chame seu modelo para fazer previsões para um novo arquivo de dados que você carregou em sua conta de armazenamento. Em seguida, salve as previsões de volta para a conta de armazenamento. Você pode executar essa rotina sob demanda no nó de borda do cluster ou usando um trabalho agendado.

### <a name="score-in-azure-machine-learning-aml"></a>Pontuação em Azure Machine Learning (AML)

Para pontuar usando Azure Machine Learning, use o pacote de R Azure Machine Learning software livre conhecido como [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) para publicar seu modelo como um serviço Web do Azure. Para sua conveniência, este pacote é pré-instalado no nó de borda. Em seguida, use as instalações no Azure Machine Learning para criar uma interface do usuário para o serviço Web e, em seguida, chame o serviço Web conforme necessário para pontuação.

Se você escolher essa opção, deverá converter qualquer objeto de modelo do scaler em objetos de modelo de código-fonte aberto equivalentes para uso com o serviço Web. Use funções de coerção de scaler, como `as.randomForest()` para modelos baseados em Ensemble, para essa conversão.

### <a name="score-on-premises"></a>Pontuação local

Para pontuar no local depois de criar seu modelo, você pode serializar o modelo em R, baixá-lo, desserializá-lo e, em seguida, usá-lo para Pontuação de novos dados. Você pode pontuar novos dados usando a abordagem descrita anteriormente em [pontuação no HDInsight](#score-in-hdinsight) ou usando [Serviços Web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Manter o cluster

### <a name="install-and-maintain-r-packages"></a>Instalar e manter pacotes de R

A maioria dos pacotes de R que você usa é necessária no nó de borda, uma vez que a maioria das etapas de seus scripts R é executada lá. Para instalar pacotes R adicionais no nó de borda, você pode usar o `install.packages()` método em R.

Se você estiver usando apenas rotinas da biblioteca do scaler no cluster, normalmente não precisará instalar pacotes R adicionais nos nós de dados. No entanto, você pode precisar de pacotes adicionais para dar suporte ao uso da execução de **rxExec** ou **RxDataStep** nos nós de dados.

Nesses casos, os pacotes adicionais podem ser instalados com uma ação de script depois que você cria o cluster. Para obter mais informações, consulte [gerenciar serviços de ml no cluster HDInsight](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Alterar Apache Hadoop configurações de memória do MapReduce

Um cluster pode ser modificado para alterar a quantidade de memória disponível para os serviços de ML quando ele está executando um trabalho MapReduce. Para modificar um cluster, use a interface do usuário do Apache Ambari que está disponível por meio da folha portal do Azure para o cluster. Para obter instruções sobre como acessar a interface do usuário do amAmbari para seu cluster, consulte [gerenciar clusters HDInsight usando a interface do usuário da Web](../hdinsight-hadoop-manage-ambari.md)do amAmbari.

Também é possível alterar a quantidade de memória disponível para os serviços de ML usando os comutadores do Hadoop na chamada para **RxHadoopMR** da seguinte maneira:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Dimensionar o cluster

Um cluster existente dos serviços ML no HDInsight pode ser expandido ou reduzido por meio do Portal. Ao escalar verticalmente, você pode obter a capacidade adicional que pode ser necessária para tarefas de processamento maiores ou pode reduzir um cluster de volta quando ele estiver ocioso. Para obter instruções sobre como dimensionar um cluster, consulte [gerenciar clusters HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Manter o sistema

A manutenção para aplicar patches do sistema operacional e outras atualizações é executada nas VMs do Linux subjacentes em um cluster HDInsight fora do horário comercial. Normalmente, a manutenção é feita às 3:30 (com base na hora local da VM) toda segunda-feira e quinta-feira. As atualizações são executadas de forma que não afetem mais do que um trimestre do cluster de cada vez.

Como os nós de cabeçalho são redundantes e nem todos os nós de dados são afetados, todos os trabalhos em execução durante esse tempo podem ficar mais lentos. No entanto, eles ainda devem ser executados até a conclusão. Qualquer software personalizado ou dados locais que você tenha são preservados nesses eventos de manutenção, a menos que ocorra uma falha catastrófica que exija uma recompilação do cluster.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opções de IDE para serviços de ML no HDInsight

O nó de borda do Linux de um cluster HDInsight é a zona de aterrissagem para análise baseada em R. As versões recentes do HDInsight fornecem uma instalação padrão do RStudio Server no nó de borda como um IDE baseado em navegador. O uso do RStudio Server como um IDE para o desenvolvimento e a execução de scripts R pode ser consideravelmente mais produtivo do que apenas usar o console do R.

Além disso, você pode instalar um IDE de área de trabalho e usá-lo para acessar o cluster por meio do uso de um contexto de computação do Spark ou MapReduce remoto. As opções incluem o [estado](http://www.walware.de/goto/statet)baseado em eclipse da Microsoft [Ferramentas do R para Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS), RStudio e statet baseado no.

Além disso, você pode acessar o console do R no nó de borda digitando **R** no prompt de comando do Linux após a conexão via SSH ou de saída. Ao usar a interface do console, é conveniente executar um editor de texto para o desenvolvimento de script R em outra janela e recortar e colar seções do seu script no console do R, conforme necessário.

## <a name="pricing"></a>Preços

Os preços associados a um cluster HDInsight de serviços ML são estruturados de forma semelhante aos preços de outros tipos de cluster HDInsight. Elas se baseiam no dimensionamento das VMs subjacentes entre os nós nome, dados e borda, com a adição de um upgrade de hora central. Para obter mais informações, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como usar os serviços de ML em clusters HDInsight, consulte os seguintes tópicos:

* [Executar um script do R em um cluster de serviços de ML no Azure HDInsight usando o servidor RStudio](machine-learning-services-quickstart-job-rstudio.md)
* [Opções do contexto de cálculo para o cluster dos Serviços ML no HDInsight](r-server-compute-contexts.md)
* [Opções de armazenamento para o cluster de serviços am no HDInsight](r-server-storage.md)
