---
title: Plataformas de dados suportadas
titleSuffix: Azure Data Science Virtual Machine
description: Conheça as plataformas e ferramentas de dados suportadas para a Máquina Virtual de Ciência de Dados Azure.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bfae8147c348c76fa0e406fec283144ebc26e86b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390680"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Plataformas de dados suportadas na máquina de Virtual de ciência de dados

Com uma Máquina Virtual de Ciência de Dados (DSVM), pode construir a sua análise contra uma vasta gama de plataformas de dados. Para além das interfaces para plataformas de dados remoto, a DSVM fornece uma instância local para um rápido desenvolvimento e criação de protótipos.

As seguintes ferramentas de plataforma de dados são suportadas no DSVM.

## <a name="sql-server-developer-edition"></a>Edição de desenvolvedor de servidor SQL

| | |
| ------------- | ------------- |
| O que é?   | Uma instância de base de dados relacionais locais      |
| Edições DSVM suportadas      | Windows: SQL Server 2017, Windows 2019 (pré-visualização) : SQL Server 2019      |
| Utilizações típicas      | Desenvolvimento rápido localmente com o conjunto de dados mais pequeno <br/> Execute R na base de dados   |
| Links para amostras      |    Uma pequena amostra de um conjunto de dados da cidade de Nova Iorque é carregada na base de dados SQL:<br/>  `nyctaxi` <br/> A amostra de jupyter que mostra o Microsoft Machine Learning Server e a análise da base de dados pode ser encontrada em:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Ferramentas relacionadas no DSVM       | SQL Server Management Studio <br/> Condutores da ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> A SQL Server Developer Edition só pode ser utilizada para fins de desenvolvimento e teste. Precisa de uma licença ou uma das VMs do SQL Server para executá-lo em produção.


### <a name="setup"></a>Configurar

O servidor de base de dados já está pré-configurado e os serviços Windows relacionados com o SQL Server (como `SQL Server (MSSQLSERVER)`) estão definidos para funcionar automaticamente. O único passo manual consiste em permitir a análise da base de dados utilizando o Microsoft Machine Learning Server. Pode ativar a análise executando o seguinte comando como uma ação única no Estúdio de Gestão de Servidores SQL (SSMS). Execute este comando depois de iniciar sessão como administrador da máquina, abra uma nova consulta no SSMS e certifique-se de que a base de dados selecionada é `master`:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Para executar o Estúdio de Gestão de Servidores SQL, pode pesquisar o "SQL Server Management Studio" na lista de programas ou utilizar o Windows Search para o encontrar e executar. Quando solicitado para obter credenciais, selecione **A autenticação do Windows** e utilize o nome da máquina ou ```localhost``` no campo Nome do Servidor **SQL.**

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo

Por predefinição, o servidor de base de dados com a instância de base de dados predefinida é executado automaticamente. Pode usar ferramentas como o SQL Server Management Studio na VM para acessar o banco de dados do SQL Server localmente. As contas do administrador local têm acesso à administração na base de dados.

Além disso, o DSVM vem com os condutores da ODBC e JDBC para falar com o SQL Server, as bases de dados Azure SQL e o Azure SQL Data Warehouse de aplicações escritas em vários idiomas, incluindo Python e Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Como é configurado e instalado no DSVM? 

 O Servidor SQL está instalado da forma padrão. Pode ser encontrado em `C:\Program Files\Microsoft SQL Server`. A instância do Servidor de Aprendizagem automática in-base de dados encontra-se em `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. O DSVM também tem uma instância separada autónoma do Servidor de Aprendizagem automática, que é instalada em `C:\Program Files\Microsoft\R Server\R_SERVER`. Estas duas instâncias do Servidor de Aprendizagem automática não partilham bibliotecas.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autónomo)

| | |
| ------------- | ------------- |
| O que é?   | Um exemplo autónomo (nó único em processo) da popular plataforma Apache Spark; um sistema de processamento rápido e em larga escala de dados e aprendizagem automática     |
| Edições DSVM suportadas      | Linux     |
| Utilizações típicas      | * Desenvolvimento rápido de aplicações Spark/PySpark localmente com um conjunto de dados menor e posterior implantação em grandes clusters de faíscas, como O Azure HDInsight<br/> * Teste o contexto do servidor de aprendizagem automática da Microsoft <br />* Utilize a sparkml ou a biblioteca [MMLSpark](https://github.com/Azure/mmlspark) de código aberto da Microsoft para construir aplicações ML |
| Links para amostras      |    Exemplo de Jupyter: <br />&nbsp;&nbsp;* ~/cadernos/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (contexto spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Ferramentas relacionadas no DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark Kernels)<br/>Microsoft Machine Learning Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Como a utilizar
Pode submeter os trabalhos spark na linha de comando executando o comando `spark-submit` ou `pyspark`. Também pode criar um bloco de notas do Jupyter através da criação de um novo bloco de notas com o kernel do Spark.

Pode utilizar o Spark from R utilizando bibliotecas como sparkr, Sparklyr e Microsoft Machine Learning Server, que estão disponíveis no DSVM. Consulte os ponteiros de exemplos na tabela anterior.

### <a name="setup"></a>Configurar
Antes de correr num contexto spark no Microsoft Machine Learning Server na edição Ubuntu Linux DSVM, você deve completar um passo de configuração único para permitir um nó único local Hadoop HDFS e yarn instância. Por predefinição, os serviços do Hadoop são instalados mas desativados na DSVM. Para os ativar, execute os seguintes comandos como raiz da primeira vez:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Pode parar os serviços relacionados com Hadoop quando já não precisar deles, executando ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```.

Uma amostra que demonstre como desenvolver e testar a MRS num contexto remoto de Spark (que é a instância autónoma de Spark no DSVM) é fornecida e disponível no diretório `/dsvm/samples/MRS`.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Como é configurado e instalado no DSVM? 
|Plataforma|Localização ($SPARK_HOME) de instalação|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


As bibliotecas para aceder a dados do armazenamento do Azure Blob ou do Armazenamento de Lagos De Dados Azure, utilizando as bibliotecas de aprendizagem automática Microsoft MMLSpark, estão pré-instaladas em $SPARK_HOME/frascos. Estes JARs são carregados automaticamente quando Spark é iniciado. Por padrão, a Spark utiliza dados no disco local. 

Para que a instância De faísca no DSVM aceda aos dados armazenados no armazenamento blob ou no Armazenamento do Lago De Dados Azure, deve criar e configurar o ficheiro `core-site.xml` com base no modelo encontrado no modelo $SPARK_HOME/conf/core-site.xml. Deve também ter as credenciais adequadas para aceder ao armazenamento blob e ao armazenamento do Lago Azure Data. (Note que os ficheiros do modelo utilizam espaços reservados para armazenamento blob e configurações de armazenamento de lagos de dados azure.)

Para obter informações mais detalhadas sobre a criação de credenciais de serviço de armazenamento de lagos de dados Azure, consulte autenticação com O Armazenamento de [Lagos Azure Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Após as credenciais para armazenamento blob ou armazenamento de lago de dados Azure são introduzidos no ficheiro core-site.xml, pode fazer referência aos dados armazenados nessas fontes através do prefixo URI de wasb:// ou adl://.

