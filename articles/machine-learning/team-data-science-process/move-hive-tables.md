---
title: Crie tabelas de Colmeia e carregue dados do armazenamento blob - Team Data Science Process
description: Use consultas da Hive para criar tabelas da Colmeia e carregar dados do armazenamento de blob Azure. Tabelas de colmeia de partição e use a formatação Colunar de Linha Otimizada (ORC) para melhorar o desempenho da consulta.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 625d9d5c5ecf095d4acbff625754b2065f184536
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79251664"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Crie tabelas de colmeias e carregue dados do Armazenamento De Blob Azure

Este artigo apresenta consultas genéricas da Hive que criam tabelas da Colmeia e carregam dados do armazenamento de blob Azure. Também são fornecidas algumas orientações sobre a partilha de tabelas da Colmeia e sobre a utilização da formatação Colunar de Linha Otimizada (ORC) para melhorar o desempenho da consulta.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criei uma conta de Armazenamento Azure. Se precisar de instruções, consulte as contas de [Armazenamento Azure](../../storage/common/storage-introduction.md).
* Forprovisionou um cluster Hadoop personalizado com o serviço HDInsight.  Se precisar de instruções, consulte clusters de [configuração no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Permitiu o acesso remoto ao cluster, entrou e abriu a consola hadoop Command-Line. Se precisar de instruções, consulte [os aglomerados De Hadoop De Apache](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Enviar dados para armazenamento de blob Azure
Se criou uma máquina virtual Azure seguindo as instruções fornecidas na [Configuração de uma máquina virtual Azure para análise avançada,](../../machine-learning/data-science-virtual-machine/overview.md)este ficheiro de script deveria ter sido descarregado para o *c:\\user\\\<user\>\\name\\Documents Data Scripts directy* na máquina virtual. Estas consultas da Hive só requerem que forneça uma configuração de armazenamento de blocos de dados e blob Azure nos campos apropriados para estar pronto para submissão.

Assumimos que os dados relativos às tabelas da Hive estão num formato tabular **não comprimido** e que os dados foram enviados para o recipiente padrão (ou para um adicional) da conta de armazenamento utilizada pelo cluster Hadoop.

Se quiser praticar nos Dados da Viagem de Táxi de **NYC,** precisa de:

* **baixar** os ficheiros 24 [nyC Taxi Trip Data](https://www.andresmh.com/nyctaxitrips) (12 Ficheiros Trip e 12 ficheiros Fare),
* **desapertar** todos os ficheiros em ficheiros .csv, e, em seguida,
* **carregá-los** para o predefinido (ou recipiente apropriado) da conta de armazenamento Azure; as opções para tal conta aparecem no Armazenamento De Uso Azure com o tópico de [clusters Azure HDInsight.](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) O processo de upload dos ficheiros .csv para o recipiente predefinido na conta de armazenamento pode ser encontrado nesta [página](hive-walkthrough.md#upload).

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Como submeter consultas da Hive
As consultas de colmeia podem ser submetidas utilizando:

* [Submeter consultas de Colmeia através da Linha de Comando Hadoop em headnode do aglomerado hadoop](#headnode)
* [Submeter consultas de Colmeia com o Editor da Colmeia](#hive-editor)
* [Submeter consultas de Colmeia com comandos Azure PowerShell](#ps)

As consultas de colmeia são semelhantes a SQL. Se estiver familiarizado com o SQL, poderá achar útil a [Colmeia para utilizadores SQL Cheat Sheet.](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

Ao submeter uma consulta da Hive, também pode controlar o destino da saída a partir de consultas da Hive, seja no ecrã ou num ficheiro local no nó da cabeça ou numa bolha Azure.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Submeter consultas de Colmeia através da Linha de Comando Hadoop em headnode do aglomerado hadoop
Se a consulta da Colmeia for complexa, a sua apresentação diretamente no nó de cabeça do cluster Hadoop normalmente leva a uma viragem mais rápida do que a submetê-la com um Editor da Colmeia ou scripts Azure PowerShell.

Inicie sessão no nó da cabeça do cluster Hadoop, abra a Linha de Comando Hadoop no ambiente de trabalho do nó da cabeça e introduza o comando `cd %hive_home%\bin`.

Tem três maneiras de submeter consultas da Hive na Linha de Comando Hadoop:

* diretamente
* usando ficheiros '.hql'
* com a consola de comando Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Submeta consultas da Hive diretamente na Linha de Comando Hadoop.
Você pode executar `hive -e "<your hive query>;` comando como submeter consultas simples da Hive diretamente na Linha de Comando Hadoop. Aqui está um exemplo, onde a caixa vermelha descreve o comando que submete a consulta da Colmeia, e a caixa verde descreve a saída da consulta da Colmeia.

![Comando para submeter consulta da Hive com saída da consulta da Hive](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Submeter consultas da Hive em ficheiros '.hql'
Quando a consulta da Hive é mais complicada e tem várias linhas, editar consultas na linha de comando ou na consola de comando hive não é prático. Uma alternativa é usar um editor de texto no nó de cabeça do cluster Hadoop para salvar as consultas da Colmeia num ficheiro '.hql' num diretório local do nó de cabeça. Em seguida, a consulta da Colmeia no ficheiro '.hql' pode ser submetida utilizando o `-f` argumento da seguinte forma:

    hive -f "<path to the '.hql' file>"

![Consulta de colmeia em um ficheiro '.hql'](./media/move-hive-tables/run-hive-queries-3.png)

**Suprimir a impressão de tela de estado de progresso das consultas da Hive**

Por padrão, após a consulta da Hive ser submetida na Linha de Comando Hadoop, o progresso do trabalho Map/Reduce é impresso no ecrã. Para suprimir a impressão do ecrã do Mapa/Reduzir `-S` o progresso do trabalho, pode utilizar um argumento ("S" na parte superior) na linha de comando da seguinte forma:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Submeta consultas da Hive na consola de comando hive.
Também pode entrar primeiro na consola de `hive` comando hive executando o comando na Linha de Comando Hadoop e, em seguida, submeter consultas de Hive na consola de comando Hive. Eis um exemplo. Neste exemplo, as duas caixas vermelhas destacam os comandos utilizados para entrar na consola de comando da Hive, e a consulta da Hive submetida na consola de comando hive, respectivamente. A caixa verde destaca a saída da consulta da Colmeia.

![Abra a consola de comando da Hive e entre no comando, veja a saída da consulta da Hive](./media/move-hive-tables/run-hive-queries-2.png)

Os exemplos anteriores saem diretamente dos resultados da consulta da Hive no ecrã. Também pode escrever a saída para um ficheiro local no nó da cabeça, ou para uma bolha Azure. Em seguida, pode usar outras ferramentas para analisar ainda mais a saída de consultas da Hive.

**Resultados da consulta da Hive de saída para um ficheiro local.**
Para obter resultados de consulta da Hive para um diretório local no nó de cabeça, você tem que submeter a consulta da Colmeia na Linha de Comando Hadoop da seguinte forma:

    hive -e "<hive query>" > <local path in the head node>

No exemplo seguinte, a saída da consulta da Hive é escrita num ficheiro `hivequeryoutput.txt` no diretório `C:\apps\temp`.

![Saída da consulta da Colmeia](./media/move-hive-tables/output-hive-results-1.png)

**Resultados da consulta da Colmeia de saída para uma bolha Azure**

Também pode obter os resultados da consulta da Colmeia para uma bolha Azure, dentro do recipiente padrão do cluster Hadoop. A consulta da Colmeia para isto é a seguinte:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

No exemplo seguinte, a saída da consulta da Colmeia é `queryoutputdir` escrita para um diretório blob dentro do recipiente padrão do cluster Hadoop. Aqui, você só precisa fornecer o nome do diretório, sem o nome blob. Um erro é lançado se fornecer nomes de diretório e blob, tais como `wasb:///queryoutputdir/queryoutput.txt`.

![Saída da consulta da Colmeia](./media/move-hive-tables/output-hive-results-2.png)

Se abrir o recipiente predefinido do cluster Hadoop utilizando o Azure Storage Explorer, pode ver a saída da consulta da Colmeia, como mostra a seguinte figura. Pode aplicar o filtro (realçado pela caixa vermelha) para apenas recuperar a bolha com letras especificadas em nomes.

![Explorador de armazenamento azure mostrando saída da consulta da Colmeia](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Submeter consultas de Colmeia com o Editor da Colmeia
Também pode utilizar a Consola de Consulta (Editor da Hive) introduzindo um URL do formulário *https:\//\<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor* num navegador web. Tens de estar registado na consola e por isso precisas das tuas credenciais de cluster Hadoop aqui.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Submeter consultas de Colmeia com comandos Azure PowerShell
Também pode usar o PowerShell para submeter consultas da Hive. Para obter instruções, consulte [Submeter trabalhos de colmeia utilizando powershell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Criar base de dados e tabelas da Hive
As consultas da Hive são partilhadas no [repositório GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) e podem ser descarregadas a partir daí.

Aqui está a consulta da Colmeia que cria uma mesa de Colmeia.

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

Aqui estão as descrições dos campos que precisa para ligar e outras configurações:

* nome da base de dados : o nome da base de dados que pretende criar. ** \<\>** Se quiser apenas usar a base de dados padrão, a consulta "*criar base de dados ...*" pode ser omitida.
* nome da tabela : o nome da tabela que pretende criar dentro da base de dados especificada. ** \<\>** Se pretender utilizar a base de dados predefinida, a \<tabela\>pode ser consultada diretamente pelo * \<nome\> * da mesa sem o nome da base de dados .
* separador de campo: o separador que delimita os campos no ficheiro de dados a ser enviado para a tabela Da Colmeia. ** \<\>**
* separador de linha: o separador que delimita as linhas no ficheiro de dados. ** \<\>**
* local de armazenamento : o local de armazenamento Azure para guardar os dados das tabelas da Colmeia. ** \<\>** Se não especificar a *localização \<\>* do armazenamento da LOCALIZAÇÃO, a base de dados e as tabelas são armazenadas na *colmeia/armazém/diretório* no recipiente predefinido do cluster da Colmeia por defeito. Se pretender especificar o local de armazenamento, o local de armazenamento tem de estar dentro do recipiente predefinido para a base de dados e as tabelas. Esta localização deve ser referida como localização relativamente ao recipiente predefinido do cluster no formato de *\<'wasb:/// diretório 1>/'* ou *\<'wasb:/// diretório 1>/diretório\<2>/',* etc. Após a execução da consulta, os diretórios relativos são criados dentro do recipiente predefinido.
* **TBLPROPERTIES ("skip.header.line.count="1")**: Se o ficheiro de dados tiver uma linha de cabeçalho, tem de adicionar esta propriedade **no final** da consulta de mesa de *criação.* Caso contrário, a linha do cabeçalho é carregada como um recorde para a mesa. Se o ficheiro de dados não tiver uma linha de cabeçalho, esta configuração pode ser omitida na consulta.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Carregue dados para as tabelas da Colmeia
Aqui está a consulta da Colmeia que carrega dados numa mesa da Colmeia.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* caminho **para os\>dados blob : Se o ficheiro blob a ser carregado para a tabela Hive estiver no recipiente padrão do cluster Hadoop HDInsight, o caminho para os dados blob deve estar no formato 'wasb:// diretório neste recipiente>/blob nome de ficheiro>». \<** * \<\> * *\<\<* O ficheiro blob também pode estar num recipiente adicional do cluster Hadoop HDInsight. Neste caso, * \<\> * o caminho para os dados blob deve estar no formato 'wasb:// nome do *\<recipiente>\<nome da conta de armazenamento>.blob.core.windows.net/\<blob file name>'.*

  > [!NOTE]
  > Os dados blob a serem enviados para a tabela Hive devem estar no predefinido ou recipiente adicional da conta de armazenamento para o cluster Hadoop. Caso contrário, a consulta *DE DADOS LOAD* falha queixando-se de que não consegue aceder aos dados.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Tópicos avançados: tabela dividida e armazenar dados da Hive em formato ORC
Se os dados forem grandes, a divisão da tabela é benéfica para consultas que apenas precisam de digitalizar algumas divisórias da tabela. Por exemplo, é razoável dividir os dados de registo de um web site por datas.

Além de dividir tabelas da Hive, também é benéfico armazenar os dados da Colmeia no formato Colunar de Linhas Otimizada (ORC). Para obter mais informações sobre a formatação ORC, consulte <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">A utilização de ficheiros ORC melhora</a>o desempenho quando a Hive está a ler, a escrever e a processar dados .

### <a name="partitioned-table"></a>Mesa dividida
Aqui está a consulta da Colmeia que cria uma tabela dividida e carrega dados nela.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Ao consultar tabelas divididas, recomenda-se adicionar a **beginning** condição `where` de partição no início da cláusula, o que melhora a eficiência de pesquisa.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Armazenar dados da Hive em formato ORC
Não é possível carregar diretamente dados do armazenamento de blob em tabelas da Colmeia que são armazenados no formato ORC. Aqui estão os passos que o você precisa tomar para carregar dados de bolhas Azure para mesas hive armazenadas em formato ORC.

Crie uma tabela externa **ARMAZENADA COMO TEXTFILE** e carregue os dados do armazenamento blob para a mesa.

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

Crie uma tabela interna com o mesmo esquema que a tabela externa no passo 1, com o mesmo delimitador de campo, e guarde os dados da Colmeia no formato ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Selecione dados da tabela externa no passo 1 e insira na tabela ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Se o * \<nome\>da\< base de dados da tabela textfile . o\> nome da tabela de ficheiros* de `SELECT * FROM <database name>.<external textfile table name>` texto externo tem divisórias, no PASSO 3, o comando seleciona a variável de partição como um campo no conjunto de dados devolvido. Inserindo-o no * \<nome\>da base de dados .\< \> O nome* da tabela ORC falha desde * \<o nome\>da base de dados .\< O nome\> * da tabela ORC não tem a variável de partição como campo no esquema da tabela. Neste caso, é necessário selecionar especificamente os campos a inserir no * \<nome\>da base de dados .\< Nome\> * da tabela ORC da seguinte forma:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

É seguro deixar cair o * \<nome\> * da tabela de ficheiros de texto externo quando utilizar a seguinte consulta depois de todos os dados terem sido inseridos no * \<nome\>da base de dados .\< Nome da\>tabela ORC:*

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Após seguir este procedimento, deve ter uma tabela com dados no formato ORC prontos a utilizar.  
