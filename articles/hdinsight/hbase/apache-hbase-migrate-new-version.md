---
title: Migrar um cluster HBase para uma nova versão - Azure HDInsight
description: Como migrar os clusters Apache HBase para uma versão mais recente no Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 30cda7a83feddaeb41385252a61d1dc68a881a47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646511"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrar um cluster Apache HBase para uma nova versão

Este artigo discute os passos necessários para atualizar o seu cluster Apache HBase no Azure HDInsight para uma versão mais recente.

O tempo de inatividade durante a atualização deve ser mínimo, por ordem de minutos. Este tempo de inatividade é causado pelos passos para descarregar todos os dados na memória, em seguida, o tempo para configurar e reiniciar os serviços no novo cluster. Os seus resultados variarão, dependendo do número de nós, quantidade de dados e outras variáveis.

## <a name="review-apache-hbase-compatibility"></a>Rever a compatibilidade apache HBase

Antes de atualizar o Apache HBase, certifique-se de que as versões HBase nos clusters de origem e destino são compatíveis. Para mais informações, consulte [os componentes e versões Apache Hadoop disponíveis com o HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Recomendamos vivamente que reveja a matriz de compatibilidade da versão no [livro HBase](https://hbase.apache.org/book.html#upgrading). Quaisquer incompatibilidades de rutura devem ser descritas nas notas de lançamento da versão HBase.

Aqui está uma matriz de compatibilidade de versão exemplo. Y indica compatibilidade e N indica uma potencial incompatibilidade:

| Tipo de compatibilidade | Versão principal| Versão menor | Patch |
| --- | --- | --- | --- |
| Compatibilidade de fios cliente-servidor | N | S | S |
| Compatibilidade servidor-servidor | N | S | S |
| Compatibilidade do formato de ficheiro | N | S | S |
| Compatibilidade da API do cliente | N | S | S |
| Compatibilidade binária do cliente | N | N | S |
| **Compatibilidade limitada do Lado do Servidor** |  |  |  |
| Estável | N | S | S |
| Evoluindo | N | N | S |
| Instável | N | N | N |
| Compatibilidade da dependência | N | S | S |
| Compatibilidade operacional | N | N | S |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Upgrade com a mesma versão principal Apache HBase

Para atualizar o seu cluster Apache HBase no Azure HDInsight, complete os seguintes passos:

1. Certifique-se de que a sua aplicação é compatível com a nova versão, como mostra a matriz de compatibilidade HBase e as notas de lançamento. Teste a sua aplicação num cluster que executa a versão-alvo de HDInsight e HBase.

1. [Criar um novo cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) de destino utilizando a mesma conta de armazenamento, mas com um nome de recipiente diferente:

    ![Use a mesma conta de Armazenamento, mas crie um recipiente diferente](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

1. Lave o seu cluster HBase de origem, que é o cluster que está a atualizar. A HBase escreve dados de entrada numa loja de memória, chamada _memstore_. Depois de a memstore atingir um determinado tamanho, a HBase coloca-a em disco para armazenamento a longo prazo na conta de armazenamento do cluster. Ao apagar o aglomerado antigo, as memstores são recicladas, potencialmente perdendo dados. Para lavar manualmente a memstore para cada mesa para o disco, executar o seguinte script. A versão mais recente deste script está no [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)do Azure.

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

1. Pare de ingestão no antigo aglomerado de HBase.

1. Para garantir que quaisquer dados recentes na loja são lavados, volte a executar o script anterior.

1. Inscreva-se no [Apache Ambari](https://ambari.apache.org/) `https://OLDCLUSTERNAME.azurehdidnsight.net`no antigo cluster e pare os serviços de HBase. Quando pediu para confirmar que gostaria de parar os serviços, verifique a caixa para ligar o modo de manutenção para HBase. Para obter mais informações sobre a ligação e utilização de Ambari, consulte [os clusters Manage HDInsight utilizando o Ambari Web UI](../hdinsight-hadoop-manage-ambari.md).

    ![Em Ambari, clique em Serviços > HBase > Parar em ações de serviço](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![Verifique o modo de manutenção Turn On para obter caixa de verificação HBase e, em seguida, confirme](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

1. Inscreva-se em Ambari no novo cluster HDInsight. Altere `fs.defaultFS` a definição hDFS para indicar o nome do recipiente utilizado pelo cluster original. Esta definição está sob **o HDFS > Configs > avançado > avançado core-site.**

    ![Em Ambari, clique em Serviços > HDFS > Configs > Advanced](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Em Ambari, mude o nome do recipiente](./media/apache-hbase-migrate-new-version/change-container-name.png)

1. Se não estiver a utilizar clusters HBase com a função Enhanced Writes, ignore este passo. É necessário apenas para clusters HBase com recurso Deescritas Avançadas.

   Mude `hbase.rootdir` o caminho para apontar para o recipiente do cluster original.

    ![Em Ambari, altere o nome do recipiente para rootdir HBase](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)

1. Se estiver a atualizar o HDInsight 3.6 a 4.0, siga os passos abaixo, caso contrário, salte para o passo 10:
    1. Reinicie todos os serviços necessários em Ambari selecionando **serviços** > **Restart All Required**.
    1. Pare o serviço HBase.
    1. SSH para o nó zookeeper, e executar `rmr /hbase-unsecure` o comando [zkCli](https://github.com/go-zkcli/zkcli) para remover o znódo de raiz HBase do Zookeeper.
    1. Reiniciar a Base H.

1. Se estiver a atualizar para qualquer outra versão HDInsight além de 4.0, siga estes passos:
    1. Guarde as alterações.
    1. Reinicie todos os serviços necessários, conforme indicado pela Ambari.

1. Aponte a sua aplicação para o novo cluster.

    > [!NOTE]  
    > O DNS estático para a sua aplicação muda ao atualizar. Em vez de codificar duramente este DNS, pode configurar um CNAME nas definições dNS do seu nome de domínio que aponta para o nome do cluster. Outra opção é utilizar um ficheiro de configuração para a sua aplicação que pode atualizar sem recolocar.

1. Inicie a ingestão para ver se tudo está funcionando como esperado.

1. Se o novo cluster for satisfatório, elimine o cluster original.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre [apache HBase](https://hbase.apache.org/) e atualizar clusters HDInsight, consulte os seguintes artigos:

* [Atualize um cluster HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md)
* [Monitorize e gerencie o Azure HDInsight utilizando o Apache Ambari Web UI](../hdinsight-hadoop-manage-ambari.md)
* [Componentes e versões Apache Hadoop](../hdinsight-component-versioning.md)
* [Otimizar configurações usando Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
