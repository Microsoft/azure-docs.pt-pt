---
title: Plataformas e ferramentas para projetos de ciência de dados - Team Data Science Process
description: Detalha e aborda os recursos de dados e análise disponíveis para as empresas a padronizar o processo de ciência de dados de equipa.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 31c6c1258b6d20eb2d2802cf23244cb59df682c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61428729"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Plataformas e ferramentas para projetos de ciência de dados

A Microsoft oferece uma gama completa de serviços de dados e análise e recursos para plataformas de cloud ou no local. Eles podem ser implementados para tornar a execução dos seus projetos de ciência de dados eficiente e dimensionável. Orientações para as equipes de implementar projetos de ciência de dados num passível de controlo, versão, e controlados forma colaboração fornecida pelos [Team Data Science Process](overview.md) (TDSP).  Para um destaque das funções de equipe e das respetivas tarefas associadas que são processadas por um dados ciência equipe padronização sobre esse processo, consulte [Team Data Science Process funções e tarefas](roles-tasks.md).

Os serviços de dados e análise disponíveis para as equipas de ciência de dados com o TDSP incluem:

- Máquinas de virtuais de ciência dados (Windows e Linux CentOS)
- Clusters do HDInsight Spark
- SQL Data Warehouse
- Azure Data Lake
- Clusters do HDInsight Hive
- Armazenamento de Ficheiros do Azure
- O SQL Server 2016 R Services

Neste documento, podemos resumidamente descrevem os recursos e fornecem ligações para os tutoriais e orientações passo a passo, as equipes TDSP tem publicado. Eles podem ajudá-lo a aprender a usá-los passo a passo e começar a utilizá-los para criar as suas aplicações inteligentes. Obter mais informações sobre esses recursos estão disponíveis nas páginas de produto. 

## <a name="data-science-virtual-machine-dsvm"></a>Máquina de Virtual de ciência de dados (DSVM)

A máquina de virtual de ciência de dados oferecidos no Windows e Linux pela Microsoft, contém ferramentas populares para atividades de modelação e desenvolvimento de ciência de dados. Ele inclui ferramentas, tais como:

- Microsoft R Server Developer Edition 
- Distribuição do anaconda Python
- Blocos de notas do Jupyter para Python e R 
- Edição de Comunidade do Visual Studio com Python e ferramentas de R em Windows / Eclipse no Linux
- Ambiente de trabalho do Power BI para Windows
- SQL Server 2016 Developer Edition no Windows / Postgres no Linux

Ele também inclui **ferramentas de ML e AI** como xgboost, mxnet e Vowpal Wabbit.

Atualmente está disponível no DSVM **Windows** e **Linux CentOS** sistemas operativos. Escolha o tamanho da sua DSVM (número de núcleos de CPU) e a quantidade de memória com base nas necessidades dos projetos de ciência de dados que pretende executar no mesmo. 

Para obter mais informações sobre a edição do Windows da DSVM, consulte [máquina de Virtual de ciência de dados do Microsoft](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) no Azure marketplace. Para a edição Linux da DSVM, consulte [máquina de Virtual de ciência de dados do Linux](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Para saber como executar algumas das tarefas de ciência de dados comuns com eficiência em DSVM, veja [dez coisas que pode fazer na máquina Virtual de ciência de dados](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Clusters do Azure HDInsight Spark

O Apache Spark é um paralelo de código-fonte aberto arquitetura que suporta o processamento dentro da memória para melhorar o desempenho de aplicações de análise de macrodados de processamento. O motor de processamento do Spark foi concebido para velocidade, facilidade de utilização e análise sofisticadas. Recursos de computação dentro da memória do Spark tornam uma boa opção para algoritmos iterativos no machine learning e para computações de gráfico. O Spark também é compatível com o armazenamento de Blobs do Azure (WASB), para que os dados existentes armazenados no Azure podem ser facilmente processados com o Spark.

Quando cria um cluster do Spark no HDInsight, está a criar recursos de computação do Azure com o Spark instalado e configurado. Demora cerca de 10 minutos para criar um cluster do Spark no HDInsight. Store os dados a ser processados no armazenamento de Blobs do Azure. Para obter informações sobre como utilizar o armazenamento de Blobs do Azure com um cluster, consulte [utilização compatível com HDFS Blob storage do Azure com o Hadoop no HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

Equipe do TDSP da Microsoft publicou dois orientações passo a passo-a-ponto sobre como utilizar os Clusters do HDInsight Spark para Azure para criar soluções de ciência de dados, um que utilize Python e a outro Scala. Para obter mais informações sobre o Azure HDInsight **Clusters do Spark**, consulte [descrição geral: Apache Spark no HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Para saber como criar uma ciência de dados solução usando **Python** num Cluster do Spark HDInsight do Azure, veja [descrição geral de ciência de dados com o Spark no Azure HDInsight](spark-overview.md). Para saber como criar uma ciência de dados solução usando **Scala** num Cluster do Spark HDInsight do Azure, veja [ciência de dados com o Scala e Spark no Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

O Azure SQL Data Warehouse permite-lhe dimensionar recursos de computação facilmente e, em segundos, sem sobreaprovisionar ou pagar demasiado. Ele também oferece a opção exclusiva de colocar em pausa a utilização de recursos de computação, o que lhe dá a liberdade para melhor gerir os seus custos na cloud. A capacidade de implementar recursos de computação escalável torna possível reunir todos os dados para o Azure SQL Data Warehouse. Os custos de armazenamento são mínimos, e pode executar a computação apenas nas partes de conjuntos de dados que pretende analisar. 

Para obter mais informações sobre o Azure SQL Data Warehouse, consulte a [o SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) Web site. Para saber como criar soluções de análise avançada de ponto a ponto com o SQL Data Warehouse, veja [o processo de ciência de dados de equipa em ação: utilizar o SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure data lake é-se como um repositório de toda a empresa de todos os tipos de dados recolhidos numa única localização, antes de quaisquer requisitos formais ou um esquema que está a ser impostas. Essa flexibilidade permite que todos os tipos de dados ser mantidos num data lake, independentemente do porte e estrutura ou da rapidez com que é ingerido. As organizações podem, em seguida, utilizar o Hadoop ou análise avançada para encontrar padrões nestes dados lakes. Lakes de dados também podem servir como um repositório para preparação de dados de custo mais baixo e antes de moderação os dados e movê-lo para um armazém de dados.

Para obter mais informações sobre o Azure Data Lake, veja [apresentando o Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Para saber como criar uma solução de ciência de dados ponto a ponto escaláveis com o Azure Data Lake, veja [ciência de dados dimensionável no Azure Data Lake: Uma passo a passo-a-ponto](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Clusters do Azure Hive do HDInsight (Hadoop)

Apache Hive é um sistema de armazém de dados para o Hadoop, o que permite o resumo de dados, consulta e a análise de dados usando o HiveQL, uma linguagem de consulta semelhante ao SQL. Hive pode ser utilizado para explorar interativamente os seus dados ou para criar tarefas de processamento de batch reutilizável.

Hive permite-lhe a estrutura do projeto nos dados em grande parte não estruturados. Depois de definir a estrutura, pode utilizar o Hive para consultar os dados num cluster do Hadoop sem ter de utilizar, ou até mesmo saber, Java ou do MapReduce. HiveQL (a linguagem de consulta do Hive) permite-lhe escrever consultas com as instruções que são semelhantes ao T-SQL.

Para os cientistas de dados, Hive pode executar funções Python User-Defined (UDFs) em consultas do Hive para processar registros. Esta capacidade expande consideravelmente a capacidade de consultas do Hive em análise de dados. Especificamente, ele permite que os cientistas de dados realizar a engenharia de funcionalidades dimensionável em idiomas principalmente estão familiarizados com: o HiveQL de tipo SQL e Python. 

Para obter mais informações sobre Clusters de ramo de registo do Azure HDInsight, consulte [utilizar o Hive e o HiveQL com o Hadoop no HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Para saber como criar uma solução de ciência de dados ponto a ponto escaláveis com Clusters do Hive do HDInsight do Azure, veja [o processo de ciência de dados de equipa em ação: com clusters do HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Armazenamento de Ficheiros do Azure 

Armazenamento de ficheiros do Azure é um serviço que oferece partilhas de ficheiros na cloud utilizando o protocolo de bloco de mensagem de servidor (SMB) padrão. O SMB 2.1 e o SMB 3.0 são suportados. Com o Armazenamento de ficheiros do Azure, pode migrar aplicações antigas que se baseiam em partilhas de ficheiros para o Azure rapidamente e sem reescritas dispendiosas. As aplicações em execução em máquinas virtuais ou serviços em nuvem do Azure ou em clientes no local podem montar uma partilha de ficheiros na nuvem, tal como uma aplicação de ambiente de trabalho monta uma partilha SMB típica. Em seguida, quaisquer componentes da aplicação podem montar e aceder à partilha do File Storage em simultâneo.

Especialmente útil para projetos de ciência de dados é a capacidade de criar um armazenamento de ficheiros do Azure como o local para partilhar dados de projeto com os membros da equipe de projeto. Em seguida, cada um deles tem acesso para a mesma cópia dos dados no armazenamento de ficheiros do Azure. Também podem utilizar o armazenamento de ficheiros para partilhar os conjuntos de recursos gerados durante a execução do projeto. Se o projeto é um compromisso de cliente, os seus clientes podem criar um armazenamento de ficheiros do Azure na sua própria subscrição do Azure para partilhar os dados do projeto e recursos com o utilizador. Dessa forma, o cliente tem controlo total sobre os recursos de dados do projeto. Para obter mais informações sobre o armazenamento de ficheiros do Azure, consulte [introdução ao armazenamento de ficheiros do Azure no Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) e [como utilizar o File Storage do Azure com o Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>O SQL Server 2016 R Services

R Services (In-database) fornecem uma plataforma para desenvolver e implementar aplicações inteligentes que podem descobrir novas informações. Pode utilizar a linguagem de R diversificada e poderosa, incluindo muitos pacotes fornecidos pela Comunidade do R, para criar modelos e gerar predições a partir dos seus dados do SQL Server. Uma vez que a linguagem R de integrar o R Services (In-database) com o SQL Server, análise é mantida próximas dos dados, o que elimina os custos e riscos de segurança associados à mudança de dados.

R Services (In-database) suporta a linguagem R de código aberto com um conjunto abrangente de ferramentas do SQL Server e tecnologias. Eles oferecem desempenho superior, segurança, confiabilidade e capacidade de gestão. Pode implementar soluções de R com ferramentas convenientes e familiares. As aplicações de produção podem chamar o tempo de execução do R e obter previsões e os elementos visuais com o Transact-SQL. Também é usar as bibliotecas de ScaleR para melhorar o dimensionamento e desempenho das suas soluções de R. Para obter mais informações, consulte [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

A equipe do TDSP da Microsoft publicou dois ponto-a-ponto orientações passo a passo que mostram como criar soluções de ciência de dados do SQL Server 2016 R Services: um para programadores de R e outro para os desenvolvedores de SQL. Para **programadores de R**, consulte [ciência de dados ponto-a-ponto instruções](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Para **programadores de SQL**, consulte [na base de dados Advanced Analytics para programadores de SQL (Tutorial)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix"></a>Apêndice: Ferramentas para configurar a projetos de ciência de dados

### <a name="install-git-credential-manager-on-windows"></a>Instalar Gestor de credenciais do Git no Windows

Se estiver a seguir o TDSP **Windows**, tem de instalar o **Git Credential Manager (GCM)** para comunicar com os repositórios de Git. Para instalar o GCM, primeiro tem de instalar **Chocolaty**. Para instalar Chocolaty e o GCM, execute os seguintes comandos no Windows PowerShell como um **administrador**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Instalar o Git em máquinas do Linux (CentOS)

Execute o seguinte comando do bash para instalar o Git em máquinas do Linux (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Gerar a chave SSH pública em máquinas do Linux (CentOS)

Se estiver a utilizar máquinas do Linux (CentOS) para executar os comandos do git, terá de adicionar a chave pública de SSH do seu computador para seus serviços de DevOps do Azure, para que esta máquina é reconhecida pelos serviços de DevOps do Azure. Em primeiro lugar, terá de gerar uma chave SSH pública e adicione a chave para as chaves públicas SSH na sua página de definições de segurança de serviços de DevOps do Azure. 

- Para gerar a chave SSH, execute os dois comandos seguintes: 

        ssh-keygen
        cat .ssh/id_rsa.pub

![Comandos para gerar a chave SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Copie todo o ssh incluindo chave *ssh-rsa*. 
- Inicie sessão para os serviços de DevOps do Azure. 
- Clique em **< nome da\>**  no canto superior direito da página e clique em **segurança**. 
    
    ![Clique no seu nome e, em seguida, clique em segurança](./media/platforms-and-tools/resources-2-user-setting.png)

- Clique em **chaves públicas SSH**e clique em **+ adicionar**. 

    ![Clique em chaves públicas SSH e, em seguida, clique em + Adicionar](./media/platforms-and-tools/resources-3-add-ssh.png)

- Colar o ssh key acabou de ser copiada na caixa de texto e guarde.


## <a name="next-steps"></a>Passos Seguintes

Completa de instruções passo a passo de ponto-a-ponto, que demonstram todas as etapas do processo para **cenários específicos** também são fornecidas. Se estão listadas e estão associados ao descrições em miniatura da [instruções passo a passo do exemplo](walkthroughs.md) tópico. Eles ilustram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

Para obter exemplos executar os passos no processo de ciência de dados de equipa que utilizam o Azure Machine Learning Studio, consulte a [com o Azure ML](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) percurso de aprendizagem.
