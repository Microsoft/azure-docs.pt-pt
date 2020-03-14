---
title: Criar tabelas do Hive e carregar dados do armazenamento de BLOBs - Team Data Science Process
description: Utilize consultas do Hive para criar tabelas do Hive e carregar dados do armazenamento de Blobs do Azure. Particionar tabelas do Hive e utilizar o otimizada linhas em colunas (ORC) formatação para melhorar o desempenho de consulta.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251664"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Criar tabelas do Hive e carregar dados do armazenamento de Blobs do Azure

Este artigo apresenta genéricas consultas do Hive que criam tabelas do Hive e carregar dados do armazenamento de Blobs do Azure. Também são disponibilizadas algumas orientações sobre a criação de partições de tabelas do Hive e sobre como utilizar o otimizada linhas em colunas (ORC) formatação para melhorar o desempenho de consulta.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem:

* Criei uma conta de Armazenamento Azure. Se precisar de instruções, consulte as contas de [Armazenamento Azure](../../storage/common/storage-introduction.md).
* Aprovisionar um cluster do Hadoop personalizado com o serviço HDInsight.  Se precisar de instruções, consulte clusters de [configuração no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Acesso remoto para o cluster, iniciou sessão e abrir a consola de linha de comandos do Hadoop. Se precisar de instruções, consulte [os aglomerados De Hadoop De Apache](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados para o armazenamento de Blobs do Azure
Se criou uma máquina virtual Azure seguindo as instruções fornecidas na [Configuração de uma máquina virtual Azure para análise avançada,](../../machine-learning/data-science-virtual-machine/overview.md)este ficheiro de script deveria ter sido descarregado para o *C:\\Utilizadores\\\<nome de utilizador\>documentos \\documentos\\* data Science Scripts na máquina virtual. Estas consultas da Hive só requerem que forneça uma configuração de armazenamento de blocos de dados e blob Azure nos campos apropriados para estar pronto para submissão.

Assumimos que os dados relativos às tabelas da Hive estão num formato tabular **não comprimido** e que os dados foram enviados para o recipiente padrão (ou para um adicional) da conta de armazenamento utilizada pelo cluster Hadoop.

Se quiser praticar nos Dados da Viagem de Táxi de **NYC,** precisa de:

* **baixar** os ficheiros 24 [nyC Taxi Trip Data](https://www.andresmh.com/nyctaxitrips) (12 Ficheiros Trip e 12 ficheiros Fare),
* **desapertar** todos os ficheiros em ficheiros .csv, e, em seguida,
* **carregá-los** para o predefinido (ou recipiente apropriado) da conta de armazenamento Azure; as opções para tal conta aparecem no Armazenamento De Uso Azure com o tópico de [clusters Azure HDInsight.](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) O processo de upload dos ficheiros .csv para o recipiente predefinido na conta de armazenamento pode ser encontrado nesta [página](hive-walkthrough.md#upload).

## <a name="submit"></a>Como submeter consultas da Hive
Consultas do Hive podem ser submetidas através de:

* [Submeter consultas de Colmeia através da Linha de Comando Hadoop em headnode do aglomerado hadoop](#headnode)
* [Submeter consultas de Colmeia com o Editor da Colmeia](#hive-editor)
* [Submeter consultas de Colmeia com comandos Azure PowerShell](#ps)

Consultas do Hive são semelhantes a SQL. Se estiver familiarizado com o SQL, poderá achar útil a [Colmeia para utilizadores SQL Cheat Sheet.](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

Quando submeter uma consulta do Hive, também pode controlar o destino da saída das consultas do Hive, independentemente de serem na tela ou num arquivo local no nó principal ou para um blob do Azure.

### <a name="headnode"></a>Submeter consultas de Colmeia através da Linha de Comando Hadoop em headnode do aglomerado hadoop
Se a consulta do Hive for complexa, enviá-lo diretamente no nó principal do Hadoop cluster normalmente leva para ativar o mais rápido em torno de enviá-lo com um Editor do Hive ou o Azure PowerShell scripts.

Inicie sessão no nó da cabeça do cluster Hadoop, abra a Linha de Comando Hadoop no ambiente de trabalho do nó de cabeça e introduza o comando `cd %hive_home%\bin`.

Existem três formas de submeter consultas do Hive da linha de comandos do Hadoop:

* diretamente
* usando ficheiros '.hql'
* com o console de comando do Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Submeta consultas do Hive diretamente no Hadoop linha de comandos.
Você pode executar comando como `hive -e "<your hive query>;` para submeter consultas simples da Hive diretamente na Linha de Comando Hadoop. Eis um exemplo, em que a caixa vermelha descreve o comando que envia a consulta do Hive e a caixa verde descreve o resultado da consulta do Hive.

![Comando para submeter a consulta do Hive com o resultado da consulta do Hive](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Submeter consultas da Hive em ficheiros '.hql'
Quando a consulta do Hive é mais complicada e tem várias linhas, edição de consultas na linha de comandos ou a consola de comandos do ramo de registo não é prático. Uma alternativa é usar um editor de texto no nó de cabeça do cluster Hadoop para salvar as consultas da Colmeia num ficheiro '.hql' num diretório local do nó de cabeça. Em seguida, a consulta da Colmeia no ficheiro '.hql' pode ser submetida utilizando o argumento `-f` da seguinte forma:

    hive -f "<path to the '.hql' file>"

![Consulta de colmeia em um ficheiro '.hql'](./media/move-hive-tables/run-hive-queries-3.png)

**Suprimir a impressão de tela de estado de progresso das consultas da Hive**

Por predefinição, depois de consulta do Hive é submetida na linha de comandos do Hadoop, o progresso da tarefa de mapa/redução é impressos na tela. Para suprimir a impressão do ecrã do Mapa/Reduzir o progresso do trabalho, pode utilizar um argumento `-S` ("S" na maiúscula) na linha de comando da seguinte forma:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Submeta consultas do Hive na consola de comandos do Hive.
Também pode entrar primeiro na consola de comando hive executando `hive` de comando na Linha de Comando Hadoop e, em seguida, submeter consultas de Hive na consola de comando Hive. Eis um exemplo. Neste exemplo, as duas caixas vermelhas realçam os comandos utilizados para introduza a consola de comandos do Hive e a consulta do Hive submetido na consola de comandos do Hive, respectivamente. Na caixa verde destaca o resultado da consulta do Hive.

![Abra a consola de comandos do Hive e introduza o comando, ver o resultado da consulta do Hive](./media/move-hive-tables/run-hive-queries-2.png)

Os exemplos anteriores de saída diretamente os resultados da consulta do Hive no ecrã. Também pode escrever a saída num ficheiro local no nó principal ou para um blob do Azure. Em seguida, pode utilizar outras ferramentas para analisar mais aprofundadamente o resultado das consultas do Hive.

**Resultados da consulta da Hive de saída para um ficheiro local.**
Para a saída de resultados da consulta do Hive para um diretório local no nó principal, tem de submeter a consulta do Hive da linha de comandos do Hadoop da seguinte forma:

    hive -e "<hive query>" > <local path in the head node>

No exemplo seguinte, a saída da consulta da Hive é escrita num ficheiro `hivequeryoutput.txt` em diretório `C:\apps\temp`.

![Saída da consulta do Hive](./media/move-hive-tables/output-hive-results-1.png)

**Resultados da consulta da Colmeia de saída para uma bolha Azure**

Também pode emitir os resultados da consulta do Hive para um blob do Azure, dentro do contentor predefinido do cluster de Hadoop. A consulta do Hive para isso é o seguinte:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

No exemplo seguinte, a saída da consulta da Colmeia é escrita para um diretório blob `queryoutputdir` dentro do recipiente padrão do cluster Hadoop. Aqui, só tem de fornecer o nome do diretório, sem o nome do blob. Um erro é lançado se fornecer nomes de diretório e blob, como `wasb:///queryoutputdir/queryoutput.txt`.

![Saída da consulta do Hive](./media/move-hive-tables/output-hive-results-2.png)

Se abrir o contentor predefinido do cluster de Hadoop com o Explorador de armazenamento do Azure, pode ver o resultado da consulta do Hive, conforme mostrado na figura a seguir. Pode aplicar o filtro (realçado por caixa vermelha) para obter apenas o blob com letras especificadas de nomes.

![Explorador de armazenamento do Azure que mostra a saída da consulta do Hive](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a>Submeter consultas de Colmeia com o Editor da Colmeia
Também pode utilizar a Consola de Consulta (Hive Editor) introduzindo um URL do formulário https:\//\<nome de *cluster Hadoop>.azurehdinsight.net/Home/HiveEditor* num navegador web. Tem de ser conectado a ver esta consola e então, precisa que as credenciais de cluster do Hadoop.

### <a name="ps"></a>Submeter consultas de Colmeia com comandos Azure PowerShell
Também pode utilizar o PowerShell para submeter consultas do Hive. Para obter instruções, consulte [Submeter trabalhos de colmeia utilizando powershell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Criar base de dados e tabelas da Hive
As consultas da Hive são partilhadas no [repositório GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) e podem ser descarregadas a partir daí.

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

* \<nome da base de **dados\>:** o nome da base de dados que pretende criar. Se quiser apenas usar a base de dados padrão, a consulta "*criar base de dados ...* " pode ser omitida.
* \<nome de **mesa\>:** o nome da tabela que pretende criar na base de dados especificada. Se pretender utilizar a base de dados predefinida, a tabela pode ser consultada\< *\>* sem \<nome da base de dados\>.
* **\<separador de campo\>:** o separador que delimita os campos no ficheiro de dados a ser enviado para a tabela Da Colmeia.
* **\<separador de linha\>:** o separador que delimita as linhas no ficheiro de dados.
* \<local de **armazenamento\>:** o local de armazenamento Azure para guardar os dados das tabelas da Colmeia. Se não especificar o local de *armazenamento \<LOCAL\>,* a base de dados e as tabelas são armazenadas na *colmeia/armazém/diretório* no recipiente predefinido do cluster da Colmeia por defeito. Se pretender especificar a localização de armazenamento, a localização de armazenamento tem de estar dentro do contentor predefinido para a base de dados e tabelas. Esta localização deve ser referida como localização em relação ao recipiente padrão do cluster no formato *'wasb:/// diretório\<1>'* ou *'wasb:///\<diretório 1>/\<diretório 2>',* etc. Após a execução da consulta, os diretórios relativos são criados dentro do recipiente predefinido.
* **TBLPROPERTIES ("skip.header.line.count="1")** : Se o ficheiro de dados tiver uma linha de cabeçalho, tem de adicionar esta propriedade **no final** da consulta de mesa de *criação.* Caso contrário, a linha de cabeçalho é carregada como um registo à tabela. Se o ficheiro de dados não tiver uma linha de cabeçalho, esta configuração pode ser omitida na consulta.

## <a name="load-data"></a>Carregue dados para as tabelas da Colmeia
Esta é a consulta de Hive que carrega dados para uma tabela do Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* \<caminho para os **dados blob\>** : Se o ficheiro blob a ser carregado para a tabela Hive estiver no recipiente padrão do cluster Hadoop HDInsight, o caminho\<para os dados *blob\>* deve estar no formato *'wasb://\<diretório neste recipiente>/\<nome de ficheiro blob>'* O arquivo de BLOBs também pode ser num contentor adicional do cluster HDInsight Hadoop. Neste caso, *\<caminho para os dados blob\>* devem estar no formato *'wasb://\<nome de contentor>\<nome da conta de armazenamento>.blob.core.windows.net/\<blob file name>'* .

  > [!NOTE]
  > Os dados de BLOBs para ser carregado para uma tabela do Hive tem de estar na predefinição ou adicional contentor da conta do storage para o cluster do Hadoop. Caso contrário, a consulta *DE DADOS LOAD* falha queixando-se de que não consegue aceder aos dados.
  >
  >

## <a name="partition-orc"></a>Tópicos avançados: tabela dividida e armazenar dados da Hive em formato ORC
Se os dados forem grandes, a criação de partições de tabela é benéfica para consultas que apenas precisam de analisar algumas partições da tabela. Por exemplo, é razoável particionar os dados de registo de um site da web até datas.

Além de criação de partições de tabelas do Hive, também é benéfico armazenar os dados de Hive no formato otimizado linhas em colunas (ORC). Para obter mais informações sobre a formatação ORC, consulte <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">A utilização de ficheiros ORC melhora</a>o desempenho quando a Hive está a ler, a escrever e a processar dados .

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

Ao consultar tabelas divididas, recomenda-se adicionar a condição de partição no **início** da cláusula `where`, que melhora a eficiência de pesquisa.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Armazenar dados da Hive em formato ORC
Não é possível carregar diretamente dados do armazenamento de BLOBs para tabelas do Hive, que é armazenado no formato ORC. Eis os passos que o que precisa de efetuar para carregar blobs de dados do Azure para tabelas de ramo de registo armazenadas em formato ORC.

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
> Se a tabela TEXTFILE\<nome da base de dados\>\<nome de *tabela de ficheiros* de texto externo\>tiver divisórias, no PASSO 3, o comando `SELECT * FROM <database name>.<external textfile table name>` seleciona a variável de partição como um campo no conjunto de dados devolvido. Inseri-lo no nome da base de dados\<\>.\<nome de *tabela ORC\>* falha, uma vez que\<nome da base de dados\>.\<nome de tabela *ORC\>* não tem a variável de partição como um campo no esquema de mesa. Neste caso, é necessário selecionar especificamente os campos a inserir na\<nome da base de *dados\>.\<nome de tabela ORC\>* seguinte:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

É seguro deixar cair o\<nome de tabela de *ficheiros* de texto externo\>quando utilizar a seguinte consulta depois de todos os dados terem sido inseridos no nome da base de dados\<\>\<nome de *tabela ORC\>:*

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Depois de seguir este procedimento, deve ter uma tabela com dados no formato ORC pronto a utilizar.  
