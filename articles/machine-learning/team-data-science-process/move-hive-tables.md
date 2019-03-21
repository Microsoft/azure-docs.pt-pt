---
title: Criar tabelas do Hive e carregar dados do armazenamento de BLOBs - Team Data Science Process
description: Utilize consultas do Hive para criar tabelas do Hive e carregar dados do armazenamento de Blobs do Azure. Particionar tabelas do Hive e utilizar o otimizada linhas em colunas (ORC) formatação para melhorar o desempenho de consulta.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d67bd26dcb2ac0b3bf909e1ef3d5ca75a0882eb3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840652"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Criar tabelas do Hive e carregar dados do armazenamento de Blobs do Azure

Este artigo apresenta genéricas consultas do Hive que criam tabelas do Hive e carregar dados do armazenamento de Blobs do Azure. Também são disponibilizadas algumas orientações sobre a criação de partições de tabelas do Hive e sobre como utilizar o otimizada linhas em colunas (ORC) formatação para melhorar o desempenho de consulta.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [sobre as contas de armazenamento](../../storage/common/storage-introduction.md).
* Aprovisionar um cluster do Hadoop personalizado com o serviço HDInsight.  Se precisar de instruções, consulte [programa de configuração de Clusters no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Acesso remoto para o cluster, iniciou sessão e abrir a consola de linha de comandos do Hadoop. Se precisar de instruções, consulte [clusters do Apache Hadoop gerir](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados para o armazenamento de Blobs do Azure
Se criou uma máquina virtual do Azure ao seguir as instruções fornecidas [configurar uma máquina virtual do Azure para análise avançada](../../machine-learning/data-science-virtual-machine/overview.md), este ficheiro de script deve ter sido transferido para o *c:\\utilizadores \\ \<nome de utilizador\>\\documentos\\Scripts de ciência de dados* diretório na máquina virtual. Estas consultas do Hive apenas exigem que se conectar no seu próprio esquema de dados e a configuração de armazenamento de Blobs do Azure nos campos apropriados para estar preparado para envio.

Partimos do princípio de que os dados de tabelas do Hive estão numa **descomprimidos** formato tabular, e que os dados tem sido carregados para a predefinição (ou mais) contentor da conta de armazenamento utilizada pelo cluster do Hadoop.

Se quiser prática sobre o **dados de viagens de táxis NYC**, terá de:

* **Baixe** a 24 [dados de viagens de táxis NYC](https://www.andresmh.com/nyctaxitrips) ficheiros (ficheiros de viagens 12 e 12 ficheiros de Europeia),
* **deszipe** todos os ficheiros para os ficheiros. csv e, em seguida,
* **carregue** -os para o padrão (ou contentor adequado) de armazenamento do Azure conta; as opções para esta conta aparecer no [armazenamento de utilização Azure com clusters do Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) tópico. O processo para carregar os ficheiros. csv para o contentor predefinido na conta de armazenamento pode ser encontrado nisso [página](hive-walkthrough.md#upload).

## <a name="submit"></a>Como submeter consultas do Hive
Consultas do Hive podem ser submetidas através de:

1. [Submeter consultas do Hive através de linha de comandos do Hadoop no nó principal do Hadoop cluster](#headnode)
2. [Submeter consultas do Hive com o Editor do Hive](#hive-editor)
3. [Submeter consultas do Hive com comandos do PowerShell do Azure](#ps)

Consultas do Hive são semelhantes a SQL. Se estiver familiarizado com o SQL, pode achar a [Hive para folha de referência de utilizadores do SQL](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) útil.

Quando submeter uma consulta do Hive, também pode controlar o destino da saída das consultas do Hive, independentemente de serem na tela ou num arquivo local no nó principal ou para um blob do Azure.

### <a name="headnode"></a> 1. Submeter consultas do Hive através de linha de comandos do Hadoop no nó principal do Hadoop cluster
Se a consulta do Hive for complexa, enviá-lo diretamente no nó principal do Hadoop cluster normalmente leva para ativar o mais rápido em torno de enviá-lo com um Editor do Hive ou o Azure PowerShell scripts.

Inicie sessão no nó principal do cluster do Hadoop, abra a linha de comandos do Hadoop no ambiente de trabalho do nó principal e introduza o comando `cd %hive_home%\bin`.

Existem três formas de submeter consultas do Hive da linha de comandos do Hadoop:

* diretamente
* usando arquivos de .hql
* com o console de comando do Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Submeta consultas do Hive diretamente no Hadoop linha de comandos.
Pode executar o comando como `hive -e "<your hive query>;` para submeter consultas do Hive simples diretamente no Hadoop linha de comandos. Eis um exemplo, em que a caixa vermelha descreve o comando que envia a consulta do Hive e a caixa verde descreve o resultado da consulta do Hive.

![Comando para submeter a consulta do Hive com o resultado da consulta do Hive](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Submeter consultas do Hive em arquivos de .hql
Quando a consulta do Hive é mais complicada e tem várias linhas, edição de consultas na linha de comandos ou a consola de comandos do ramo de registo não é prático. Uma alternativa é usar um editor de texto no nó principal do cluster do Hadoop para guardar as consultas do Hive num arquivo de .hql num diretório local do nó principal. Em seguida, a consulta do Hive no ficheiro .hql pode ser submetida ao utilizar o `-f` argumento da seguinte forma:

    hive -f "<path to the .hql file>"

![Consulta do Hive num arquivo de .hql](./media/move-hive-tables/run-hive-queries-3.png)

**Suprimir a impressão de ecrã de estado de progresso de consultas do Hive**

Por predefinição, depois de consulta do Hive é submetida na linha de comandos do Hadoop, o progresso da tarefa de mapa/redução é impressos na tela. Para suprimir a impressão de tela de progresso da tarefa de mapa/redução, pode usar um argumento `-S` ("S" em maiúsculas) no comando linha da seguinte forma:

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Submeta consultas do Hive na consola de comandos do Hive.
Pode introduzir também primeiro a consola de comandos do Hive ao executar o comando `hive` na linha de comandos do Hadoop e, em seguida, submeter consultas do Hive na consola de comandos do Hive. Eis um exemplo. Neste exemplo, as duas caixas vermelhas realçam os comandos utilizados para introduza a consola de comandos do Hive e a consulta do Hive submetido na consola de comandos do Hive, respectivamente. Na caixa verde destaca o resultado da consulta do Hive.

![Abra a consola de comandos do Hive e introduza o comando, ver o resultado da consulta do Hive](./media/move-hive-tables/run-hive-queries-2.png)

Os exemplos anteriores de saída diretamente os resultados da consulta do Hive no ecrã. Também pode escrever a saída num ficheiro local no nó principal ou para um blob do Azure. Em seguida, pode utilizar outras ferramentas para analisar mais aprofundadamente o resultado das consultas do Hive.

**Saída de resultados da consulta do Hive num ficheiro local.**
Para a saída de resultados da consulta do Hive para um diretório local no nó principal, tem de submeter a consulta do Hive da linha de comandos do Hadoop da seguinte forma:

    hive -e "<hive query>" > <local path in the head node>

No exemplo a seguir, o resultado da consulta do Hive é escrito num arquivo `hivequeryoutput.txt` no diretório `C:\apps\temp`.

![Saída da consulta do Hive](./media/move-hive-tables/output-hive-results-1.png)

**Resultados de consulta do Hive de saída para um blob do Azure**

Também pode emitir os resultados da consulta do Hive para um blob do Azure, dentro do contentor predefinido do cluster de Hadoop. A consulta do Hive para isso é o seguinte:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

No exemplo a seguir, o resultado da consulta do Hive é escrito para um diretório de blob `queryoutputdir` dentro do contentor predefinido do cluster de Hadoop. Aqui, só tem de fornecer o nome do diretório, sem o nome do blob. É gerado um erro se fornecer nomes de diretório e BLOBs, tal como `wasb:///queryoutputdir/queryoutput.txt`.

![Saída da consulta do Hive](./media/move-hive-tables/output-hive-results-2.png)

Se abrir o contentor predefinido do cluster de Hadoop com o Explorador de armazenamento do Azure, pode ver o resultado da consulta do Hive, conforme mostrado na figura a seguir. Pode aplicar o filtro (realçado por caixa vermelha) para obter apenas o blob com letras especificadas de nomes.

![Explorador de armazenamento do Azure que mostra a saída da consulta do Hive](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. Submeter consultas do Hive com o Editor do Hive
Também pode utilizar a consola de consulta (Editor do Hive) ao introduzir um URL do formulário *https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor* num navegador da web. Tem de ser conectado a ver esta consola e então, precisa que as credenciais de cluster do Hadoop.

### <a name="ps"></a> 3. Submeter consultas do Hive com comandos do PowerShell do Azure
Também pode utilizar o PowerShell para submeter consultas do Hive. Para obter instruções, consulte [tarefas de submeter o Hive com o PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Criar base de dados do Hive e tabelas
As consultas do Hive são partilhadas no [repositório do GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) e pode ser transferido a partir daí.

Esta é a consulta de Hive que cria uma tabela do Hive.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Aqui estão as descrições dos campos que precisa de plug-in e outras configurações:

* **\<nome da base de dados\>**: o nome da base de dados que pretende criar. Se pretender utilizar a base de dados padrão, a consulta *criar base de dados...*  pode ser omitida.
* **\<nome da tabela\>**: o nome da tabela que pretende criar na base de dados especificado. Se pretender utilizar a base de dados padrão, a tabela pode ser chamada diretamente pelo *\<nome da tabela\>* sem \<nome da base de dados\>.
* **\<separador de campo\>**: o separador que delimits campos no ficheiro de dados para ser carregado para a tabela do Hive.
* **\<separador de linha\>**: o separador que delimits linhas no ficheiro de dados.
* **\<localização de armazenamento\>**: a localização de armazenamento do Azure para guardar os dados de tabelas do Hive. Se não especificar *localização \<localização de armazenamento\>*, a base de dados e as tabelas são armazenadas na *hive/Armazém/* diretório no contentor predefinido do cluster através do Hive padrão. Se pretender especificar a localização de armazenamento, a localização de armazenamento tem de estar dentro do contentor predefinido para a base de dados e tabelas. Esta localização tem de ser referido como localização relativa ao contentor predefinido do cluster no formato *' wasb: / / / < diretório 1 > /'* ou *' wasb: / / / < diretório 1 > / < diretório 2 > /'*, etc. Depois da consulta é executada, são criados os diretórios relativos dentro do contentor predefinido.
* **TBLPROPERTIES("skip.header.line.count"="1")**: Se o ficheiro de dados tem uma linha de cabeçalho, tem de adicionar esta propriedade **no final** da *criar tabela* consulta. Caso contrário, a linha de cabeçalho é carregada como um registo à tabela. Se o ficheiro de dados não tiver uma linha de cabeçalho, esta configuração pode ser omitida na consulta.

## <a name="load-data"></a>Carregar dados para tabelas do Hive
Esta é a consulta de Hive que carrega dados para uma tabela do Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **\<caminho para os dados de BLOBs\>**: Se o arquivo de BLOBs para ser carregado para a tabela do Hive no contentor predefinido do cluster HDInsight Hadoop, o *\<caminho para os dados de blob\>* deve estar no formato *' wasb: / / /<directory in this container> /<blob file name>'*. O arquivo de BLOBs também pode ser num contentor adicional do cluster HDInsight Hadoop. Neste caso, *\<caminho para os dados de blob\>* deve estar no formato *' wasb: / /<container name><storage account name>.blob.core.windows.net/<blob file name>'*.

  > [!NOTE]
  > Os dados de BLOBs para ser carregado para uma tabela do Hive tem de estar na predefinição ou adicional contentor da conta do storage para o cluster do Hadoop. Caso contrário, o *carregar dados* consulta reclamar que ele não é possível acessar os dados de falha.
  >
  >

## <a name="partition-orc"></a>Tópicos avançados: particionados de tabela e o arquivo de dados de Hive no formato ORC
Se os dados forem grandes, a criação de partições de tabela é benéfica para consultas que apenas precisam de analisar algumas partições da tabela. Por exemplo, é razoável particionar os dados de registo de um site da web até datas.

Além de criação de partições de tabelas do Hive, também é benéfico armazenar os dados de Hive no formato otimizado linhas em colunas (ORC). Para obter mais informações sobre a formatação de ORC, consulte <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">ficheiros ORC usando melhora o desempenho ao Hive é ler, escrever e processamento de dados</a>.

### <a name="partitioned-table"></a>Tabela particionada
Esta é a consulta de Hive que cria uma tabela particionada e carrega dados para ele.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Ao consultar as tabelas particionadas, é recomendado para adicionar a condição de partição na **início** do `where` cláusula como isso melhora a eficácia da pesquisa de forma significativa.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Dados de Hive Store em formato ORC
Não é possível carregar diretamente dados do armazenamento de BLOBs para tabelas do Hive, que é armazenado no formato ORC. Eis os passos que o que precisa de efetuar para carregar blobs de dados do Azure para tabelas de ramo de registo armazenadas em formato ORC.

Criar uma tabela externa **ARMAZENADOS TEXTFILE de AS** e carregar dados do armazenamento de BLOBs para a tabela.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Criar uma tabela interna com o mesmo esquema de tabela externa no passo 1, com o delimitador de campos mesmo e armazenar os dados de Hive no formato ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Selecionar dados da tabela externa no passo 1 e inserir na tabela ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Se a tabela TEXTFILE *\<nome da base de dados\>.\< nome da tabela externa textfile\>* tem partições, no passo 3, o `SELECT * FROM <database name>.<external textfile table name>` comando seleciona a variável de partição como um campo no conjunto de dados retornado. Inseri-los para o *\<nome da base de dados\>.\< Nome da tabela ORC\>* falhar desde *\<nome da base de dados\>.\< Nome da tabela ORC\>* não tem a variável de partição como um campo no esquema de tabela. Neste caso, tem de selecionar especificamente os campos a ser inserido para *\<nome da base de dados\>.\< Nome da tabela ORC\>* da seguinte forma:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

É seguro remover o *\<nome da tabela externa textfile\>* quando utilizando a seguinte consulta depois de todos os dados foi inserido nas  *\<nome de base de dados\>.\< Nome da tabela ORC\>*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Depois de seguir este procedimento, deve ter uma tabela com dados no formato ORC pronto a utilizar.  
