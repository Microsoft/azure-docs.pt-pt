---
title: Guia do programador do R para o Azure - programação R | Documentos da Microsoft
description: Este artigo fornece uma descrição geral das várias formas que os cientistas de dados podem aproveitar suas habilidades existentes com o R linguagem no Azure de programação. O Azure oferece vários serviços que os desenvolvedores de R podem aproveitar para expandir as cargas de trabalho de ciência de dados para a cloud.
services: machine-learning
documentationcenter: ''
author: AnalyticJeremy
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: R
ms.topic: article
ms.date: 09/12/2018
ms.author: jepeach
ms.openlocfilehash: 70fc78fb515c56f0b3102bb006eb6491a664babd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886692"
---
# <a name="r-developers-guide-to-azure"></a>Guia para programadores de R para o Azure
<img src="media/r-developers-guide/logo_r.svg" alt="R logo" align="right" width="200" />

Lidando com cada vez os cientistas de dados muitos volumes de dados estão buscando formas de tirar partido do poder da informática na cloud para as suas análises.  Este artigo fornece uma descrição geral das várias formas que os cientistas de dados podem aproveitar suas habilidades existentes com o [linguagem de programação R](https://www.r-project.org) no Azure.

Microsoft totalmente ADOTOU a linguagem de programação R como ferramenta de primeira classe para os cientistas de dados.  Ao fornecer muitas opções diferentes para programadores de R executar seu código no Azure, a empresa é permitir que os cientistas de dados expandir as cargas de trabalho de ciência de dados para a cloud ao lidar com projetos de grande escala.

Vamos examinar as várias opções e os cenários mais atraentes para cada um deles.

## <a name="azure-services-with-r-language-support"></a>Serviços do Azure com suporte de linguagem R
Este artigo abrange os seguintes serviços do Azure que suportam a linguagem R de:

|Serviço                                                          |Descrição                                                                       |
|-----------------------------------------------------------------|----------------------------------------------------------------------------------|
|[Máquina de Virtual de ciência de dados](#data-science-virtual-machine)    |uma VM personalizada para utilizar como uma estação de trabalho de ciência de dados ou como um destino de computação personalizada|
|[Serviços de ML no HDInsight](#ml-services-on-hdinsight)            |sistema baseado em cluster para executar análises de R em grandes conjuntos de dados por muitos nós   |
|[Azure Databricks](#azure-databricks)                            |ambiente de colaboração do Spark que suporte o R e outros idiomas               |
|[Azure Machine Learning Studio](#azure-machine-learning-studio)  |executar scripts personalizados do R no experimentações de machine do Azure learning                      |
|[Azure Batch](#azure-batch)                                      |oferece uma variedade de opções para a execução, de forma económica, código R por muitos nós num cluster|
|[Azure Notebooks](#azure-notebooks)                              |uma versão de com base na cloud sem custos de blocos de notas do Jupyter                  |
|[Base de Dados SQL do Azure](#azure-sql-database)                        |executar scripts R dentro do motor de base de dados do SQL Server                            |

## <a name="data-science-virtual-machine"></a>Máquina Virtual de Ciência de Dados
O [máquina de Virtual de ciência de dados](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) (DSVM) é uma imagem VM personalizada na plataforma de cloud do Azure da Microsoft criada especificamente para fazer ciência de dados. Ele tem muitas ferramentas de ciência de dados populares, incluindo:
* [Microsoft R Open](https://mran.microsoft.com/open/)
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)
* [Ambiente de trabalho do RStudio](https://www.rstudio.com/products/rstudio/#Desktop)
* [R Studio Server](https://www.rstudio.com/products/rstudio/#Server)

A DSVM pode ser aprovisionada com o Windows ou Linux como o sistema operativo.  Pode usar a DSVM de duas formas diferentes: como uma estação de trabalho interativa ou como uma plataforma de computação para um cluster personalizado.

### <a name="as-a-workstation"></a>Como uma estação de trabalho
Se pretender começar com R na cloud rápida e facilmente, esta é sua melhor aposta.  O ambiente será familiar para qualquer pessoa que já trabalhou com R numa estação de trabalho local.  No entanto, em vez de utilizar recursos locais, o ambiente de R é executado numa VM na cloud.  Se os dados já estão armazenados no Azure, isso tem o benefício agregado de permitir que os scripts R executar "mais perto dos dados." Em vez de transferir os dados através da Internet, os dados podem ser acedidos através de rede interno do Azure, que fornece muito mais rápidas horas de acesso.

A DSVM pode ser particularmente útil para pequenas equipes de desenvolvedores de R.  Em vez de investir em estações de trabalho eficientes para cada desenvolvedor e exigir que os membros da Equipe sincronizar em quais versões de vários pacotes de software, será utilizado, cada desenvolvedor pode acelerar uma instância da DSVM sempre que necessário.

### <a name="as-a-compute-platform"></a>Como uma plataforma de computação
Além de ser usado como uma estação de trabalho, a DSVM também é utilizada como uma plataforma de computação dimensionável para projetos de R.  Usando o <code>[AzureDSVM](https://github.com/Azure/AzureDSVM)</code> pacote de R, pode controlar programaticamente a criação e eliminação de instâncias DSVM.  Pode formar as instâncias num cluster e implementar uma análise distribuída a serem executadas na cloud.  Todo este processo pode ser controlado pelo código de R em execução na sua estação de trabalho local.

Para saber mais sobre a DSVM, consulte o ["Introdução à máquina Virtual de ciência de dados do Azure para Linux e Windows."](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)

## <a name="ml-services-on-hdinsight"></a>Serviços ML no HDInsight
[Serviços do Microsoft ML](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview) fornecem a cientistas de dados, estatísticos e programadores de R com acesso a pedido dimensionável, distribuído métodos de análise no HDInsight.  Esta solução oferece as capacidades mais recentes para análise baseada em R em conjuntos de dados de virtualmente qualquer tamanho, carregado para o armazenamento de Blobs do Azure ou o Data Lake.

Esta é uma solução de nível empresarial que permite-lhe ajustar o seu código de R num cluster.  Ao tirar partido das funções da Microsoft
<code>[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)</code> pacote, os scripts R no HDInsight podem executar as funções de processamento de dados em paralelo por muitos nós num cluster.  Isso permite que R dados processe numa escala muito maior que é possível com R de um único thread em execução numa estação de trabalho.

Esta capacidade de dimensionar torna os serviços de ML no HDInsight uma ótima opção para programadores de R com conjuntos de dados maciços.  Ele fornece uma plataforma escalonável e flexível para executar os scripts R na cloud.

Para um passo a passo sobre como criar um cluster de serviços de ML, consulte a ["Comece com serviços de ML no Azure HDInsight"](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-get-started) artigo.

## <a name="azure-databricks"></a>Azure Databricks
O [Azure Databricks](https://azure.microsoft.com/services/databricks/) é uma plataforma de análise baseada no Apache Spark e otimizada para a plataforma de serviços cloud Microsoft Azure.  Concebida com os fundadores do Apache Spark, o Databricks está integrado com o Azure para prestar configurações com um clique, fluxos de trabalho fluídos e uma área de trabalho interativa que permite a colaboração entre cientistas de dados, engenheiros de dados e analistas empresariais.

A colaboração no Databricks está ativada por sistema de bloco de notas da plataforma.  Os utilizadores podem criar, partilhar e editar blocos de notas com outros utilizadores dos sistemas.  Estes blocos de notas permitem aos utilizadores escrever código que é executado em relação a clusters do Spark geridos no ambiente do Databricks.  Estes blocos de notas totalmente suportam R e dar aos utilizadores acesso ao Spark através de ambos os `SparkR` e `sparklyr` pacotes.

Uma vez que o Databricks baseia-se no Spark e tem grande foco em colaboração, a plataforma é frequentemente utilizada pelas equipas de cientistas de dados que funcionam em conjunto em análises complexas de grandes conjuntos de dados.  Como os blocos de notas no Databricks suportam outros idiomas além do R, é especialmente útil para equipes onde analistas utilizam idiomas diferentes para o trabalho principal.

O artigo ["Qual é o Azure Databricks?"](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)
Pode fornecer mais detalhes sobre a plataforma e o ajudam a começar.

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning studio
[O Azure Machine Learning studio](https://azure.microsoft.com/services/machine-learning-studio/) é uma ferramenta de colaboração, de arrastar e largar que pode utilizar para criar, testar e implementar soluções de Análise Preditiva na cloud.  Permite que os cientistas de dados emergentes criar e implementar modelos de aprendizagem automática sem a necessidade de escrever muito código.

O Azure Machine Learning studio suporta R e Python.  Pode utilizar o R com o Azure Machine Learning studio de duas formas.

### <a name="custom-r-scripts-in-your-experiments"></a>Scripts de personalizado de R em suas experimentações
Em primeiro lugar, pode estender a manipulação de dados e capacidades de machine learning do ML Studio ao escrever scripts personalizados do R.
Embora o ML Studio inclui uma ampla variedade de módulos para preparar e análise de dados, ele não pode corresponder as capacidades de uma linguagem madura como R.  Por conseguinte, o serviço foi projetado para que possa introduzir os seus próprios scripts personalizados do R em casos em que não cumprem os módulos fornecidos suas necessidades.

Para tirar partido desta capacidade, arrastar e soltar um módulo de "Executar o Script R" na sua experimentação.  Em seguida, utilize o editor de código no painel "Propriedades" para criar um novo script de R ou colar um script existente.  No script, pode referenciar os pacotes de R externos.  Pode utilizar o script a manipulação de dados ou a criar modelos de ML complexos que não fazem parte da biblioteca padrão de modelo no Azure Machine Learning studio.

Para obter uma introdução completa sobre como utilizar o R no ML Studio experimentações, confira o ["Tutorial de início rápido para o idioma para o Azure Machine Learning studio de programação R".](https://docs.microsoft.com/azure/machine-learning/studio/r-quickstart)

### <a name="create-manage-and-deploy-experiments-from-your-local-r-environment"></a>Criar, gerir e implementar experimentações do seu ambiente local do R
A outra forma, que pode utilizar R com o Azure Machine Learning studio é usar o
<code>[AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)</code> pacote para monitorizar e controlar o processo de experimentação com o ambiente de programação R.  Este pacote, o que é mantido pela Microsoft, permite-lhe carregar e transferir os conjuntos de dados do Azure Machine Learning studio, para interrogar experimentações, publicar R funciona como serviços da web e para executar o R dados existentes através de serviços da web em obter o saída.

Este pacote torna muito mais fácil de utilizar o Azure Machine Learning studio como uma plataforma de implantação escalonável para o seu código de R.  Em vez de clicar e arrastar na interface do Usuário, é possível automatizar o processo de implantação completa usando ferramentas que já conhece.

## <a name="azure-batch"></a>Azure Batch
Para as tarefas de R em grande escala, pode usar [do Azure Batch](https://azure.microsoft.com/services/batch/).  Este serviço fornece gestão de computação e agendamento de tarefa de escala da cloud, pelo que pode dimensionar a sua carga de trabalho de R em dezenas, centenas ou milhares de máquinas virtuais.  Uma vez que é uma plataforma de computação generalizada, existem algumas opções para tarefas de R em execução no Azure Batch.

Uma opção é utilizar da Microsoft <code>[doAzureParallel](https://github.com/Azure/doAzureParallel)</code> pacote.  Este pacote de R é um back-end paralelo para o `foreach` pacote.  Ele permite que cada iteração do `foreach` loop para executar em paralelo num nó no cluster do Azure Batch.  Para obter uma introdução ao pacote, leia o ["doAzureParallel: Tire partido da computação de flexível do Azure diretamente a partir de sua sessão de R"](https://azure.microsoft.com/blog/doazureparallel/) postagem de blog.

Outra opção para executar um script R no Azure Batch é agrupar o seu código com "RScript.exe" como uma aplicação do Batch no portal do Azure.  Para instruções detalhadas, consulte ["R cargas de trabalho no Azure Batch".](https://azure.microsoft.com/blog/r-workloads-on-azure-batch/)

Uma terceira opção é utilizar o [Toolkit do Azure distribuídas dados engenharia](https://github.com/Azure/aztk) (AZTK), que permite-lhe aprovisionar clusters do Spark a pedido com contentores do Docker no Azure Batch.  Isso fornece uma forma económica para executar tarefas do Spark no Azure.  Usando [SparklyR com AZTK](https://github.com/Azure/aztk/wiki/SparklyR-on-Azure-with-AZTK), os scripts R podem ser aumentados horizontalmente na cloud facilmente e de forma económica.

## <a name="azure-notebooks"></a>Azure Notebooks

[Blocos de notas do Azure](https://notebooks.azure.com) é um método de baixo custo, de baixa fricção para desenvolvedores de R que preferem trabalhar com blocos de notas para utilizar código próprio para o Azure.  É um serviço gratuito para qualquer pessoa desenvolver e executar código no seu navegador usando [Jupyter](https://jupyter.org/), que é um projeto de código-fonte aberto que permite que combinam prosa de markdown, código executável e gráficos para uma única tela.

O escalão de serviço gratuito de blocos de notas do Azure é uma opção viável para projetos de pequena escala, como ela limita o processo de cada bloco de anotações para 4GB de memória e 1GB de conjuntos de dados. Se precisar de energia de dados e computação além destas limitações, no entanto, pode executar blocos de notas numa instância de máquina de Virtual de ciência de dados. Para obter mais informações, consulte [gerir e configurar projetos de blocos de notas do Azure - camada de computação](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure
[Base de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) é o serviço de base de dados do Microsoft cloud relacional completamente gerido inteligente.  Pode usar toda a potência do SQL Server sem qualquer necessidade de configurar a infraestrutura.  Isto inclui [Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning?view=sql-server-2017), que é uma das adições mais recentes para o serviço do SQL.

Esta funcionalidade oferece um embedded, preditivo análises e dados de ciência motor que pode executar o código de R dentro de uma base de dados do SQL Server como procedimentos armazenados, como scripts T-SQL que contém instruções de R ou como código de R que contém o T-SQL.  Em vez de extração de dados da base de dados e carregá-lo no ambiente de R, que carregar o seu código de R diretamente no banco de dados e permitir que ele seja executado diretamente em conjunto com os dados.

Embora o Machine Learning Services tem sido parte do SQL Server no local desde 2016, é relativamente novo para a base de dados do Azure SQL.  Ele está atualmente em [pré-visualização limitada](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#azure-sql-database-roadmap) , mas continuará a evoluir.


### <a name="next-steps"></a>Passos Seguintes
* [Execução do seu código de R no Azure com mrsdeploy](https://blog.revolutionanalytics.com/2017/03/running-your-r-code-azure.html)
* [Machine Learning Server na Cloud](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-in-the-cloud)
* [Recursos adicionais para Machine Learning Server e Microsoft R](https://docs.microsoft.com/machine-learning-server/resources-more)
* [R no Azure](https://github.com/yueguoguo/r-on-azure) – uma descrição geral de pacotes, ferramentas e estudos de caso para utilizar o R com o Azure

---

<sub>É o logótipo do R &copy; 2016 o R Foundation e é usado de acordo com os termos do [licença Creative Commons o Attribution ShareAlike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/).</sub>
