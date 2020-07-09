---
title: Plataformas de dados apoiadas
titleSuffix: Azure Data Science Virtual Machine
description: Conheça as plataformas e ferramentas de dados suportadas para a Máquina Virtual Azure Data Science.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: ccb95064f756ef035b7da92d029680f1c195982b
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958740"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Plataformas de dados suportadas na Máquina Virtual da Ciência de Dados

Com uma Máquina Virtual de Ciência de Dados (DSVM), pode construir a sua análise contra uma vasta gama de plataformas de dados. Além de interfaces para plataformas de dados remotas, o DSVM fornece uma instância local para desenvolvimento rápido e prototipagem.

As seguintes ferramentas da plataforma de dados são suportadas no DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| O que é?   | Uma instância de base de dados relacional local      |
| Edições DSVM suportadas      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Usos típicos      | Desenvolvimento rápido localmente com conjunto de dados mais pequeno <br/> Executar in-database R   |
| Ligações a amostras      |    Uma pequena amostra de um conjunto de dados da cidade de Nova Iorque é carregada na base de dados SQL:<br/>  `nyctaxi` <br/> A amostra do Jupyter que mostra o Microsoft Machine Learning Server e a análise in-database pode ser encontrada em:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Ferramentas relacionadas no DSVM       | SQL Server Management Studio <br/> Condutores ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> A SQL Server Developer Edition só pode ser utilizada para fins de desenvolvimento e teste. Precisa de uma licença ou de um dos VMs do SQL Server para executá-la em produção.


### <a name="setup"></a>Configuração

O servidor de base de dados já está pré-configurado e os serviços do Windows relacionados com o SQL Server `SQL Server (MSSQLSERVER)` (como) estão definidos para funcionar automaticamente. O único passo manual consiste em permitir a análise in-database utilizando o Microsoft Machine Learning Server. Pode ativar a análise executando o seguinte comando como uma ação única no SQL Server Management Studio (SSMS). Executar este comando depois de iniciar sessão como administrador da máquina, abrir uma nova consulta em SSMS e certificar-se de que a base de dados selecionada `master` é:

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

(Substitua %COMPUTERNAME% pelo seu nome VM.)

Para executar o SQL Server Management Studio, pode procurar "SQL Server Management Studio" na lista de programas ou utilizar o Windows Search para o encontrar e executar. Quando solicitado para obter credenciais, selecione **a autenticação** do Windows e utilize o nome da máquina ou ```localhost``` no campo **SQL Server Name.**

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo

Por predefinição, o servidor de base de dados com a instância de base de dados padrão é executado automaticamente. Pode utilizar ferramentas como o SQL Server Management Studio no VM para aceder localmente à base de dados do SQL Server. As contas dos administradores locais têm acesso administrativo na base de dados.

Além disso, o DSVM vem com controladores ODBC e JDBC para falar com sql Server, bases de dados Azure SQL e Azure SQL Data Warehouse a partir de aplicações escritas em vários idiomas, incluindo Python e Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Como é configurado e instalado no DSVM? 

 O SQL Server está instalado da forma padrão. Pode ser encontrado em `C:\Program Files\Microsoft SQL Server` . A instância do Servidor de Aprendizagem automática in-database é encontrada em `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` . O DSVM também tem uma instância separada autónoma do Servidor de Aprendizagem automática, que é instalada em `C:\Program Files\Microsoft\R Server\R_SERVER` . Estas duas situações do Servidor de Aprendizagem automática não partilham bibliotecas.


## <a name="apache-spark-2x-standalone"></a>Faísca Apache 2.x (Autónoma)

| | |
| ------------- | ------------- |
| O que é?   | Uma instância autónoma (nó único em processo) da popular plataforma Apache Spark; um sistema de processamento rápido e em larga escala de dados e aprendizagem automática     |
| Edições DSVM suportadas      | Linux     |
| Usos típicos      | * Desenvolvimento rápido de aplicações Spark/PySpark localmente com um conjunto de dados menor e posterior implantação em grandes clusters de faíscas, como Azure HDInsight<br/> * Teste o contexto de faísca do servidor de aprendizagem de máquinas da Microsoft <br />* Utilize a biblioteca [MMLSpark](https://github.com/Azure/mmlspark) de código aberto da SparkML ou da Microsoft para construir aplicações ML |
| Ligações a amostras      |    Amostra de Jupyter: <br />&nbsp;&nbsp;* ~/cadernos/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/cadernos/MMLSpark <br /> Microsoft Machine Learning Server (contexto spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Ferramentas relacionadas no DSVM       | PySpark<br/>Jupyter (Spark/PySpark Kernels)<br/>Microsoft Machine Learning Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Como utilizá-lo
Pode submeter trabalhos spark na linha de comando executando o `spark-submit` comando ou `pyspark` comando. Também pode criar um caderno Jupyter criando um novo caderno com o kernel Spark.

Pode utilizar o Spark from R utilizando bibliotecas como SparkR, Sparklyr e Microsoft Machine Learning Server, que estão disponíveis no DSVM. Consulte os indicadores das amostras na tabela anterior.

### <a name="setup"></a>Configuração
Antes de executar um contexto de faísca no Microsoft Machine Learning Server na edição DSVM do Ubuntu Linux, tem de completar um passo de configuração único para ativar um único nó local Hadoop HDFS e yarn. Por predefinição, os serviços Hadoop são instalados, mas desativados no DSVM. Para os ativar, executar os seguintes comandos como raiz da primeira vez:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Pode parar os serviços relacionados com Hadoop quando já não precisar deles funcionando ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

Uma amostra que demonstra como desenvolver e testar a MRS num contexto remoto de faísca (que é a instância de faísca autónoma no DSVM) é fornecida e disponível no `/dsvm/samples/MRS` diretório.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Como é configurado e instalado no DSVM? 
|Plataforma|Localização de instalação ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X-bin-hadoopX.X|


As bibliotecas para aceder aos dados do armazenamento Azure Blob ou do Azure Data Lake Storage, utilizando as bibliotecas de aprendizagem automática microsoft MMLSpark, estão pré-instaladas em $SPARK_HOME/jars. Estes JARs são carregados automaticamente quando a Faísca começa. Por predefinição, a Spark utiliza dados no disco local. 

Para a instância Spark no DSVM para aceder aos dados armazenados no armazenamento blob ou no armazenamento do Lago de Dados Azure, deve criar e configurar o `core-site.xml` ficheiro com base no modelo encontrado no $SPARK_HOME/conf/core-site.xml.template. Você também deve ter as credenciais apropriadas para aceder ao armazenamento blob e ao armazenamento do Lago de Dados Azure. (Note que os ficheiros de modelo utilizam espaços reservados para armazenamento de Blob e configurações de armazenamento de lago de dados Azure.)

Para obter informações mais detalhadas sobre a criação de credenciais de serviço de armazenamento de data lake [azure, consulte autenticação com Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Depois de serem inseridas as credenciais para armazenamento Blob ou Azure Data Lake Storage no ficheiro core-site.xml, pode referenciar os dados armazenados nessas fontes através do prefixo URI de wasb:// ou adl://.

