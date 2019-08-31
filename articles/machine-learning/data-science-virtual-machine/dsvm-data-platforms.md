---
title: Plataformas de dados com suporte
titleSuffix: Azure Data Science Virtual Machine
description: Saiba mais sobre as plataformas de dados e as ferramentas com suporte para o Máquina Virtual de Ciência de Dados do Azure.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: e0ba013f7739e7ade717136cb5507af630263a18
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192204"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Plataformas de dados suportadas na máquina de Virtual de ciência de dados

A Máquina Virtual de ciência de dados (DSVM) permite-lhe criar a sua análise contra uma ampla variedade de plataformas de dados. Para além das interfaces para plataformas de dados remoto, a DSVM fornece uma instância local para um rápido desenvolvimento e criação de protótipos. 

Seguem-se as ferramentas de plataforma de dados suportadas no DSVM. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| O que é?   | Uma instância de base de dados relacionais locais      |
| Edições DSVM suportados      | Windows      |
| Utilizações típicas      | Desenvolvimento rápido localmente com o conjunto de dados mais pequeno <br/> Execute R na base de dados   |
| Links para amostras      |    Uma pequena amostra do conjunto de dados da cidade de nova York é carregada para a base de dados SQL `nyctaxi`. <br/> Exemplo do Jupyter que mostra o Microsoft R e análise na base de dados pode ser encontrado em:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Ferramentas relacionadas na DSVM       | SQL Server Management Studio <br/> ODBC/JDBC Drivers<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> O SQL Server 2016 developer edition só pode ser utilizado para fins de teste e de desenvolvimento. Precisa de uma licença ou uma das VMs do SQL Server para executá-lo em produção. 


### <a name="setup"></a>Configurar

O servidor de base de dados já está pré-configurado e os serviços do Windows relacionados ao SQL Server (como `SQL Server (MSSQLSERVER)`) são definidos para executar automaticamente. A etapa manual apenas para ser executado é ativar a análise de na base de dados com o Microsoft R. Pode fazê-lo ao executar o seguinte comando, como um tempo de uma ação no SQL Server Management Studio (SSMS) depois de iniciar sessão em como o administrador da máquina virtual, abra uma "nova consulta" no SSMS, certifique-se de que a base de dados selecionada `master` e, em seguida, execute: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Para executar o SQL Server Management Studio, pode procurar "SQL Server Management Studio" na lista de programa ou utilizar o Windows Search para localizar e executá-lo. Quando lhe forem pedidas credenciais, escolha "Autenticação do Windows" e utilizar o nome da máquina ou ```localhost``` no nome do SQL Server. 

### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?  

O servidor de base de dados com a instância de base de dados predefinida está em execução automaticamente por predefinição. Pode usar ferramentas como o SQL Server Management Studio na VM para acessar o banco de dados do SQL Server localmente. Conta de administradores local tem acesso de administrador na base de dados. 

Também a DSVM vem com controladores de ODBC Drivers e JDBC para comunicar com o SQL Server, bases de dados SQL do Azure e o Azure SQL Data Warehouse de aplicativos escritos em várias linguagens, incluindo o Python, R. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Como é ele configurado / instalado no DSVM? 

O SQL Server está instalado da forma padrão. Podem ser encontrados em `C:\Program Files\Microsoft SQL Server`. A instância de base de dados em R foi encontrada em `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. A DSVM também tem uma instância de R Server autônomo separado instalada em `C:\Program Files\Microsoft\R Server\R_SERVER`. Estas instâncias de dois R não partilham as bibliotecas.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autónomo)

| | |
| ------------- | ------------- |
| O que é?   | Uma instância autónoma (único nó dentro do processo) da popular plataforma de Apache Spark, um sistema para processamento de dados rapidamente em grande escala e machine learning     |
| Edições DSVM suportados      | Linux <br /> (Experimental) do Windows      |
| Utilizações típicas      | * Um rápido desenvolvimento de aplicações do Spark/PySpark localmente com o conjunto de dados menor e mais tarde implementá-la em grandes clusters do Spark, como o Azure HDInsight<br/> * Testar o contexto de Spark do Microsoft R Server <br />* Utilizar SparkML ou da Microsoft open source [MMLSpark](https://github.com/Azure/mmlspark) biblioteca para criar aplicativos de ML  |
| Links para amostras      |    Exemplo de Jupyter: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (contexto de Spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Ferramentas relacionadas na DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark Kernels)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Como usá-lo
Pode executar o Spark, submeter tarefas do Spark na linha de comandos com `spark-submit` ou `pyspark` comandos. Também pode criar um bloco de notas do Jupyter através da criação de um novo bloco de notas com o kernel do Spark. 

Pode utilizar o Spark no R usando bibliotecas como SparkR, Sparklyr ou o Microsoft R Server que estão disponíveis na DSVM. Consulte os ponteiros de exemplos na tabela anterior. 

> [!NOTE]
> Executar o Microsoft R Server no contexto de Spark da DSVM só é suportada na edição DSVM do Ubuntu Linux. 



### <a name="setup"></a>Configurar
Antes de executar no contexto do Spark no Microsoft R Server na edição de DSVM do Linux Ubuntu, terá de realizar uma uma vez que o passo de configuração para ativar um único nó HDFS do Hadoop e Yarn a instância local. Por predefinição, os serviços do Hadoop são instalados mas desativados na DSVM. Para ativá-lo, terá de executar os seguintes comandos como raiz na primeira vez:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

É possível parar o Hadoop relacionados com serviços quando não precisar delas, executando ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` um exemplo que demonstra como desenvolver e testar MRS no contexto de Spark remoto (que é a instância de Spark autónoma no DSVM) é fornecido e está disponível no `/dsvm/samples/MRS` diretório. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Como é ele configurado / instalado no DSVM? 
|Plataforma|Localização ($SPARK_HOME) de instalação|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotecas para aceder a dados de Blobs do Azure ou de armazenamento do Azure Data Lake (ADLS) e utilização de bibliotecas de MMLSpark aprendizagem da Microsoft são pré-instalado em $SPARK_HOME/jars. Estes JARs são carregados automaticamente quando Spark é iniciado. Por predefinição o Spark utiliza os dados no disco local. Para que a instância do Spark no DSVM para acessar dados armazenados em BLOBs do Azure ou do ADLS tem de configurar/criar o `core-site.xml` ficheiros com base no modelo encontrado em $SPARK_HOME/conf/core-site.xml.template (onde os marcadores de posição para o Blob e ADLS configurações) com as credenciais corretas para BLOBs do Azure e o armazenamento do Azure Data Lake. Encontrará mais passos sobre como criar as credenciais do serviço ADLS [aqui](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Depois das credenciais de Blobs do Azure ou do ADLS são introduzidas no ficheiro core-site, pode referenciar os dados armazenados nessas fontes com o prefixo URI de wasb: / / ou adl: / /. 

