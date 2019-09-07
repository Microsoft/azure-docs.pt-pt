---
title: Backup e replicação para Apache HBase e Apache Phoenix no Azure HDInsight
description: Configure o backup e a replicação para HBase e Phoenix.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: a1b6e850fab5e9b9b651de9d02ee7981d71a343c
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735901"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Configurar o backup e a replicação para o Apache HBase e o Apache Phoenix no HDInsight

O Apache HBase dá suporte a várias abordagens para proteger contra perda de dados:

* Copiar a `hbase` pasta
* Exportar e importar
* Copiar tabelas
* Instantâneos
* Replicação

> [!NOTE]  
> O Apache Phoenix armazena seus metadados em tabelas HBase, para que seja feito backup dos metadados ao fazer backup das tabelas do catálogo do sistema HBase.

As seções a seguir descrevem o cenário de uso para cada uma dessas abordagens.

## <a name="copy-the-hbase-folder"></a>Copiar a pasta HBase

Com essa abordagem, você copia todos os dados do HBase, sem ser capaz de selecionar um subconjunto de tabelas ou famílias de colunas. As abordagens subsequentes fornecem maior controle.

O HBase no HDInsight usa o armazenamento padrão selecionado ao criar o cluster, os blobs de armazenamento do Azure ou o Azure Data Lake Storage. Em ambos os casos, o HBase armazena seus arquivos de dados e metadados no seguinte caminho:

    /hbase

* Em uma conta de armazenamento do `hbase` Azure, a pasta reside na raiz do contêiner de BLOBs:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* No Azure data Lake Storage a `hbase` pasta reside no caminho raiz que você especificou ao provisionar um cluster. Esse caminho raiz normalmente tem uma `clusters` pasta, com uma subpasta nomeada após o cluster HDInsight:

    ```
    /clusters/<clusterName>/hbase
    ```

Em ambos os casos, `hbase` a pasta contém todos os dados que o HBase liberou para o disco, mas pode não conter os dados na memória. Para que você possa contar com essa pasta como uma representação precisa dos dados do HBase, você deve desligar o cluster.

Depois de excluir o cluster, você pode deixar os dados em vigor ou copiar os dados para um novo local:

* Crie uma nova instância do HDInsight apontando para o local de armazenamento atual. A nova instância é criada com todos os dados existentes.

* Copie a `hbase` pasta para um contêiner de blob de armazenamento do Azure diferente ou data Lake Storage local e, em seguida, inicie um novo cluster com esses dados. Para o armazenamento do Azure, use [AzCopy](../../storage/common/storage-use-azcopy.md)e para data Lake Storage use [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportar e importar

No cluster HDInsight de origem, use o utilitário de exportação (incluído com o HBase) para exportar dados de uma tabela de origem para o armazenamento anexado padrão. Em seguida, você pode copiar a pasta exportada para o local de armazenamento de destino e executar o utilitário de importação no cluster de destino do HDInsight.

Para exportar uma tabela, primeiro SSH para o nó principal do seu cluster HDInsight de origem e, em seguida `hbase` , execute o seguinte comando:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Para importar uma tabela, use ssh no nó principal do cluster HDInsight de destino e, em seguida, `hbase` execute o seguinte comando:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Especifique o caminho de exportação completo para o armazenamento padrão ou para qualquer uma das opções de armazenamento anexadas. Por exemplo, no armazenamento do Azure:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

No Azure Data Lake Storage Gen2, a sintaxe é:

    abfs://<containername>@<accountname>.dfs.core.windows.net/<path>

No Azure Data Lake Storage Gen1, a sintaxe é:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Essa abordagem oferece granularidade em nível de tabela. Você também pode especificar um intervalo de datas para as linhas a serem incluídas, o que permite que você execute o processo de forma incremental. Cada data é em milissegundos desde a época do UNIX.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Observe que você precisa especificar o número de versões de cada linha para exportar. Para incluir todas as versões no intervalo de datas, `<numberOfVersions>` defina como um valor maior que o máximo possível de versões de linha, como 100000.

## <a name="copy-tables"></a>Copiar tabelas

O utilitário copyTable copia dados de uma tabela de origem, linha por linha, para uma tabela de destino existente com o mesmo esquema que a origem. A tabela de destino pode estar no mesmo cluster ou em um cluster HBase diferente.

Para usar copiartable em um cluster, use ssh no nó principal do cluster HDInsight de origem e execute este `hbase` comando:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

Para usar copiartable para copiar para uma tabela em um cluster diferente, adicione a `peer` opção com o endereço do cluster de destino:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

O endereço de destino é composto pelas três partes a seguir:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>`é uma lista separada por vírgulas de nós de Apache ZooKeeper, por exemplo:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>`no HDInsight, o padrão é 2181 e `<ZnodeParent>` é `/hbase-unsecure`, portanto, o `<destinationAddress>` completo seria:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Consulte [coletar manualmente a lista de Apache ZooKeeper quorum](#manually-collect-the-apache-zookeeper-quorum-list) neste artigo para obter detalhes sobre como recuperar esses valores para o cluster HDInsight.

O utilitário copyTable também dá suporte a parâmetros para especificar o intervalo de tempo de linhas a serem copiadas e para especificar o subconjunto de famílias de colunas em uma tabela a ser copiada. Para ver a lista completa de parâmetros com suporte de copyTable, execute copyTable sem nenhum parâmetro:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

O copyTable examina todo o conteúdo da tabela de origem que será copiado para a tabela de destino. Isso pode reduzir o desempenho do cluster HBase enquanto a copyTable é executada.

> [!NOTE]  
> Para automatizar a cópia de dados entre tabelas, consulte o `hdi_copy_table.sh` script no repositório do [Azure HBase utils](https://github.com/Azure/hbase-utils/tree/master/replication) no github.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Coletar manualmente a lista de quorum Apache ZooKeeper

Quando ambos os clusters HDInsight estão na mesma rede virtual, conforme descrito anteriormente, a resolução de nome de host interno é automática. Para usar Copiartable para clusters HDInsight em duas redes virtuais separadas conectadas por um gateway de VPN, você precisará fornecer os endereços IP do host dos nós Zookeeper no quorum.

Para adquirir os nomes de host de quorum, execute o seguinte comando de ondulação:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

O comando de ondulação recupera um documento JSON com informações de configuração do HBase e o comando grep retorna apenas a entrada "HBase. Zookeeper. quorum", por exemplo:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

O valor dos nomes de host de quorum é a cadeia de caracteres inteira à direita dos dois-pontos.

Para recuperar os endereços IP para esses hosts, use o seguinte comando de rotação para cada host na lista anterior:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

Nesse comando de rotação, `<zookeeperHostFullName>` é o nome DNS completo de um host ZooKeeper, como o exemplo. `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net` A saída do comando contém o endereço IP para o host especificado, por exemplo:

    100    "ip" : "10.0.0.9",

Depois de coletar os endereços IP para todos os nós ZooKeeper em seu quorum, recompile o endereço de destino:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

Em nosso exemplo:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Instantâneos

Os instantâneos permitem que você faça um backup pontual dos dados em seu armazenamento do HBase. Os instantâneos têm sobrecarga mínima e são concluídos em segundos, porque uma operação de instantâneo é efetivamente uma operação de metadados capturando os nomes de todos os arquivos no armazenamento naquele instante. No momento de um instantâneo, nenhum dado real é copiado. Os instantâneos dependem da natureza imutável dos dados armazenados no HDFS, onde as atualizações, as exclusões e as inserções são representadas como novos dados. Você pode restaurar (*clonar*) um instantâneo no mesmo cluster ou exportar um instantâneo para outro cluster.

Para criar um instantâneo, execute o ssh no nó principal do cluster do HBase do HDInsight e inicie `hbase` o Shell:

    hbase shell

No Shell do HBase, use o comando de instantâneo com os nomes da tabela e deste instantâneo:

    snapshot '<tableName>', '<snapshotName>'

Para restaurar um instantâneo por nome no `hbase` Shell, primeiro desabilite a tabela e, em seguida, restaure o instantâneo e habilite novamente a tabela:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Para restaurar um instantâneo para uma nova tabela, use clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Para exportar um instantâneo para o HDFS para uso por outro cluster, primeiro crie o instantâneo conforme descrito anteriormente e, em seguida, use o utilitário ExportSnapshot. Execute este utilitário de dentro da sessão SSH para o nó principal, não dentro do `hbase` Shell:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

O `<hdfsHBaseLocation>` pode ser qualquer um dos locais de armazenamento acessíveis ao seu cluster de origem e deve apontar para a pasta HBase usada pelo cluster de destino. Por exemplo, se você tiver uma conta de armazenamento do Azure secundária anexada ao seu cluster de origem e essa conta fornecer acesso ao contêiner usado pelo armazenamento padrão do cluster de destino, você poderá usar este comando:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

Depois que o instantâneo for exportado, use o SSH no nó principal do cluster de destino e restaure o instantâneo usando o comando restore_snapshot conforme descrito anteriormente.

Os instantâneos fornecem um backup completo de uma tabela no momento do `snapshot` comando. Os instantâneos não fornecem a capacidade de executar instantâneos incrementais por janelas de tempo, nem para especificar subconjuntos de famílias de colunas a serem incluídas no instantâneo.

## <a name="replication"></a>Replicação

A replicação do HBase envia automaticamente as transações de um cluster de origem para um cluster de destino, usando um mecanismo assíncrono com sobrecarga mínima no cluster de origem. No HDInsight, você pode configurar a replicação entre clusters em que:

* Os clusters de origem e de destino estão na mesma rede virtual.
* Os clusters de origem e destinos estão em redes virtuais diferentes conectadas por um gateway de VPN, mas ambos os clusters existem na mesma localização geográfica.
* O cluster de origem e os clusters de destinos estão em redes virtuais diferentes conectadas por um gateway de VPN e cada cluster existe em uma localização geográfica diferente.

As etapas gerais para configurar a replicação são:

1. No cluster de origem, crie as tabelas e preencha os dados.
2. No cluster de destino, crie tabelas de destino vazias com o esquema da tabela de origem.
3. Registre o cluster de destino como um ponto para o cluster de origem.
4. Habilite a replicação nas tabelas de origem desejadas.
5. Copie os dados existentes das tabelas de origem para as tabelas de destino.
6. A replicação copia automaticamente novas modificações de dados para as tabelas de origem nas tabelas de destino.

Para habilitar a replicação no HDInsight, aplique uma ação de script ao cluster HDInsight de origem em execução. Para obter uma explicação de como habilitar a replicação em seu cluster ou fazer experiências com a replicação em clusters de exemplo criados em redes virtuais usando modelos de gerenciamento de recursos do Azure, consulte [Configurar a replicação do Apache HBase](apache-hbase-replication.md). Esse artigo também inclui instruções para habilitar a replicação de metadados Phoenix.

## <a name="next-steps"></a>Passos seguintes

* [Configurar a replicação do Apache HBase](apache-hbase-replication.md)
