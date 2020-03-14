---
title: Plataformas e ferramentas para projetos de ciência de dados - Team Data Science Process
description: Detalha e aborda os recursos de dados e análise disponíveis para as empresas a padronizar o processo de ciência de dados de equipa.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3297319c67ad2b7c94371356cde49113c7ef737
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251612"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Plataformas e ferramentas para projetos de ciência de dados

A Microsoft fornece um espectro completo de recursos de análise para plataformas de cloud ou no local. Eles podem ser implementados para tornar a execução dos seus projetos de ciência de dados eficiente e dimensionável. A orientação para equipas que implementam projetos de ciência de dados de forma trackável, controlada e colaborativa é fornecida pelo Processo de Ciência de Dados da [Equipa](overview.md) (TDSP).  Para um esboço das funções de pessoal, e suas tarefas associadas que são tratadas por uma equipa de ciência de dados padronizada neste processo, consulte as [funções e tarefas](roles-tasks.md)do Processo de Ciência de Dados da Equipa.

Os recursos de análise disponíveis para as equipas de ciência de dados que utilizam o TDSP incluem:

- Máquinas de virtuais de ciência dados (Windows e Linux CentOS)
- Clusters do HDInsight Spark
- Synapse Analytics
- Azure Data Lake
- Clusters do HDInsight Hive
- Armazenamento de Ficheiros do Azure
- Serviços SQL Server 2019 R e Python
- Azure Databricks

Neste documento, podemos resumidamente descrevem os recursos e fornecem ligações para os tutoriais e orientações passo a passo, as equipes TDSP tem publicado. Eles podem ajudá-lo a aprender a usá-los passo a passo e começar a utilizá-los para criar as suas aplicações inteligentes. Obter mais informações sobre esses recursos estão disponíveis nas páginas de produto. 

## <a name="data-science-virtual-machine-dsvm"></a>Máquina de Virtual de ciência de dados (DSVM)

A máquina de virtual de ciência de dados oferecidos no Windows e Linux pela Microsoft, contém ferramentas populares para atividades de modelação e desenvolvimento de ciência de dados. Ele inclui ferramentas, tais como:

- Microsoft R Server Developer Edition 
- Distribuição do anaconda Python
- Blocos de notas do Jupyter para Python e R 
- Edição de Comunidade do Visual Studio com Python e ferramentas de R em Windows / Eclipse no Linux
- Ambiente de trabalho do Power BI para Windows
- SQL Server 2016 Developer Edition no Windows / Postgres no Linux

Também inclui **ferramentas ML e IA** como xgboost, mxnet e Vowpal Wabbit.

Atualmente o DSVM está disponível nos sistemas **operativos Windows** e **Linux CentOS.** Escolha o tamanho da sua DSVM (número de núcleos de CPU) e a quantidade de memória com base nas necessidades dos projetos de ciência de dados que pretende executar no mesmo. 

Para obter mais informações sobre a edição do Windows do DSVM, consulte a [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) no Azure Marketplace. Para a edição linux do DSVM, consulte [Linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Para aprender a executar algumas das tarefas comuns de ciência de dados no DSVM de forma eficiente, veja [10 coisas que pode fazer na Máquina Virtual](../data-science-virtual-machine/vm-do-ten-things.md) da Ciência dos Dados


## <a name="azure-hdinsight-spark-clusters"></a>Clusters do Azure HDInsight Spark

O Apache Spark é um paralelo de código-fonte aberto arquitetura que suporta o processamento dentro da memória para melhorar o desempenho de aplicações de análise de macrodados de processamento. O motor de processamento do Spark foi concebido para velocidade, facilidade de utilização e análise sofisticadas. Recursos de computação dentro da memória do Spark tornam uma boa opção para algoritmos iterativos no machine learning e para computações de gráfico. O Spark também é compatível com o armazenamento de Blobs do Azure (WASB), para que os dados existentes armazenados no Azure podem ser facilmente processados com o Spark.

Quando cria um cluster do Spark no HDInsight, está a criar recursos de computação do Azure com o Spark instalado e configurado. Demora cerca de 10 minutos para criar um cluster do Spark no HDInsight. Store os dados a ser processados no armazenamento de Blobs do Azure. Para obter informações sobre a utilização do Armazenamento De Blob Azure com um cluster, consulte Use o [armazenamento Azure Blob compatível com HDFS com Hadoop em HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

Equipe do TDSP da Microsoft publicou dois orientações passo a passo-a-ponto sobre como utilizar os Clusters do HDInsight Spark para Azure para criar soluções de ciência de dados, um que utilize Python e a outro Scala. Para obter mais informações sobre os **Clusters de Faíscas**Azure HDInsight, consulte [a visão geral: Apache Spark no HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Para aprender a construir uma solução de ciência de dados usando **Python** num Cluster de Faíscas Azure HDInsight, consulte [a visão geral da Ciência dos Dados usando a Spark no Azure HDInsight](spark-overview.md). Para aprender a construir uma solução de ciência de dados usando **Scala** num Cluster de Faíscas Azure HDInsight, consulte [data science usando Scala e Spark on Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

O Azure SQL Data Warehouse permite-lhe dimensionar recursos de computação facilmente e, em segundos, sem sobreaprovisionar ou pagar demasiado. Ele também oferece a opção exclusiva de colocar em pausa a utilização de recursos de computação, o que lhe dá a liberdade para melhor gerir os seus custos na cloud. A capacidade de implementar recursos de computação escalável torna possível reunir todos os dados para o Azure SQL Data Warehouse. Os custos de armazenamento são mínimos, e pode executar a computação apenas nas partes de conjuntos de dados que pretende analisar. 

Para mais informações sobre o Armazém de Dados Azure SQL, consulte o site do [SQL Data Warehouse.](https://azure.microsoft.com/services/sql-data-warehouse) Para aprender a construir soluções de análise avançadas de ponta a ponta com o SQL Data Warehouse, consulte o Processo de Ciência de Dados da Equipa em ação: utilizando o [SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

O Lago de Dados Azure é um repositório em toda a empresa de todos os tipos de dados recolhidos num único local, antes de quaisquer requisitos formais, ou esquema imposto. Essa flexibilidade permite que todos os tipos de dados ser mantidos num data lake, independentemente do porte e estrutura ou da rapidez com que é ingerido. As organizações podem, em seguida, utilizar o Hadoop ou análise avançada para encontrar padrões nestes dados lakes. Lakes de dados também podem servir como um repositório para preparação de dados de custo mais baixo e antes de moderação os dados e movê-lo para um armazém de dados.

Para mais informações sobre o Lago de Dados Azure, consulte a introdução do Lago de [Dados Azure](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Para aprender a construir uma solução de ciência de dados de ponta a ponta escalável com o Lago de Dados Azure, consulte a Ciência dos Dados Escaláveis no Lago de [Dados Azure: Um Walkthrough](data-lake-walkthrough.md) de ponta a ponta


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Clusters do Azure Hive do HDInsight (Hadoop)

Apache Hive é um sistema de armazém de dados para o Hadoop, o que permite o resumo de dados, consulta e a análise de dados usando o HiveQL, uma linguagem de consulta semelhante ao SQL. Hive pode ser utilizado para explorar interativamente os seus dados ou para criar tarefas de processamento de batch reutilizável.

Hive permite-lhe a estrutura do projeto nos dados em grande parte não estruturados. Depois de definir a estrutura, pode utilizar o Hive para consultar os dados num cluster do Hadoop sem ter de utilizar, ou até mesmo saber, Java ou do MapReduce. HiveQL (a linguagem de consulta do Hive) permite-lhe escrever consultas com as instruções que são semelhantes ao T-SQL.

Para os cientistas de dados, Hive pode executar funções Python User-Defined (UDFs) em consultas do Hive para processar registros. Esta capacidade expande consideravelmente a capacidade de consultas do Hive em análise de dados. Especificamente, ele permite que os cientistas de dados realizar a engenharia de funcionalidades dimensionável em idiomas principalmente estão familiarizados com: o HiveQL de tipo SQL e Python. 

Para obter mais informações sobre os Clusters de Colmeia Azure HDInsight, consulte [Use Hive e HiveQL com Hadoop em HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Para aprender a construir uma solução de ciência de dados de ponta a ponta escalável com clusters de colmeia Azure HDInsight, consulte o Processo de Ciência de Dados da [Equipa em ação: utilizando clusters hadoop HDInsight](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Armazenamento de Ficheiros do Azure 

Armazenamento de ficheiros do Azure é um serviço que oferece partilhas de ficheiros na cloud utilizando o protocolo de bloco de mensagem de servidor (SMB) padrão. O SMB 2.1 e o SMB 3.0 são suportados. Com o Armazenamento de ficheiros do Azure, pode migrar aplicações antigas que se baseiam em partilhas de ficheiros para o Azure rapidamente e sem reescritas dispendiosas. As aplicações em execução em máquinas virtuais ou serviços em nuvem do Azure ou em clientes no local podem montar uma partilha de ficheiros na nuvem, tal como uma aplicação de ambiente de trabalho monta uma partilha SMB típica. Em seguida, quaisquer componentes da aplicação podem montar e aceder à partilha do File Storage em simultâneo.

Especialmente útil para projetos de ciência de dados é a capacidade de criar um armazenamento de ficheiros do Azure como o local para partilhar dados de projeto com os membros da equipe de projeto. Em seguida, cada um deles tem acesso para a mesma cópia dos dados no armazenamento de ficheiros do Azure. Também podem utilizar o armazenamento de ficheiros para partilhar os conjuntos de recursos gerados durante a execução do projeto. Se o projeto é um compromisso de cliente, os seus clientes podem criar um armazenamento de ficheiros do Azure na sua própria subscrição do Azure para partilhar os dados do projeto e recursos com o utilizador. Dessa forma, o cliente tem controlo total sobre os recursos de dados do projeto. Para obter mais informações sobre o Armazenamento de Ficheiros Azure, consulte [Start start with Azure File storage on Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) and How to use [Azure File Storage with Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>Serviços SQL Server 2019 R e Python

Os Serviços R (Base de Dados) fornecem uma plataforma para desenvolver e implementar aplicações inteligentes que podem descobrir novos conhecimentos. Pode utilizar a linguagem de R diversificada e poderosa, incluindo muitos pacotes fornecidos pela Comunidade do R, para criar modelos e gerar predições a partir dos seus dados do SQL Server. Uma vez que os Serviços R (Base de dados) integram a linguagem R com o SQL Server, a análise é mantida perto dos dados, o que elimina os custos e riscos de segurança associados aos dados em movimento.

Os Serviços R (Base de Dados) suportam a linguagem R de código aberto com um conjunto abrangente de ferramentas e tecnologias Do Servidor SQL. Eles oferecem desempenho superior, segurança, confiabilidade e capacidade de gestão. Pode implementar soluções de R com ferramentas convenientes e familiares. As aplicações de produção podem chamar o tempo de execução do R e obter previsões e os elementos visuais com o Transact-SQL. Também é usar as bibliotecas de ScaleR para melhorar o dimensionamento e desempenho das suas soluções de R. Para mais informações, consulte [os Serviços R do Servidor SQL](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

A equipe do TDSP da Microsoft publicou dois ponto-a-ponto orientações passo a passo que mostram como criar soluções de ciência de dados do SQL Server 2016 R Services: um para programadores de R e outro para os desenvolvedores de SQL. Para **programadores R,** consulte [Data Science End-to-End Walkthrough](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Para **desenvolvedores SQL,** consulte [In-Database Advanced Analytics for SQL Developers (Tutorial)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix"></a>Apêndice: Ferramentas para criar projetos de ciência de dados

### <a name="install-git-credential-manager-on-windows"></a>Instalar Gestor de credenciais do Git no Windows

Se estiver a seguir o TDSP no **Windows,** precisa de instalar o **Git Credential Manager (GCM)** para comunicar com os repositórios Git. Para instalar o GCM, primeiro é necessário instalar **o Chocolaty**. Para instalar o Chocolaty e o GCM, execute os seguintes comandos no Windows PowerShell como **administrador:**  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Instalar o Git em máquinas do Linux (CentOS)

Execute o seguinte comando do bash para instalar o Git em máquinas do Linux (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Gerar a chave SSH pública em máquinas do Linux (CentOS)

Se estiver a utilizar máquinas do Linux (CentOS) para executar os comandos do git, terá de adicionar a chave pública de SSH do seu computador para seus serviços de DevOps do Azure, para que esta máquina é reconhecida pelos serviços de DevOps do Azure. Em primeiro lugar, terá de gerar uma chave SSH pública e adicione a chave para as chaves públicas SSH na sua página de definições de segurança de serviços de DevOps do Azure. 

1. Para gerar a chave SSH, execute os dois comandos seguintes: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Comandos para gerar a chave SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Copie toda a chave ssh, incluindo *ssh-rsa*. 
1. Inicie sessão para os serviços de DevOps do Azure. 
1. Clique **em <O seu nome\>** no canto superior direito da página e clique em **segurança**. 
    
   ![Clique no seu nome e, em seguida, clique em segurança](./media/platforms-and-tools/resources-2-user-setting.png)

1. Clique em **teclas públicas SSH,** e clique em **+Adicionar**. 

   ![Clique em chaves públicas SSH e, em seguida, clique em + Adicionar](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Colar a chave ssh copiada na caixa de texto e guardar.


## <a name="next-steps"></a>Passos seguintes

São também fornecidos passeios completos de ponta a ponta que demonstrem todos os passos no processo para **cenários específicos.** Estão listados e ligados com descrições de miniaturas no tópico de [walkthroughs exemplo.](walkthroughs.md) Eles ilustram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

Por exemplo, que mostram como executar passos no Processo de Ciência de Dados da Equipa utilizando o Azure Machine Learning Studio (clássico), consulte o caminho de aprendizagem [Com Azure ML.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)
