---
title: Criar tabelas de Colmeia e carregar dados do armazenamento blob - Processo de Ciência de Dados de Equipa
description: Use consultas de Colmeia para criar tabelas de Colmeia e carregar dados a partir do armazenamento de bolhas Azure. As tabelas de colmeia de partição e a formatação da Coluna de Linha Otimizada (ORC) para melhorar o desempenho da consulta.
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
ms.openlocfilehash: 5d61c0f5f26bc46b9c4a5bc4a793df1e10710004
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130872"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Criar tabelas de Colmeia e carregar dados a partir do armazenamento do Blob Azure

Este artigo apresenta consultas genéricas de Hive que criam tabelas de Colmeia e carregam dados do armazenamento de bolhas Azure. Algumas orientações também são fornecidas sobre as tabelas de colmeias de partição e sobre a utilização da formatação colunar de linha otimizada (ORC) para melhorar o desempenho da consulta.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criei uma conta de Armazenamento Azure. Se precisar de instruções, consulte [as contas de armazenamento do Azure](../../storage/common/storage-introduction.md).
* Aderiu um cluster Hadoop personalizado com o serviço HDInsight.  Se precisar de instruções, consulte [Clusters de Configuração em HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Permitiu o acesso remoto ao cluster, iniciou sessão e abriu a consola Hadoop Command-Line. Se precisar de instruções, consulte [os agrupamentos Apache Hadoop](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Faça upload de dados para o armazenamento de blob Azure
Se criou uma máquina virtual Azure seguindo as instruções fornecidas na [Configuração de uma máquina virtual Azure para análises avançadas,](../../machine-learning/data-science-virtual-machine/overview.md)este ficheiro de script deveria ter sido descarregado para o *diretório C: \\ Users Documents Data Science \\ \<user name\> \\ \\ Scripts* na máquina virtual. Estas consultas de Hive apenas requerem que forneça um esquema de dados e uma configuração de armazenamento de bolhas Azure nos campos apropriados para estar pronto para submissão.

Assumimos que os dados das tabelas hive estão num formato tabular **não comprimido,** e que os dados foram enviados para o recipiente padrão (ou para um outro) recipiente da conta de armazenamento usado pelo cluster Hadoop.

Se quiser praticar nos dados da viagem de táxi de **NYC,** tem de:

* **descarregue** os 24 ficheiros [NYC Taxi Trip Data](https://www.andresmh.com/nyctaxitrips) (12 ficheiros Trip e 12 Ficheiros Fare),
* **desapertar** todos os ficheiros em ficheiros .csv e, em seguida,
* **carregá-los** para o padrão (ou recipiente apropriado) da conta de Armazenamento Azure; opções para tal conta aparecem no [Use Azure Storage com o tópico de clusters Azure HDInsight.](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) O processo de envio dos ficheiros .csv para o recipiente predefinido na conta de armazenamento pode ser encontrado nesta [página](hive-walkthrough.md#upload).

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Como submeter consultas de Colmeia
As consultas de colmeia podem ser submetidas através de:

* [Submeter consultas de Colmeia através da Linha de Comando Hadoop em headnode do cluster Hadoop](#headnode)
* [Submeter consultas de Colmeia com o Editor da Colmeia](#hive-editor)
* [Submeter consultas de Colmeia com comandos Azure PowerShell](#ps)

As consultas de colmeia são semelhantes a SQL. Se estiver familiarizado com o SQL, poderá achar útil a [Colmeia para utilizadores SQL Cheat Sheet.](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

Ao submeter uma consulta de Hive, também pode controlar o destino da saída a partir de consultas de Hive, seja no ecrã ou num ficheiro local no nó da cabeça ou numa bolha de Azure.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Submeter consultas de Colmeia através da Linha de Comando Hadoop em headnode do cluster Hadoop
Se a consulta da Colmeia for complexa, submetê-la diretamente no nó de cabeça do cluster Hadoop normalmente leva a uma viragem mais rápida do que a submetê-la com um Editor de Colmeia ou scripts Azure PowerShell.

Inicie sessão no nó de cabeça do cluster Hadoop, abra a Linha de Comando Hadoop no ambiente de trabalho do nó de cabeça e introduza o comando `cd %hive_home%\bin` .

Você tem três maneiras de submeter consultas de Colmeia na Linha de Comando Hadoop:

* diretamente
* usando ficheiros '.hql'
* com a consola de comando Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Submeta as consultas de Colmeia diretamente na Linha de Comando Hadoop.
Você pode executar comando como `hive -e "<your hive query>;` enviar simples consultas de Colmeia diretamente na Linha de Comando Hadoop. Aqui está um exemplo, onde a caixa vermelha descreve o comando que submete a consulta da Colmeia, e a caixa verde descreve a saída da consulta da Colmeia.

![Comando para submeter consulta de Colmeia com saída da consulta hive](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Submeter consultas de Colmeia em ficheiros '.hql'
Quando a consulta da Colmeia é mais complicada e tem várias linhas, editar consultas na linha de comando ou consola de comando Hive não é prático. Uma alternativa é usar um editor de texto no nó de cabeça do cluster Hadoop para salvar as consultas de Colmeia num ficheiro '.hql' num diretório local do nó de cabeça. Em seguida, a consulta da Colmeia no ficheiro '.hql' pode ser submetida utilizando o argumento da `-f` seguinte forma:

```console
hive -f "<path to the '.hql' file>"
```

![Consulta de colmeia num ficheiro '.hql'](./media/move-hive-tables/run-hive-queries-3.png)

**Suprimir a impressão de tela de estado de progresso das consultas de Hive**

Por padrão, após a consulta da Hive ser submetida na Linha de Comando Hadoop, o progresso do trabalho Mapa/Redução é impresso no ecrã. Para suprimir a impressão de ecrã do Mapa/Reduzir o progresso do trabalho, pode utilizar um argumento `-S` ("S" em maiúscula) na linha de comando da seguinte forma:

```console
hive -S -f "<path to the '.hql' file>"
hive -S -e "<Hive queries>"
```

#### <a name="submit-hive-queries-in-hive-command-console"></a>Submeta consultas de Colmeia na consola de comando da Hive.
Também pode primeiro introduzir a consola de comando da Colmeia executando o comando `hive` na Linha de Comando Hadoop e, em seguida, submeter consultas de Colmeia na consola de comando da Hive. Eis um exemplo. Neste exemplo, as duas caixas vermelhas destacam os comandos utilizados para entrar na consola de comando da Colmeia, e a consulta hive submetida na consola de comando da Hive, respectivamente. A caixa verde destaca a saída da consulta hive.

![Abra a consola de comando da Colmeia e insira o comando, veja a saída da consulta da Hive](./media/move-hive-tables/run-hive-queries-2.png)

Os exemplos anteriores desempendam diretamente os resultados da consulta da Hive no ecrã. Também pode escrever a saída para um ficheiro local no nó de cabeça ou para uma bolha de Azure. Em seguida, pode usar outras ferramentas para analisar ainda mais a saída das consultas de Colmeia.

**A consulta da Hive de saída resulta num ficheiro local.**
Para obter resultados de consulta de Hive para um diretório local no nó de cabeça, você tem que submeter a consulta de Colmeia na Linha de Comando Hadoop da seguinte forma:

```console
hive -e "<hive query>" > <local path in the head node>
```

No exemplo seguinte, a saída da consulta hive é escrita num ficheiro `hivequeryoutput.txt` em diretório `C:\apps\temp` .

![A screenshot mostra a saída da consulta de Colmeia numa janela da Linha de Comando Hadoop.](./media/move-hive-tables/output-hive-results-1.png)

**Consulta de hive de saída resulta de uma bolha Azure**

Também pode obter os resultados da consulta da Colmeia para uma bolha Azure, dentro do recipiente padrão do cluster Hadoop. A consulta da Colmeia para isto é a seguinte:

```console
insert overwrite directory wasb:///<directory within the default container> <select clause from ...>
```

No exemplo seguinte, a saída da consulta de Hive é escrita para um diretório blob `queryoutputdir` dentro do recipiente padrão do cluster Hadoop. Aqui, só precisa de fornecer o nome do diretório, sem o nome blob. Um erro é lançado se fornecer nomes de diretório e blob, tais como `wasb:///queryoutputdir/queryoutput.txt` .

![A screenshot mostra o comando anterior na janela da Linha de Comando Hadoop.](./media/move-hive-tables/output-hive-results-2.png)

Se abrir o recipiente predefinido do cluster Hadoop utilizando o Azure Storage Explorer, pode ver a saída da consulta hive como mostrado na seguinte figura. Pode aplicar o filtro (realçado pela caixa vermelha) para apenas recuperar a bolha com letras especificadas em nomes.

![Azure Storage Explorer mostrando saída da consulta de Colmeia](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Submeter consultas de Colmeia com o Editor da Colmeia
Também pode utilizar a Consola de Consulta (Hive Editor) introduzindo um URL do formulário *https: \/ / \<Hadoop cluster name> .azurehdinsight.net/Home/HiveEditor* num navegador web. Você deve estar registado na consola e por isso precisa das suas credenciais de cluster Hadoop aqui.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Submeter consultas de Colmeia com comandos Azure PowerShell
Também pode usar o PowerShell para submeter consultas de Colmeia. Para obter instruções, consulte [submeter os trabalhos de colmeia utilizando o PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Criar base de dados e tabelas de Colmeias
As consultas de Colmeia são partilhadas no [repositório GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) e podem ser descarregadas a partir daí.

Aqui está a consulta da Colmeia que cria uma mesa de colmeia.

```hiveql
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
```

Aqui estão as descrições dos campos que precisa de ligar e outras configurações:

* **\<database name\>** : o nome da base de dados que pretende criar. Se apenas quiser utilizar a base de dados padrão, a consulta " *criar base de dados"* pode ser omitida.
* **\<table name\>** : o nome da tabela que pretende criar na base de dados especificada. Se pretender utilizar a base de dados predefinida, a tabela pode ser diretamente encaminhada *\<table name\>* sem \<database name\> .
* **\<field separator\>** : o separador que delimita campos no ficheiro de dados a ser enviado para a tabela Hive.
* **\<line separator\>** : o separador que delimita linhas no ficheiro de dados.
* **\<storage location\>** : o local de armazenamento Azure para guardar os dados das tabelas da Colmeia. Se não especificar *localização, \<storage location\>* a base de dados e as tabelas são armazenadas em *colmeia/armazém/diretório* no recipiente predefinido do cluster da Colmeia por padrão por padrão. Se pretender especificar o local de armazenamento, o local de armazenamento deve estar dentro do recipiente predefinido para a base de dados e tabelas. Esta localização deve ser referida como localização relativa ao recipiente predefinido do cluster no formato de *\<directory 1> "wasb:////"* ou *\<directory 1> / \<directory 2> "wasb:////'* , etc. Após a execução da consulta, os diretórios relativos são criados dentro do recipiente padrão.
* **TBLPROPERTIES ("skip.header.line.count"="1")** : Se o ficheiro de dados tiver uma linha de cabeçalho, tem de adicionar esta propriedade **no final** da consulta de *tabela criar.* Caso contrário, a linha do cabeçalho é carregada como um registo para a mesa. Se o ficheiro de dados não tiver uma linha de cabeçalho, esta configuração pode ser omitida na consulta.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Carregar dados para as tabelas da Colmeia
Aqui está a consulta da Colmeia que carrega dados numa tabela de Colmeias.

```hiveql
LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;
```

* **\<path to blob data\>** : Se o ficheiro blob a ser carregado para a tabela Hive estiver no recipiente predefinido do cluster HDInsight Hadoop, o *\<path to blob data\>* deve estar no formato *\<directory in this container> / \<blob file name> 'wasb://'* . O ficheiro blob também pode estar num recipiente adicional do cluster HDInsight Hadoop. Neste caso, *\<path to blob data\>* deve estar no formato *«wasb:// \<container name> @ \<storage account name> \<blob file name> .blob.core.windows.net/».*

  > [!NOTE]
  > Os dados blob a serem enviados para a tabela Hive devem estar na conta padrão ou adicional da conta de armazenamento do cluster Hadoop. Caso contrário, a consulta *DE DADOS DE CARGA* falha ao queixar-se de que não consegue aceder aos dados.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Tópicos avançados: mesa dividida e armazenar dados da Hive em formato ORC
Se os dados forem grandes, dividir a tabela é benéfico para consultas que só precisam de digitalizar algumas divisórias da tabela. Por exemplo, é razoável dividir os dados de registo de um web site por datas.

Além das tabelas de colmeias de partição, também é benéfico armazenar os dados da Colmeia no formato Coluna de Linha Otimizada (ORC). Para obter mais informações sobre a formatação ORC, consulte <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">a utilização de ficheiros ORC melhora o desempenho quando a Hive está a ler, a escrever e a processar dados.</a>

### <a name="partitioned-table"></a>Mesa dividida
Aqui está a consulta da Colmeia que cria uma tabela dividida e carrega dados nela.

```hiveql
CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
(field1 string,
...
fieldN string
)
PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
    lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
    PARTITION (<partitionfieldname>=<partitionfieldvalue>);
```

Ao consultar as tabelas partitioned, recomenda-se adicionar a condição de partição no **início** da cláusula, o `where` que melhora a eficiência de pesquisa.

```hiveql
select
    field1, field2, ..., fieldN
from <database name>.<partitioned table name>
where <partitionfieldname>=<partitionfieldvalue> and ...;
```

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Armazenar dados da Colmeia em formato ORC
Não é possível carregar diretamente os dados do armazenamento de bolhas nas tabelas Hive que são armazenadas no formato ORC. Aqui estão os passos que precisa de tomar para carregar dados de bolhas Azure para tabelas de Colmeias armazenadas em formato ORC.

Crie uma tabela externa **ARMAZENADA COMO TEXTFILE** e carregue os dados do armazenamento do blob para a tabela.

```hiveql
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
```

Crie uma tabela interna com o mesmo esquema que a tabela externa no passo 1, com o mesmo delimiter de campo, e guarde os dados da Hive no formato ORC.

```hiveql
CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
(
    field1 string,
    field2 int,
    ...
    fieldN date
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;
```

Selecione os dados da tabela externa no passo 1 e insira na tabela ORC

```hiveql
INSERT OVERWRITE TABLE <database name>.<ORC table name>
    SELECT * FROM <database name>.<external textfile table name>;
```

> [!NOTE]
> Se a tabela TEXTFILE *\<database name\> . \<external textfile table name\>* tem divisórias, no PASSO 3, o `SELECT * FROM <database name>.<external textfile table name>` comando seleciona a variável de partição como um campo no conjunto de dados devolvidos. Inserindo-o no *\<database name\> . \<ORC table name\>* falha desde *\<database name\> . \<ORC table name\>* não tem a variável de partição como um campo no esquema de mesa. Neste caso, é necessário selecionar especificamente os campos a introduzir *\<database name\> . \<ORC table name\>* da seguinte forma:
>
>

```hiveql
INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
    SELECT field1, field2, ..., fieldN
    FROM <database name>.<external textfile table name>
    WHERE <partition variable>=<partition value>;
```

É seguro deixar cair a *\<external text file table name\>* seguinte consulta depois de todos os dados terem sido inseridos *\<database name\> \<ORC table name\>* em :

```hiveql
    DROP TABLE IF EXISTS <database name>.<external textfile table name>;
```

Depois de seguir este procedimento, deverá ter uma tabela com dados no formato ORC prontos a ser utilizados.  
