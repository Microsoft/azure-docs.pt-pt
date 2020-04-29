---
title: Plataformas e ferramentas para projetos de ciência de dados - Team Data Science Process
description: Itemiza e discute os dados e recursos analíticos disponíveis para as empresas normalizadas no Processo de Ciência de Dados da Equipa.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3f3f06bd5a9b6a78b45a63de0684b580e662075b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256523"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Plataformas e ferramentas para projetos de ciência de dados

A Microsoft fornece um espectro completo de recursos de análise para plataformas de cloud ou no local. Podem ser implantados para tornar eficiente e escalável a execução dos seus projetos de ciência de dados. A orientação para equipas que implementam projetos de ciência de dados de forma trackável, controlada e colaborativa é fornecida pelo Processo de Ciência de Dados da [Equipa](overview.md) (TDSP).  Para um esboço das funções de pessoal, e suas tarefas associadas que são tratadas por uma equipa de ciência de dados padronizada neste processo, consulte as [funções e tarefas](roles-tasks.md)do Processo de Ciência de Dados da Equipa.

Os recursos de análise disponíveis para as equipas de ciência de dados que utilizam o TDSP incluem:

- Máquinas virtuais da Ciência dos Dados (tanto Windows como Linux CentOS)
- Aglomerados de faíscas HDInsight
- Synapse Analytics
- Azure Data Lake
- Aglomerados de colmeia HDInsight
- Armazenamento de Ficheiros do Azure
- Serviços SQL Server 2019 R e Python
- Azure Databricks

Neste documento, descrevemos brevemente os recursos e fornecemos ligações aos tutoriais e walkthroughs que as equipas da TDSP publicaram. Eles podem ajudá-lo a aprender a usá-los passo a passo e começar a usá-los para construir suas aplicações inteligentes. Mais informações sobre estes recursos estão disponíveis nas suas páginas de produtos. 

## <a name="data-science-virtual-machine-dsvm"></a>Máquina Virtual da Ciência dos Dados (DSVM)

A máquina virtual de ciência de dados oferecida tanto no Windows como no Linux pela Microsoft, contém ferramentas populares para modelação e desenvolvimento de data science. Inclui ferramentas como:

- Microsoft R Server Developer Edition 
- Distribuição de Anaconda Python
- Cadernos jupyter para Python e R 
- Edição Comunitária de Estúdio Visual com Python e Ferramentas R no Windows / Eclipse no Linux
- Ambiente de trabalho power BI para Windows
- Edição de desenvolvedor do SQL Server 2016 no Windows / Postgres no Linux

Também inclui **ferramentas ML e IA** como xgboost, mxnet e Vowpal Wabbit.

Atualmente o DSVM está disponível nos sistemas **operativos Windows** e **Linux CentOS.** Escolha o tamanho do seu DSVM (número de núcleos de CPU e a quantidade de memória) com base nas necessidades dos projetos de ciência de dados que está a planear executar nele. 

Para obter mais informações sobre a edição do Windows do DSVM, consulte a [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) no Azure Marketplace. Para a edição linux do DSVM, consulte [Linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804).

Para aprender a executar algumas das tarefas comuns de ciência de dados no DSVM de forma eficiente, veja [10 coisas que pode fazer na Máquina Virtual](../data-science-virtual-machine/vm-do-ten-things.md) da Ciência dos Dados


## <a name="azure-hdinsight-spark-clusters"></a>Aglomerados de faíscas Azure HDInsight

O Apache Spark é uma arquitetura de processamento paralelo open source que suporta o processamento dentro da memória para melhorar o desempenho de aplicações de análise de macrodados. O motor de processamento Spark é construído para velocidade, facilidade de utilização e análise sofisticada. As capacidades de computação na memória de Spark fazem dele uma boa escolha para algoritmos iterativos na aprendizagem automática e para computações de gráficos. A faísca também é compatível com o armazenamento Azure Blob (WASB), pelo que os dados existentes armazenados no Azure podem ser facilmente processados usando a Spark.

Quando cria um cluster do Spark no HDInsight, está a criar recursos de computação do Azure com o Spark instalado e configurado. Leva cerca de 10 minutos para criar um cluster Spark no HDInsight. Guarde os dados a serem processados no armazenamento da Blob Azure. Para obter informações sobre a utilização do Armazenamento De Blob Azure com um cluster, consulte Use o [armazenamento Azure Blob compatível com HDFS com Hadoop em HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

A equipa da TDSP da Microsoft publicou dois walkthroughs de ponta a ponta sobre como usar o Azure HDInsight Spark Clusters para construir soluções de ciência de dados, uma usando Python e outra Scala. Para obter mais informações sobre os **Clusters de Faíscas**Azure HDInsight, consulte [a visão geral: Apache Spark no HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Para aprender a construir uma solução de ciência de dados usando **Python** num Cluster de Faíscas Azure HDInsight, consulte [a visão geral da Ciência dos Dados usando a Spark no Azure HDInsight](spark-overview.md). Para aprender a construir uma solução de ciência de dados usando **Scala** num Cluster de Faíscas Azure HDInsight, consulte [data science usando Scala e Spark on Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

O Azure SQL Data Warehouse permite-lhe escalar os recursos da computação de forma fácil e em segundos, sem sobre-provisionamento ou sobre-pagamento. Também oferece a opção única de parar o uso de recursos computacionais, dando-lhe a liberdade de gerir melhor os seus custos de nuvem. A capacidade de implantar recursos computacionais escaláveis permite trazer todos os seus dados para o Azure SQL Data Warehouse. Os custos de armazenamento são mínimos e só pode executar a computação nas partes dos conjuntos de dados que pretende analisar. 

Para mais informações sobre o Armazém de Dados Azure SQL, consulte o site do [SQL Data Warehouse.](https://azure.microsoft.com/services/sql-data-warehouse) Para aprender a construir soluções de análise avançadas de ponta a ponta com o SQL Data Warehouse, consulte o Processo de Ciência de Dados da Equipa em ação: utilizando o [SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

O Lago de Dados Azure é um repositório em toda a empresa de todos os tipos de dados recolhidos num único local, antes de quaisquer requisitos formais, ou esquema imposto. Esta flexibilidade permite que todos os tipos de dados sejam mantidos num lago de dados, independentemente do seu tamanho ou estrutura ou da rapidez com que é ingerido. As organizações podem então usar Hadoop ou análises avançadas para encontrar padrões nestes lagos de dados. Os lagos de dados também podem servir como um repositório para a preparação de dados de baixo custo antes de curar os dados e movê-los para um armazém de dados.

Para mais informações sobre o Lago de Dados Azure, consulte a introdução do Lago de [Dados Azure](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Para aprender a construir uma solução de ciência de dados de ponta a ponta escalável com o Lago de Dados Azure, consulte a Ciência dos Dados Escaláveis no Lago de [Dados Azure: Um Walkthrough](data-lake-walkthrough.md) de ponta a ponta


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Aglomerados de colmeia Azure HDInsight (Hadoop)

Apache Hive é um sistema de armazém de dados para Hadoop, que permite a sumutualização de dados, consulta, e a análise de dados usando hiveQL, uma linguagem de consulta semelhante à SQL. A Colmeia pode ser usada para explorar interativamente os seus dados ou para criar trabalhos de processamento reutilizáveis de lotes.

A Hive permite-lhe projetar a estrutura em dados largamente desestruturados. Depois de definir a estrutura, pode usar a Hive para consultar esses dados num cluster Hadoop sem ter de usar, ou mesmo saber, Java ou MapReduce. A HiveQL (a linguagem da consulta da Colmeia) permite-lhe escrever consultas com declarações semelhantes a T-SQL.

Para os cientistas de dados, a Hive pode executar funções definidas pelo utilizador python (UDFs) em consultas de Hive para processar registos. Esta capacidade alarga consideravelmente a capacidade das consultas da Hive na análise de dados. Especificamente, permite que os cientistas de dados conduzam a engenharia de recursos escaláveis em línguas que estão mais familiarizadas: o HiveQL e python semelhantes ao SQL. 

Para obter mais informações sobre os Clusters de Colmeia Azure HDInsight, consulte [Use Hive e HiveQL com Hadoop em HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Para aprender a construir uma solução de ciência de dados de ponta a ponta escalável com clusters de colmeia Azure HDInsight, consulte o Processo de Ciência de Dados da [Equipa em ação: utilizando clusters hadoop HDInsight](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Armazenamento de Ficheiros do Azure 

O Armazenamento de Ficheiros Azure é um serviço que oferece partilhas de ficheiros na nuvem utilizando o protocolo padrão do Bloco de Mensagens do Servidor (SMB). O SMB 2.1 e o SMB 3.0 são suportados. Com o Armazenamento de ficheiros do Azure, pode migrar aplicações antigas que se baseiam em partilhas de ficheiros para o Azure rapidamente e sem reescritas dispendiosas. As aplicações em execução em máquinas virtuais ou serviços em nuvem do Azure ou em clientes no local podem montar uma partilha de ficheiros na nuvem, tal como uma aplicação de ambiente de trabalho monta uma partilha SMB típica. Em seguida, quaisquer componentes da aplicação podem montar e aceder à partilha do Armazenamento de ficheiros em simultâneo.

Especialmente útil para projetos de ciência de dados é a capacidade de criar uma loja de ficheiros Azure como o local para partilhar dados do projeto com os membros da sua equipa de projeto. Cada um deles tem então acesso à mesma cópia dos dados no armazenamento de ficheiros Azure. Também podem usar este armazenamento de ficheiros para partilhar conjuntos de funcionalidades gerados durante a execução do projeto. Se o projeto for um compromisso com o cliente, os seus clientes podem criar um armazenamento de ficheiros Azure sob a sua própria subscrição Azure para partilhar os dados e funcionalidades do projeto consigo. Desta forma, o cliente tem o controlo total dos ativos de dados do projeto. Para obter mais informações sobre o Armazenamento de Ficheiros Azure, consulte [Start start with Azure File storage on Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) and How to use [Azure File Storage with Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>Serviços SQL Server 2019 R e Python

Os Serviços R (Base de Dados) fornecem uma plataforma para desenvolver e implementar aplicações inteligentes que podem descobrir novos conhecimentos. Você pode usar a linguagem R rica e poderosa, incluindo os muitos pacotes fornecidos pela comunidade R, para criar modelos e gerar previsões a partir dos seus dados do SQL Server. Uma vez que os Serviços R (Base de dados) integram a linguagem R com o SQL Server, a análise é mantida perto dos dados, o que elimina os custos e riscos de segurança associados aos dados em movimento.

Os Serviços R (Base de Dados) suportam a linguagem R de código aberto com um conjunto abrangente de ferramentas e tecnologias Do Servidor SQL. Oferecem desempenho superior, segurança, fiabilidade e gestão. Pode implementar soluções R utilizando ferramentas convenientes e familiares. As suas aplicações de produção podem ligar para o tempo de execução r e recuperar previsões e visuais usando transact-SQL. Também utiliza as bibliotecas ScaleR para melhorar a escala e o desempenho das suas soluções R. Para mais informações, consulte [os Serviços R do Servidor SQL](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

A equipa da TDSP da Microsoft publicou dois walkthroughs de ponta a ponta que mostram como construir soluções de ciência de dados em Serviços R SQL Server 2016: um para programadores R e outro para desenvolvedores SQL. Para **programadores R,** consulte [Data Science End-to-End Walkthrough](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Para **desenvolvedores SQL,** consulte [In-Database Advanced Analytics for SQL Developers (Tutorial)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Apêndice: Ferramentas para criar projetos de ciência de dados

### <a name="install-git-credential-manager-on-windows"></a>Instale git Credential Manager no Windows

Se estiver a seguir o TDSP no **Windows,** precisa de instalar o **Git Credential Manager (GCM)** para comunicar com os repositórios Git. Para instalar o GCM, primeiro é necessário instalar **o Chocolaty**. Para instalar o Chocolaty e o GCM, execute os seguintes comandos no Windows PowerShell como **administrador:**  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Instale git nas máquinas Linux (CentOS)

Executar o seguinte comando de bash para instalar Git nas máquinas Linux (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Gere a chave SSH pública nas máquinas Linux (CentOS)

Se estiver a utilizar máquinas Linux (CentOS) para executar os comandos git, tem de adicionar a chave SSH pública da sua máquina aos seus Serviços Azure DevOps, para que esta máquina seja reconhecida pelos Serviços Azure DevOps. Em primeiro lugar, é necessário gerar uma chave SSH pública e adicionar a chave às chaves públicas SSH na página de definição de segurança dos Serviços Azure DevOps. 

1. Para gerar a chave SSH, execute os seguintes dois comandos: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Comandos para gerar a chave SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Copie toda a chave ssh, incluindo *ssh-rsa*. 
1. Faça login nos seus Serviços Azure DevOps. 
1. Clique **<\> O Seu Nome** no canto superior direito da página e clique em **segurança**. 
    
   ![Clique no seu nome e clique em segurança](./media/platforms-and-tools/resources-2-user-setting.png)

1. Clique em **teclas públicas SSH,** e clique em **+Adicionar**. 

   ![Clique em teclas públicas SSH e, em seguida, clique em +Adicionar](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Colar a chave ssh copiada na caixa de texto e guardar.


## <a name="next-steps"></a>Passos seguintes

São também fornecidos passeios completos de ponta a ponta que demonstrem todos os passos no processo para **cenários específicos.** Estão listados e ligados com descrições de miniaturas no tópico de [walkthroughs exemplo.](walkthroughs.md) Ilustram como combinar cloud, ferramentas no local e serviços num fluxo de trabalho ou oleoduto para criar uma aplicação inteligente. 

Por exemplo, que mostram como executar passos no Processo de Ciência de Dados da Equipa utilizando o Azure Machine Learning Studio (clássico), consulte o caminho de aprendizagem [Com Azure ML.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)
