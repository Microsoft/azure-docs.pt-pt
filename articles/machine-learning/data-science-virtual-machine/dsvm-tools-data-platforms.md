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
ms.openlocfilehash: cd787881957d78f179107e46b2650de4618c7724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282329"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Plataformas de dados suportadas na Máquina Virtual da Ciência dos Dados

Com uma Máquina Virtual de Ciência de Dados (DSVM), pode construir a sua análise contra uma vasta gama de plataformas de dados. Além de interfaces com plataformas de dados remotos, o DSVM fornece uma instância local para desenvolvimento rápido e prototipagem.

As seguintes ferramentas de plataforma de dados são suportadas no DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| O que é?   | Uma instância de base de dados relacional local      |
| Edições DSVM suportadas      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Usos típicos      | Desenvolvimento rápido localmente com conjunto de dados menor <br/> Executar A Base de Dados R   |
| Ligações a amostras      |    Uma pequena amostra de um conjunto de dados da cidade de Nova Iorque é carregada na base de dados SQL:<br/>  `nyctaxi` <br/> A amostra de jupyter que mostra o Microsoft Machine Learning Server e a análise da base de dados pode ser encontrada em:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Ferramentas relacionadas no DSVM       | SQL Server Management Studio <br/> Condutores da ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> A SQL Server Developer Edition só pode ser utilizada para fins de desenvolvimento e teste. Precisa de uma licença ou de um dos VMs do SQL Server para executá-la em produção.


### <a name="setup"></a>Configuração

O servidor de base de dados já está pré-configurado `SQL Server (MSSQLSERVER)`e os serviços Windows relacionados com o SQL Server (como) estão definidos para serem executados automaticamente. O único passo manual consiste em permitir a análise da base de dados utilizando o Microsoft Machine Learning Server. Pode ativar a análise executando o seguinte comando como uma ação única no Estúdio de Gestão de Servidores SQL (SSMS). Execute este comando depois de iniciar sessão como administrador da máquina, abra uma nova `master`consulta no SSMS e certifique-se de que a base de dados selecionada é:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Para executar o Estúdio de Gestão de Servidores SQL, pode pesquisar o "SQL Server Management Studio" na lista de programas ou utilizar o Windows Search para o encontrar e executar. Quando solicitado para obter credenciais, selecione **A autenticação do Windows** e utilize o nome da máquina ou ```localhost``` no campo Nome do Servidor **SQL.**

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo

Por predefinição, o servidor de base de dados com a instância de base de dados predefinida é executado automaticamente. Pode utilizar ferramentas como o SQL Server Management Studio no VM para aceder localmente à base de dados do SQL Server. As contas do administrador local têm acesso à administração na base de dados.

Além disso, o DSVM vem com os condutores da ODBC e JDBC para falar com o SQL Server, as bases de dados Azure SQL e o Azure SQL Data Warehouse de aplicações escritas em vários idiomas, incluindo Python e Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Como é configurado e instalado no DSVM? 

 O Servidor SQL está instalado da forma padrão. Pode ser encontrado `C:\Program Files\Microsoft SQL Server`em . A instância do Servidor de Aprendizagem automática in-base de dados encontra-se em `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. O DSVM também tem uma instância separada autónoma do `C:\Program Files\Microsoft\R Server\R_SERVER`Servidor de Aprendizagem automática, que está instalada em . Estas duas instâncias do Servidor de Aprendizagem automática não partilham bibliotecas.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (Autónomo)

| | |
| ------------- | ------------- |
| O que é?   | Um exemplo autónomo (nó único em processo) da popular plataforma Apache Spark; um sistema de processamento rápido e em larga escala de dados e aprendizagem automática     |
| Edições DSVM suportadas      | Linux     |
| Usos típicos      | * Desenvolvimento rápido de aplicações Spark/PySpark localmente com um conjunto de dados menor e posterior implantação em grandes clusters de faíscas, como O Azure HDInsight<br/> * Teste o contexto do servidor de aprendizagem automática da Microsoft <br />* Utilize a sparkml ou a biblioteca [MMLSpark](https://github.com/Azure/mmlspark) de código aberto da Microsoft para construir aplicações ML |
| Ligações a amostras      |    Amostra de jupyter: <br />&nbsp;&nbsp;~ /cadernos/SparkML/pySpark <br /> &nbsp;&nbsp;~ /cadernos/MMLSpark <br /> Microsoft Machine Learning Server (contexto spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Ferramentas relacionadas no DSVM       | PySpark<br/>Jupyter (Kernels Spark/PySpark)<br/>Microsoft Machine Learning Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Como utilizá-lo
Pode submeter os trabalhos de Spark `spark-submit` `pyspark` na linha de comando, executando o comando ou o comando. Você também pode criar um caderno Jupyter criando um novo caderno com o kernel Spark.

Pode utilizar o Spark from R utilizando bibliotecas como sparkr, Sparklyr e Microsoft Machine Learning Server, que estão disponíveis no DSVM. Consulte os ponteiros para amostras na tabela anterior.

### <a name="setup"></a>Configuração
Antes de correr num contexto spark no Microsoft Machine Learning Server na edição Ubuntu Linux DSVM, você deve completar um passo de configuração único para permitir um nó único local Hadoop HDFS e yarn instância. Por predefinição, os serviços Hadoop são instalados, mas desativados no DSVM. Para os ativar, execute os seguintes comandos como raiz da primeira vez:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Pode parar os serviços relacionados com Hadoop ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```quando já não precisar deles funcionando.

Uma amostra que demonstre como desenvolver e testar mrs num contexto remoto de Faísca (que é a instância `/dsvm/samples/MRS` autónoma de Spark no DSVM) é fornecida e disponível no diretório.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Como é configurado e instalado no DSVM? 
|Plataforma|Local de Instalação ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


As bibliotecas para aceder a dados do armazenamento do Azure Blob ou do Armazenamento de Lagos De Dados Azure, utilizando as bibliotecas de aprendizagem automática Microsoft MMLSpark, estão pré-instaladas em $SPARK_HOME/frascos. Estes JARs são automaticamente carregados quando a Spark começa. Por padrão, a Spark utiliza dados no disco local. 

Para que a instância De faísca no DSVM aceda aos dados armazenados no armazenamento `core-site.xml` blob ou no Armazenamento do Lago De Dados Azure, deve criar e configurar o ficheiro com base no modelo encontrado em $SPARK_HOME/conf/core-site.xml.template. Deve também ter as credenciais adequadas para aceder ao armazenamento blob e ao armazenamento do Lago Azure Data. (Note que os ficheiros do modelo utilizam espaços reservados para armazenamento blob e configurações de armazenamento de lagos de dados azure.)

Para obter informações mais detalhadas sobre a criação de credenciais de serviço de armazenamento de lagos de dados Azure, consulte autenticação com O Armazenamento de [Lagos Azure Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Após as credenciais para armazenamento blob ou armazenamento de lago de dados Azure são introduzidos no ficheiro core-site.xml, pode fazer referência aos dados armazenados nessas fontes através do prefixo URI de wasb:// ou adl://.

