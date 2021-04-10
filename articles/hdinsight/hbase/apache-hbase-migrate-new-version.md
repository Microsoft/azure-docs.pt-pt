---
title: Migrar um cluster HBase para uma nova versão - Azure HDInsight
description: Como migrar os clusters Apache HBase para uma versão mais recente em Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 43b46d19503856f5eae38272299f73d9c80055b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868890"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrar um cluster Apache HBase para uma nova versão

Este artigo discute os passos necessários para atualizar o seu cluster Apache HBase no Azure HDInsight para uma versão mais recente.

O tempo de paragem durante a atualização deve ser mínimo, na ordem dos minutos. Este tempo de inatividade é causado pelos passos para lavar todos os dados de memória, em seguida, o tempo para configurar e reiniciar os serviços no novo cluster. Os seus resultados variarão, dependendo do número de nós, quantidade de dados e outras variáveis.

## <a name="review-apache-hbase-compatibility"></a>Rever compatibilidade apache HBase

Antes de atualizar o Apache HBase, certifique-se de que as versões HBase nos clusters de origem e destino são compatíveis. Para mais informações, consulte [os componentes e versões Apache Hadoop disponíveis com HDInsight.](../hdinsight-component-versioning.md)

> [!NOTE]  
> Recomendamos vivamente que reveja a matriz de compatibilidade da versão no [livro HBase](https://hbase.apache.org/book.html#upgrading). Quaisquer incompatibilidades de rutura devem ser descritas nas notas de lançamento da versão HBase.

Aqui está uma matriz de compatibilidade de versão exemplo. Y indica compatibilidade e N indica uma potencial incompatibilidade:

| Tipo de compatibilidade | Versão principal| Versão secundária | Patch |
| --- | --- | --- | --- |
| Compatibilidade de fios Client-Server | N | Y | Y |
| Compatibilidade Server-Server | N | Y | Y |
| Compatibilidade do formato de ficheiro | N | Y | Y |
| Compatibilidade da API do Cliente | N | Y | Y |
| Compatibilidade binária do cliente | N | N | Y |
| **Compatibilidade limitada do lado do servidor** |  |  |  |
| Estável | N | Y | Y |
| Evoluindo | N | N | Y |
| Instável | N | N | N |
| Compatibilidade da dependência | N | Y | Y |
| Compatibilidade operacional | N | N | Y |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Upgrade com a mesma versão principal Apache HBase

Para atualizar o seu cluster Apache HBase no Azure HDInsight, complete os seguintes passos:

1. Certifique-se de que a sua aplicação é compatível com a nova versão, como mostra a matriz de compatibilidade HBase e as notas de lançamento. Teste a sua aplicação num cluster que executa a versão alvo de HDInsight e HBase.

1. [Configurar um novo cluster de destino HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) utilizando a mesma conta de armazenamento, mas com um nome de recipiente diferente:

   :::image type="content" source="./media/apache-hbase-migrate-new-version/same-storage-different-container.png" alt-text="Use a mesma conta de Armazenamento, mas crie um recipiente diferente" border="true":::

1. Lave o cluster HBase de origem, que é o cluster que está a atualizar. A HBase escreve dados de entrada numa loja de memórias, chamada _memstore_. Depois de a memstore atingir um determinado tamanho, a HBase coloca-a em disco para armazenamento a longo prazo na conta de armazenamento do cluster. Ao eliminar o antigo cluster, as memstores são recicladas, potencialmente perdendo dados. Para lavar manualmente a memstore para cada mesa para o disco, execute o seguinte script. A versão mais recente deste guião está no [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)da Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```

1. Pare de ingestão no antigo aglomerado HBase.

1. Para garantir que quaisquer dados recentes na memstore são lavados, volte a executar o script anterior.

1. Inscreva-se no [Apache Ambari](https://ambari.apache.org/) no antigo cluster `https://OLDCLUSTERNAME.azurehdidnsight.net` e pare os serviços HBase. Quando lhe for solicitado que confirme que pretende parar os serviços, verifique a caixa para ligar o modo de manutenção para hbase. Para obter mais informações sobre a ligação e utilização de Ambari, consulte [gerir os clusters HDInsight utilizando o Ambari Web UI](../hdinsight-hadoop-manage-ambari.md).

    :::image type="content" source="./media/apache-hbase-migrate-new-version/stop-hbase-services1.png" alt-text="Em Ambari, clique em Serviços > HBase > Parar em Ações de Serviço" border="true":::

    :::image type="content" source="./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png" alt-text="Verifique o modo de manutenção turn on para a caixa de verificação HBase e, em seguida, confirme" border="true":::

1. Se não estiver a utilizar clusters HBase com a função Escritas Melhoradas, salte este passo. É necessário apenas para clusters HBase com funcionalidade De Escritas Avançadas.

   Faça backup do dir WAL sob o HDFS, correndo abaixo dos comandos a partir de uma sessão de ssh em qualquer um dos nós do zookeeper ou nos nós de trabalhador do cluster original.
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup**
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup**
   ```
    
1. Inscreva-se no Ambari no novo cluster HDInsight. Mude a `fs.defaultFS` definição HDFS para indicar o nome do recipiente utilizado pelo cluster original. Esta definição está em **HDFS > Configs > Advanced > Advanced core-site**.

   :::image type="content" source="./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png" alt-text="Em Ambari, clique em Serviços > HDFS > Configs > Advanced" border="true":::

   :::image type="content" source="./media/apache-hbase-migrate-new-version/change-container-name.png" alt-text="Em Ambari, mude o nome do recipiente" border="true":::

1. Se não estiver a utilizar clusters HBase com a função Escritas Melhoradas, salte este passo. É necessário apenas para clusters HBase com funcionalidade De Escritas Avançadas.

   Mude o `hbase.rootdir` caminho para apontar para o recipiente do aglomerado original.

   :::image type="content" source="./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png" alt-text="Em Ambari, mude o nome do recipiente para HBase rootdir" border="true":::
    
1. Se não estiver a utilizar clusters HBase com a função Escritas Melhoradas, salte este passo. É necessário apenas para clusters HBase com funcionalidade De Escritas Avançadas, e apenas nos casos em que o seu cluster original era um cluster HBase com funcionalidade de Escritas Avançadas.

   Limpe os dados do zookeeper e do WAL FS para este novo cluster. Emite os seguintes comandos em qualquer um dos nós do zookeeper ou nos nós do trabalhador:

   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit

   hdfs dfs -rm -r hdfs://mycluster/hbasewal**
   ```

1. Se não estiver a utilizar clusters HBase com a função Escritas Melhoradas, salte este passo. É necessário apenas para clusters HBase com funcionalidade De Escritas Avançadas.
   
   Restaurar o dir WAL para o HDFS do novo cluster a partir de uma sessão de ssh em qualquer um dos nós do zookeeper ou nós operários do novo cluster.
   
   ```bash
   hdfs dfs -cp /hbase-wal-backup/hbasewal hdfs://mycluster/**
   ```
   
1. Se estiver a atualizar o HDInsight 3.6 a 4.0, siga os passos abaixo, caso contrário, salte para o passo 13:

    1. Reinicie todos os serviços necessários em Ambari selecionando **Serviços**  >  **Reiniciar todos os necessários**.
    1. Pare o serviço HBase.
    1. SSH para o nó Zookeeper, e execute o comando [zkCli](https://github.com/go-zkcli/zkcli) `rmr /hbase-unsecure` para remover o znode raiz HBase do Zookeeper.
    1. Reiniciar a Base H.

1. Se estiver a atualizar para qualquer outra versão HDInsight além do 4.0, siga estes passos:
    1. Guarde as alterações.
    1. Reinicie todos os serviços necessários, conforme indicado pela Ambari.

1. Aponte a sua candidatura para o novo cluster.

    > [!NOTE]  
    > O DNS estático para a sua aplicação muda ao atualizar. Em vez de codificar duramente este DNS, pode configurar um CNAME nas definições de DNS do seu nome de domínio que aponta para o nome do cluster. Outra opção é utilizar um ficheiro de configuração para a sua aplicação que pode atualizar sem recolocar.

1. Inicie a ingestão para ver se está tudo funcionando como esperado.

1. Se o novo cluster for satisfatório, elimine o cluster original.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre [o Apache HBase](https://hbase.apache.org/) e a atualização dos clusters HDInsight, consulte os seguintes artigos:

* [Atualize um cluster HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md)
* [Monitorize e gere a Azure HDInsight utilizando o Apache Ambari Web UI](../hdinsight-hadoop-manage-ambari.md)
* [Componentes e versões Apache Hadoop](../hdinsight-component-versioning.md)
* [Otimizar Apache HBase](../optimize-hbase-ambari.md)
