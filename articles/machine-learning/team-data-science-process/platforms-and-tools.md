---
title: Plataformas e ferramentas para projetos de ciência de dados - Processo de Ciência de Dados de Equipas
description: Itemize e discute os dados e recursos de análise disponíveis para as empresas que normalizam o Processo de Ciência de Dados de Equipa.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ba7b8af9b50b9173f5e2040bb8b623eeafdd538
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453848"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Plataformas e ferramentas para projetos de ciência de dados

A Microsoft fornece um espectro completo de recursos de análise para plataformas em nuvem ou no local. Podem ser implementados para tornar a execução dos seus projetos de ciência de dados eficiente e escalável. A orientação para equipas que implementam projetos de ciência de dados de forma rastreável, controlada pela versão e colaborativa é fornecida pelo [Processo de Ciência de Dados de Equipa](overview.md) (TDSP).  Para um esboço das funções de pessoal e das suas tarefas associadas que são tratadas por uma equipa de ciência de dados que normaliza este processo, consulte [as funções e tarefas do Processo de Ciência de Dados de Equipa.](roles-tasks.md)

Os recursos de análise disponíveis para as equipas de ciência de dados que utilizam o TDSP incluem:

- Máquinas Virtuais de Ciência de Dados (Windows e Linux CentOS)
- HDInsight Spark Clusters
- Azure Synapse Analytics
- Azure Data Lake
- HDInsight Hive Clusters
- Armazenamento de Ficheiros do Azure
- SqL Server 2019 R e Python Services
- Azure Databricks

Neste documento, descrevemos brevemente os recursos e fornecemos links para os tutoriais e caminhadas que as equipas da TDSP publicaram. Eles podem ajudá-lo a aprender a usá-los passo a passo e começar a usá-los para construir suas aplicações inteligentes. Mais informações sobre estes recursos estão disponíveis nas suas páginas de produtos. 

## <a name="data-science-virtual-machine-dsvm"></a>Máquina virtual de ciência de dados (DSVM)

A máquina virtual da ciência dos dados oferecida tanto no Windows como no Linux pela Microsoft, contém ferramentas populares para atividades de modelação e desenvolvimento de ciência de dados. Inclui ferramentas como:

- Microsoft R Server Developer Edition 
- Distribuição de Anaconda Python
- Cadernos jupyter para Python e R 
- Edição Comunitária de Estúdio Visual com Ferramentas Python e R no Windows / Eclipse no Linux
- Power BI desktop para Windows
- SQL Server 2016 Developer Edition no Windows / Postgres no Linux

Também inclui **ferramentas ML e IA** como xgboost, mxnet e Vowpal Wabbit.

Atualmente, o DSVM está disponível nos sistemas operativos **Windows** e **Linux CentOS.** Escolha o tamanho do seu DSVM (número de núcleos de CPU e a quantidade de memória) com base nas necessidades dos projetos de ciência de dados que planeia executar nele. 

Para obter mais informações sobre a edição do Windows do DSVM, consulte a [Máquina Virtual da Ciência dos Dados](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) do Microsoft no Mercado Azure. Para a edição Linux do DSVM, consulte [a Máquina Virtual linux Data Science.](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)

Para aprender a executar algumas das tarefas comuns de ciência de dados no DSVM de forma eficiente, veja [10 coisas que pode fazer na Máquina Virtual da Ciência de Dados](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Aglomerados de faíscas Azure HDInsight

O Apache Spark é uma arquitetura de processamento paralelo open source que suporta o processamento dentro da memória para melhorar o desempenho de aplicações de análise de macrodados. O motor de processamento de faíscas é construído para velocidade, facilidade de utilização e análise sofisticada. As capacidades de computação em memória da Spark fazem com que seja uma boa escolha para algoritmos iterativos na aprendizagem automática e para computações de gráficos. A faísca também é compatível com o armazenamento Azure Blob (WASB), pelo que os seus dados existentes armazenados em Azure podem ser facilmente processados usando o Spark.

Quando cria um cluster do Spark no HDInsight, está a criar recursos de computação do Azure com o Spark instalado e configurado. Leva cerca de 10 minutos para criar um cluster Spark em HDInsight. Guarde os dados a serem processados no armazenamento da Azure Blob. Para obter informações sobre a utilização do Azure Blob Storage com um cluster, consulte [use o armazenamento Azure Blob compatível com HDFS com Hadoop em HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

A equipa da TDSP da Microsoft publicou duas pesquisas de ponta a ponta sobre como usar clusters de faíscas Azure HDInsight para construir soluções de ciência de dados, uma usando Python e a outra Scala. Para obter mais informações sobre Azure HDInsight **Spark Clusters**, consulte [visão geral: Apache Spark em HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Para aprender a construir uma solução de ciência de dados usando **Python** em um Azure HDInsight Spark Cluster, consulte [a visão geral da Ciência dos Dados usando Spark on Azure HDInsight](spark-overview.md). Para aprender a construir uma solução de ciência de dados usando **Scala** num Cluster de Faíscas Azure HDInsight, consulte [Data Science usando Scala e Spark on Azure](scala-walkthrough.md). 


##  <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

O Azure Synapse Analytics permite-lhe escalar os recursos de computação de forma fácil e em segundos, sem sobre-provisões ou sobre-pagamentos. Também oferece a opção única de interromper o uso de recursos computativos, dando-lhe a liberdade de gerir melhor os seus custos em nuvem. A capacidade de implantar recursos de computação escalável permite trazer todos os seus dados para a Azure Synapse Analytics. Os custos de armazenamento são mínimos e só pode executar o cálculo nas partes dos conjuntos de dados que pretende analisar. 

Para mais informações sobre o Azure Synapse Analytics, consulte o site [Azure Synapse Analytics.](https://azure.microsoft.com/services/sql-data-warehouse) Para aprender a construir soluções de análise avançadas de ponta a ponta com a Azure Synapse Analytics, consulte [o Processo de Ciência de Dados da Equipa em ação: utilizando a Azure Synapse Analytics](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

O Azure Data Lake é um repositório em toda a empresa de todos os tipos de dados recolhidos num único local, antes de quaisquer requisitos formais, ou esquema ser imposto. Esta flexibilidade permite que todo o tipo de dados sejam guardados num lago de dados, independentemente do seu tamanho ou estrutura ou da rapidez com que são ingeridos. As organizações podem então usar Hadoop ou análises avançadas para encontrar padrões nestes lagos de dados. Os lagos de dados também podem servir de repositório para a preparação de dados de baixo custo antes de curar os dados e movê-lo para um armazém de dados.

Para obter mais informações sobre o Lago de Dados Azure, consulte [a introdução do Lago de Dados Azure.](https://azure.microsoft.com/blog/introducing-azure-data-lake/) Para aprender a construir uma solução de ciência de dados escalável de ponta a ponta com o Lago de Dados Azure, consulte [a Scalable Data Science in Azure Data Lake: Uma Walkthrough de ponta a ponta](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Aglomerados de Colmeia Azure HDInsight (Hadoop)

Apache Hive é um sistema de armazém de dados para Hadoop, que permite a resumo de dados, consulta e a análise de dados usando o HiveQL, uma linguagem de consulta semelhante ao SQL. A Colmeia pode ser usada para explorar interativamente os seus dados ou para criar trabalhos de processamento de lotes reutilizáveis.

A Colmeia permite-lhe projetar estrutura em dados em grande parte não estruturados. Depois de definir a estrutura, pode usar a Hive para consultar esses dados num cluster Hadoop sem ter de usar, ou mesmo saber, Java ou MapReduce. O HiveQL (a linguagem de consulta de Colmeia) permite-lhe escrever consultas com declarações semelhantes a T-SQL.

Para os cientistas de dados, a Hive pode executar As Funções User-Defined Python (UDFs) em consultas de Colmeia para processar registos. Esta capacidade alarga consideravelmente a capacidade das consultas de Hive na análise de dados. Especificamente, permite que os cientistas de dados conduzam engenharia de recursos escaláveis em línguas que estão mais familiarizadas: o HIVEQL e o Python semelhantes ao SQL. 

Para obter mais informações sobre clusters de colmeia Azure HDInsight, consulte [Use Hive e HiveQL com Hadoop em HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Para aprender a construir uma solução de ciência de dados escalável de ponta a ponta com clusters de colmeia azure HDInsight, consulte [o Processo de Ciência de Dados da Equipa em ação: utilizando clusters HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Armazenamento de Ficheiros do Azure 

O Azure File Storage é um serviço que oferece partilhas de ficheiros na nuvem utilizando o protocolo padrão do Bloco de Mensagens do Servidor (SMB). O SMB 2.1 e o SMB 3.0 são suportados. Com o Armazenamento de ficheiros do Azure, pode migrar aplicações antigas que se baseiam em partilhas de ficheiros para o Azure rapidamente e sem reescritas dispendiosas. As aplicações em execução em máquinas virtuais ou serviços em nuvem do Azure ou em clientes no local podem montar uma partilha de ficheiros na nuvem, tal como uma aplicação de ambiente de trabalho monta uma partilha SMB típica. Em seguida, quaisquer componentes da aplicação podem montar e aceder à partilha do Armazenamento de ficheiros em simultâneo.

Especialmente útil para projetos de ciência de dados é a capacidade de criar uma loja de ficheiros Azure como o local para partilhar dados do projeto com os membros da sua equipa de projeto. Cada um deles tem então acesso à mesma cópia dos dados no armazenamento de ficheiros Azure. Também podem utilizar este armazenamento de ficheiros para partilhar conjuntos de funcionalidades gerados durante a execução do projeto. Se o projeto for um compromisso com o cliente, os seus clientes podem criar um armazenamento de ficheiros Azure sob a sua própria assinatura Azure para partilhar os dados e funcionalidades do projeto consigo. Desta forma, o cliente tem o controlo total dos ativos de dados do projeto. Para obter mais informações sobre o armazenamento de ficheiros Azure, consulte [Começar com o armazenamento de ficheiros Azure no Windows](../../storage/files/storage-dotnet-how-to-use-files.md) e como utilizar o Armazenamento de [Ficheiros Azure com o Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>SqL Server 2019 R e Python Services

Os serviços R (Base de dados) fornecem uma plataforma para desenvolver e implementar aplicações inteligentes que podem descobrir novos conhecimentos. Você pode usar a linguagem R rica e poderosa, incluindo os muitos pacotes fornecidos pela comunidade R, para criar modelos e gerar previsões a partir dos seus dados do SQL Server. Como os Serviços R (Base de dados) integram o idioma R com o SQL Server, as análises são mantidas perto dos dados, o que elimina os custos e riscos de segurança associados aos dados em movimento.

Os serviços R (base de dados in- database) suportam a linguagem R de código aberto com um conjunto abrangente de ferramentas e tecnologias do SQL Server. Oferecem desempenho superior, segurança, fiabilidade e gestão. Pode implementar soluções R utilizando ferramentas convenientes e familiares. As suas aplicações de produção podem ligar para o tempo de execução R e recuperar previsões e visuais utilizando o Transact-SQL. Também utiliza as bibliotecas ScaleR para melhorar a escala e o desempenho das suas soluções R. Para mais informações, consulte [os Serviços R do SQL Server](/sql/advanced-analytics/r/sql-server-r-services).

A equipa da TDSP da Microsoft publicou duas pesquisas de ponta a ponta que mostram como construir soluções de ciência de dados nos Serviços R do SQL Server 2016: uma para programadores R e outra para desenvolvedores DE SQL. Para **programadores R**, consulte [data science end-to-end walkthrough](/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Para **desenvolvedores SQL**, consulte [In-Database Advanced Analytics for SQL Developers (Tutorial)](/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Apêndice: Ferramentas para criar projetos de ciência de dados

### <a name="install-git-credential-manager-on-windows"></a>Instalar Git Credencial Manager no Windows

Se estiver a seguir o TDSP no **Windows,** tem de instalar o **Git Credential Manager (GCM)** para comunicar com os repositórios git. Para instalar o GCM, primeiro tem de instalar **o Chocolaty.** Para instalar o Chocolaty e o GCM, execute os seguintes comandos no Windows PowerShell como **administrador:**  

```powershell
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
choco install git-credential-manager-for-windows -y
```  

### <a name="install-git-on-linux-centos-machines"></a>Instalar Git em máquinas Linux (CentOS)

Executar o seguinte comando de bash para instalar Git em máquinas Linux (CentOS):

```powershell
sudo yum install git
```

### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Gerar chave SSH pública em máquinas Linux (CentOS)

Se estiver a utilizar máquinas Linux (CentOS) para executar os comandos git, tem de adicionar a chave SSH pública da sua máquina aos seus Serviços Azure DevOps, para que esta máquina seja reconhecida pelos Serviços Azure DevOps. Em primeiro lugar, precisa de gerar uma chave SSH pública e adicionar a chave às chaves públicas SSH na sua página de definição de segurança dos Serviços Azure DevOps. 

1. Para gerar a tecla SSH, executar os dois comandos seguintes: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Comandos para gerar a chave SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Copie toda a chave ssh, incluindo *ssh-rsa*. 
1. Inicie sessão nos seus Serviços Azure DevOps. 
1. Clique **<O Seu \> Nome** no canto superior direito da página e clique em **segurança**. 
    
   ![Clique no seu nome e, em seguida, clique em segurança](./media/platforms-and-tools/resources-2-user-setting.png)

1. Clique em **teclas públicas SSH** e clique **em +Adicionar**. 

   ![Clique em teclas públicas SSH e, em seguida, clique em +Adicionar](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Cole a chave ssh copiada na caixa de texto e guarde.


## <a name="next-steps"></a>Passos seguintes

São também fornecidas caminhadas completas de ponta a ponta que demonstram todos os passos no processo para **cenários específicos.** Estão listados e ligados com descrições de miniaturas no tópico [Exemplo.](walkthroughs.md) Ilustram como combinar ferramentas e serviços de nuvem, no local, num fluxo de trabalho ou oleoduto para criar uma aplicação inteligente. 

Por exemplo, que mostram como executar passos no Processo de Ciência de Dados de Equipa utilizando o Azure Machine Learning Studio (clássico), consulte o caminho de aprendizagem [with Azure ML.](./index.yml)