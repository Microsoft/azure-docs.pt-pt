---
title: Migrar um cluster do HBase para uma nova versão - Azure HDInsight
description: Como migrar HBase clusters para uma nova versão.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: ashishth
ms.openlocfilehash: a152b815daeefa4c199af9b159eee8e5783971e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65143326"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrar um cluster do Apache HBase para uma nova versão

Este artigo aborda os passos necessários para atualizar o seu cluster do Apache HBase no HDInsight do Azure para uma versão mais recente.

> [!NOTE]  
> O período de indisponibilidade durante a atualização deve ser um mínimo, na ordem de minutos. Este período de indisponibilidade é causado pelos passos para liberar todos os dados na memória, em seguida, o tempo para configurar e reinicie os serviços no novo cluster. Os resultados variam, dependendo do número de nós, a quantidade de dados e outras variáveis.

## <a name="review-apache-hbase-compatibility"></a>Rever a compatibilidade do Apache HBase

Antes de atualizar o Apache HBase, certifique-se de que as versões de HBase nos clusters de origem e de destino são compatíveis. Para obter mais informações, consulte [Apache Hadoop componentes e versões disponíveis com o HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Recomendamos vivamente que reveja a matriz de compatibilidade da versão no [livro de HBase](https://hbase.apache.org/book.html#upgrading).

Aqui está uma matriz de compatibilidade da versão de exemplo. Y indica a compatibilidade e N indica uma incompatibilidade potencial:

| Tipo de compatibilidade | Versão principal| Versão secundária | Patch |
| --- | --- | --- | --- |
| Compatibilidade de durante a transmissão de cliente-servidor | N | S | S |
| Compatibilidade de-servidor | N | S | S |
| Compatibilidade de formato de ficheiro | N | S | S |
| Compatibilidade de API do cliente | N | S | S |
| Compatibilidade de binária do cliente | N | N | S |
| **Lado do servidor limitado de compatibilidade da API** |  |  |  |
| Estável | N | S | S |
| Em evolução | N | N | S |
| Instável | N | N | N |
| Compatibilidade de dependência | N | S | S |
| Compatibilidade operacional | N | N | S |

> [!NOTE]  
> Qualquer incompatibilidades de última hora devem ser descritas nas notas de versão da versão de HBase.

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Atualizar com a mesma versão principal do Apache HBase

Para atualizar o seu cluster do Apache HBase no HDInsight do Azure, conclua os seguintes passos:

1. Certifique-se de que seu aplicativo é compatível com a nova versão, como mostra as notas de versão de matriz e de compatibilidade do HBase. Teste seu aplicativo num cluster a executar a versão de destino do HDInsight e o HBase.

2. [Configurar um novo cluster de HDInsight destino](../hdinsight-hadoop-provision-linux-clusters.md) com a mesma conta de armazenamento, mas com um nome de contentor diferente:

    ![Utilizar a mesma conta de armazenamento, mas criar um contentor diferente](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Esvaziar o seu cluster de HBase de origem, o que é o cluster que estiver a atualizar. HBase escreve dados de entrada para um arquivo de dentro da memória, chamado um _memstore_. Depois do memstore atinge um determinado tamanho, o HBase libera-la no disco para armazenamento a longo prazo na conta de armazenamento do cluster. Ao eliminar o cluster antigo, os memstores são reciclados, possivelmente perda de dados. Liberasse manualmente o memstore para cada tabela para o disco, execute o seguinte script. A versão mais recente deste script é do Azure [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

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
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
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
    
4. Pare ingestão para o cluster HBase antigo.
5. Para garantir que todos os dados recentes do memstore é descarregado, execute novamente o script anterior.
6. Inicie sessão no [Apache Ambari](https://ambari.apache.org/) no cluster antigo (https://OLDCLUSTERNAME.azurehdidnsight.net) e parar os serviços de HBase. Quando lhe for pedido para confirmar que pretende parar os serviços, marque a caixa para ativar o modo de manutenção para o HBase. Para obter mais informações sobre a ligação e com o Ambari, consulte [clusters do HDInsight gerir com a IU da Web de Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![No Ambari, clique em serviços > HBase > parar em ações de serviço](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Verifique o ativar no modo de manutenção para a caixa de verificação do HBase, em seguida, confirmar](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Inicie sessão no Ambari no novo cluster do HDInsight. Alterar o `fs.defaultFS` definição do HDFS para apontar para o nome do contentor utilizado pelo original cluster. Esta definição está sob **HDFS > configurações > Avançadas > Avançadas de site principal**.

    ![No Ambari, clique em serviços > HDFS > configurações > Avançadas](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Na Ambari, altere o nome de contentor](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **Se não estiver a utilizar clusters do HBase com a funcionalidade avançada escreve, ignore este passo. É necessário apenas para clusters do HBase com funcionalidade avançada escreve.**
   
   Alterar o `hbase.rootdir` caminho para apontar para o contentor do original cluster.

    ![Na Ambari, altere o nome de contentor para rootdir de HBase](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
1. Se estiver a atualizar o HDInsight 3.6 4.0, siga os passos abaixo, caso contrário, avance para o passo 10:
    1. Reiniciar todos os serviços necessários no Ambari selecionando **serviços** > **reiniciar necessário todos os**.
    1. Pare o serviço de HBase.
    1. SSH para o nó do Zookeeper e executar o [zkCli](https://github.com/go-zkcli/zkcli) comando `rmr /hbase-unsecure` para remover o znode de raiz do HBase do Zookeeper.
    1. Reinicie o HBase.
1. Se estiver a atualizar para qualquer outra versão do HDInsight além 4.0, siga estes passos:
    1. Guarde as alterações.
    1. Reinicie todos os serviços necessários, conforme indicado pelo Ambari.
1. Ponto de seu aplicativo para o novo cluster.

    > [!NOTE]  
    > O DNS estático para que as alterações de aplicação ao atualizar. Em vez de embutir no código este DNS, pode configurar um CNAME nas definições do seu nome de domínio DNS que aponta para o nome do cluster. Outra opção é usar um arquivo de configuração para a sua aplicação que pode atualizar sem necessidade de reimplementação.

12. Inicie a ingestão para ver se tudo o que está a funcionar conforme esperado.
13. Se o novo cluster for satisfatório, elimine o cluster original.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre [Apache HBase](https://hbase.apache.org/) e atualizar clusters do HDInsight, veja os artigos seguintes:

* [Atualizar um cluster do HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md)
* [Monitorizar e gerir o HDInsight do Azure com a IU do Apache Ambari Web](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop componentes e versões](../hdinsight-component-versioning.md)
* [Otimizar as configurações com o Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
