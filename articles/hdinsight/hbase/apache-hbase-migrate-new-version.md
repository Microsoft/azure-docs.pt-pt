---
title: Migrar um cluster HBase para uma nova versão-Azure HDInsight
description: Como migrar clusters do Apache HBase para uma versão mais recente no Azure HDInsight.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: ashishth
ms.openlocfilehash: b03bbc7aacd3bfa2a8e29296a5fafed7d4e7e37a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931534"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrar um cluster do Apache HBase para uma nova versão

Este artigo discute as etapas necessárias para atualizar o cluster do Apache HBase no Azure HDInsight para uma versão mais recente.

> [!NOTE]  
> O tempo de inatividade durante a atualização deve ser mínimo, na ordem de minutos. Esse tempo de inatividade é causado pelas etapas para liberar todos os dados na memória, em seguida, a hora de configurar e reiniciar os serviços no novo cluster. Os resultados irão variar, dependendo do número de nós, da quantidade de dados e de outras variáveis.

## <a name="review-apache-hbase-compatibility"></a>Examinar a compatibilidade do Apache HBase

Antes de atualizar o Apache HBase, verifique se as versões do HBase nos clusters de origem e de destino são compatíveis. Para obter mais informações, consulte [Apache Hadoop componentes e versões disponíveis com o HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> É altamente recomendável que você examine a matriz de compatibilidade de versão no [livro do HBase](https://hbase.apache.org/book.html#upgrading).

Aqui está um exemplo de matriz de compatibilidade de versão. Y indica compatibilidade e N indica uma possível incompatibilidade:

| Tipo de compatibilidade | Versão principal| Versão secundária | Patch |
| --- | --- | --- | --- |
| Compatibilidade de cliente-servidor de conexão | N | S | S |
| Compatibilidade de servidor-servidor | N | S | S |
| Compatibilidade de formato de arquivo | N | S | S |
| Compatibilidade de API de cliente | N | S | S |
| Compatibilidade binária de cliente | N | N | S |
| **Compatibilidade de API limitada do lado do servidor** |  |  |  |
| Estável | N | S | S |
| Constantes | N | N | S |
| Ocorrer | N | N | N |
| Compatibilidade de dependência | N | S | S |
| Compatibilidade operacional | N | N | S |

> [!NOTE]  
> Quaisquer incompatibilidades de interrupção devem ser descritas nas notas de versão de versão do HBase.

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Atualizar com a mesma versão principal do Apache HBase

Para atualizar o cluster do Apache HBase no Azure HDInsight, conclua as seguintes etapas:

1. Verifique se seu aplicativo é compatível com a nova versão, conforme mostrado na matriz de compatibilidade do HBase e nas notas de versão. Teste seu aplicativo em um cluster que executa a versão de destino do HDInsight e do HBase.

2. [Configure um novo cluster HDInsight de destino](../hdinsight-hadoop-provision-linux-clusters.md) usando a mesma conta de armazenamento, mas com um nome de contêiner diferente:

    ![Usar a mesma conta de armazenamento, mas criar um contêiner diferente](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Libere o cluster HBase de origem, que é o cluster que você está atualizando. O HBase grava dados de entrada em um repositório na memória, chamado de _memstore_. Depois que o memstore atingir um determinado tamanho, o HBase o liberará para o disco para armazenamento de longo prazo na conta de armazenamento do cluster. Ao excluir o cluster antigo, os memstores são reciclados, potencialmente perdendo dados. Para liberar manualmente o memstore de cada tabela para o disco, execute o script a seguir. A versão mais recente desse script está no [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)do Azure.

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
    
4. Interrompa a ingestão para o antigo cluster HBase.
5. Para garantir que todos os dados recentes no memstore sejam liberados, execute o script anterior novamente.
6. Entre no [Apache Ambari](https://ambari.apache.org/) no cluster antigo (https://OLDCLUSTERNAME.azurehdidnsight.net) e interrompa os serviços do HBase. Quando você receber uma solicitação para confirmar que deseja parar os serviços, marque a caixa para ativar o modo de manutenção do HBase. Para obter mais informações sobre como se conectar ao e usar o Ambari, consulte [gerenciar clusters HDInsight usando a interface do usuário da Web do amAmbari](../hdinsight-hadoop-manage-ambari.md).

    ![No Ambari, clique em serviços > HBase > parar em ações de serviço](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![Marque a caixa de seleção Ativar o modo de manutenção para HBase e, em seguida, confirme](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Entre no Ambari no novo cluster HDInsight. Altere a configuração `fs.defaultFS` HDFS para apontar para o nome do contêiner usado pelo cluster original. Essa configuração está sob o **HDFS > configurações > avançado > Advanced Core-site**.

    ![No Ambari, clique em serviços > HDFS > Configurações > avançado](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Em Ambari, altere o nome do contêiner](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **Se você não estiver usando clusters HBase com o recurso de gravações avançadas, ignore esta etapa. Ele é necessário apenas para clusters do HBase com o recurso de gravações avançadas.**
   
   Altere o caminho `hbase.rootdir` para apontar para o contêiner do cluster original.

    ![Em Ambari, altere o nome do contêiner para o HBase rootdir](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
1. Se você estiver atualizando o HDInsight 3,6 para 4,0, siga as etapas abaixo, caso contrário, pule para a etapa 10:
    1. Reinicie todos os serviços necessários no Ambari selecionando **serviços** > **reiniciar todos os necessários**.
    1. Interrompa o serviço HBase.
    1. SSH para o nó Zookeeper e execute o comando [zkCli](https://github.com/go-zkcli/zkcli) `rmr /hbase-unsecure` para remover o znode raiz do HBase de Zookeeper.
    1. Reinicie o HBase.
1. Se você estiver atualizando para qualquer outra versão do HDInsight além de 4,0, siga estas etapas:
    1. Guarde as alterações.
    1. Reinicie todos os serviços necessários conforme indicado por Ambari.
1. Aponte seu aplicativo para o novo cluster.

    > [!NOTE]  
    > O DNS estático para o aplicativo é alterado durante a atualização. Em vez de embutir em código esse DNS, você pode configurar um CNAME nas configurações de DNS do seu nome de domínio que aponta para o nome do cluster. Outra opção é usar um arquivo de configuração para o aplicativo que você pode atualizar sem reimplantação.

12. Inicie a ingestão para ver se tudo está funcionando conforme o esperado.
13. Se o novo cluster for satisfatório, exclua o cluster original.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o [Apache HBase](https://hbase.apache.org/) e atualizar os clusters HDInsight, consulte os seguintes artigos:

* [Atualizar um cluster HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md)
* [Monitorar e gerenciar o Azure HDInsight usando a interface do usuário da Web do Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Componentes e versões do Apache Hadoop](../hdinsight-component-versioning.md)
* [Otimizar as configurações usando o Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
