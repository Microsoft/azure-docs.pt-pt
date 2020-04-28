---
title: Replicação de & de backup para Apache HBase, Phoenix - Azure HDInsight
description: Configurar backup e replicação para Apache HBase e Apache Phoenix em Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: c6d33158b581bf4394a0d1bac2b277830328e110
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75495945"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Configurar cópiade segurança e replicação para Apache HBase e Apache Phoenix no HDInsight

Apache HBase suporta várias abordagens para proteger contra perda de dados:

* Copiar `hbase` a pasta
* Exportar e ntão Importar
* Tabelas de cópias
* Instantâneos
* Replicação

> [!NOTE]  
> A Apache Phoenix armazena os seus metadados em tabelas HBase, para que os metadados são apoiados quando se faz o back up das tabelas de catálogo do sistema HBase.

As seguintes secções descrevem o cenário de utilização de cada uma destas abordagens.

## <a name="copy-the-hbase-folder"></a>Copiar a pasta hbase

Com esta abordagem, copia todos os dados da Base H, sem ser capaz de selecionar um subconjunto de tabelas ou famílias de colunas. As abordagens subsequentes proporcionam um maior controlo.

O HBase em HDInsight utiliza o armazenamento predefinido selecionado ao criar o cluster, quer as bolhas de Armazenamento Azure, quer o Azure Data Lake Storage. Em qualquer dos casos, a HBase armazena os seus ficheiros de dados e metadados no seguinte caminho:

    /hbase

* Numa conta de Armazenamento `hbase` Azure, a pasta reside na raiz do recipiente de bolhas:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* No Armazenamento do Lago `hbase` De Dados Azure, a pasta reside sob o caminho raiz que especificou ao fornecer um cluster. Este caminho raiz normalmente tem uma `clusters` pasta, com uma subpasta com o nome do seu cluster HDInsight:

    ```
    /clusters/<clusterName>/hbase
    ```

Em qualquer dos `hbase` casos, a pasta contém todos os dados que o HBase descarregou para o disco, mas pode não conter os dados da memória. Antes de poder confiar nesta pasta como uma representação precisa dos dados da Base H, tem de desligar o cluster.

Depois de eliminar o cluster, pode deixar os dados no lugar ou copiar os dados para um novo local:

* Crie uma nova instância HDInsight apontando para o local de armazenamento atual. A nova instância é criada com todos os dados existentes.

* Copie `hbase` a pasta para um recipiente de blob azure diferente ou localização de armazenamento de data lake, e em seguida, inicie um novo cluster com esses dados. Para armazenamento azure, utilize [AzCopy](../../storage/common/storage-use-azcopy.md), e para armazenamento de data lake use [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportar e ntão Importar

No cluster HDInsight de origem, utilize o [utilitário Export](https://hbase.apache.org/book.html#export) (incluído com HBase) para exportar dados de uma tabela de origem para o armazenamento anexo por defeito. Em seguida, pode copiar a pasta exportada para o local de armazenamento de destino, e executar o [utilitário Import](https://hbase.apache.org/book.html#import) no cluster destino HDInsight.

Para exportar dados da tabela, primeiro SSH no nó principal do seu `hbase` cluster HDInsight fonte e, em seguida, executar o seguinte comando:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

O diretório de exportação já não deve existir. O nome da mesa é sensível ao caso.

Para importar dados da tabela, SSH no nó principal do seu `hbase` cluster HDInsight de destino e, em seguida, executar o seguinte comando:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

A mesa já deve existir.

Especifique a via de exportação completa para o armazenamento por defeito ou para qualquer uma das opções de armazenamento anexadas. Por exemplo, no Armazenamento Azure:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Em Azure Data Lake Storage Gen2, a sintaxe é:

    abfs://<containername>@<accountname>.dfs.core.windows.net/<path>

Em Azure Data Lake Storage Gen1, a sintaxe é:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Esta abordagem oferece granularidade ao nível da mesa. Também pode especificar um intervalo de data para as linhas incluirem, o que lhe permite realizar o processo de forma incremental. Cada data está em milissegundos desde a época unix.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Note que tem de especificar o número de versões de cada linha para exportar. Para incluir todas as versões `<numberOfVersions>` na gama de datas, definida para um valor superior ao máximo possível versões de linha, como 100000.

## <a name="copy-tables"></a>Tabelas de cópias

O [utilitário CopyTable](https://hbase.apache.org/book.html#copy.table) copia dados de uma tabela de origem, linha a linha, para uma tabela de destino existente com o mesmo esquema que a fonte. A tabela de destino pode estar no mesmo cluster ou num cluster HBase diferente. Os nomes das mesas são sensíveis aos casos.

Para utilizar o CopyTable dentro de um cluster, SSH no nó de `hbase` cabeça do seu cluster HDInsight de origem e, em seguida, executar este comando:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>


Para utilizar o CopyTable para copiar para uma `peer` tabela num cluster diferente, adicione o interruptor com o endereço do cluster de destino:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

O endereço de destino é composto pelas seguintes três partes:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>`é uma lista separada de nós apache zookeeper, por exemplo:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>`em HDInsight predefinidos para 2181, e `<ZnodeParent>` é, `/hbase-unsecure`assim, o completo `<destinationAddress>` seria:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Consulte manualmente a Lista de [Quorum](#manually-collect-the-apache-zookeeper-quorum-list) Do ZooKeeper Apache neste artigo para obter mais detalhes sobre como recuperar estes valores para o seu cluster HDInsight.

O utilitário CopyTable também suporta parâmetros para especificar o intervalo de tempo das linhas para copiar e especificar o subconjunto das famílias de colunas numa tabela para copiar. Para ver a lista completa de parâmetros suportados pelo CopyTable, execute o CopyTable sem quaisquer parâmetros:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

A CopyTable digitaliza todo o conteúdo da tabela de origem que será copiado para a tabela de destino. Isto pode reduzir o desempenho do seu cluster HBase enquanto o CopyTable executa.

> [!NOTE]  
> Para automatizar a cópia de dados `hdi_copy_table.sh` entre as tabelas, consulte o script no repositório [Azure HBase Utils](https://github.com/Azure/hbase-utils/tree/master/replication) no GitHub.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Recolha manualmente a Lista de Quórum Do ZooKeeper Apache

Quando ambos os clusters HDInsight estão na mesma rede virtual, como descrito anteriormente, a resolução interna de nomes de anfitrião é automática. Para utilizar o CopyTable para clusters HDInsight em duas redes virtuais separadas ligadas por um Gateway VPN, terá de fornecer os endereços IP do anfitrião dos nós do Zookeeper no quórum.

Para adquirir os nomes de anfitrião do quórum, execute o seguinte comando de caracóis:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

O comando curl recupera um documento JSON com informações de configuração HBase, e o comando grep devolve apenas a entrada "hbase.zookeeper.quorum", por exemplo:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

O valor dos nomes do anfitrião quórum é toda a corda à direita do cólon.

Para recuperar os endereços IP para estes anfitriões, utilize o seguinte comando de caracóis para cada anfitrião na lista anterior:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

Neste comando de `<zookeeperHostFullName>` caracóis, está o nome DNS completo `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`de um hospedeiro zookeeper, como o exemplo . A saída do comando contém o endereço IP para o hospedeiro especificado, por exemplo:

    100    "ip" : "10.0.0.9",

Depois de recolher os endereços IP para todos os nós do ZooKeeper no seu quórum, reconstrua o endereço de destino:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

No nosso exemplo:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Instantâneos

[As imagens](https://hbase.apache.org/book.html#ops.snapshots) permitem-lhe obter uma cópia de segurança pontual dos dados na sua loja de dados HBase. As imagens têm uma sobrecarga mínima e completam-se em segundos, porque uma operação instantânea é efetivamente uma operação de metadados que captura os nomes de todos os ficheiros armazenados nesse instante. No momento de um instantâneo, nenhum dado real é copiado. Os instantâneos baseiam-se na natureza imutável dos dados armazenados no HDFS, onde atualizações, eliminações e inserções são todos representados como novos dados. Pode restaurar *(clone)* um instantâneo no mesmo cluster, ou exportar um instantâneo para outro cluster.

Para criar um instantâneo, SSH no nó de cabeça do seu `hbase` cluster HDInsight HBase e iniciar a concha:

    hbase shell

Dentro da concha da base, utilize o comando instantâneo com os nomes da mesa e deste instantâneo:

    snapshot '<tableName>', '<snapshotName>'

Para restaurar uma imagem `hbase` instantânea pelo nome dentro da casca, primeiro desative a tabela, depois restaure o instantâneo e volte a ativar a tabela:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Para restaurar uma imagem instantânea de uma nova mesa, use clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Para exportar um instantâneo para o HDFS para utilização por outro cluster, primeiro crie o instantâneo como descrito anteriormente e, em seguida, use o utilitário ExportSnapshot. Execute esta utilidade de dentro da sessão sSH `hbase` até ao nó da cabeça, não dentro da concha:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

Pode `<hdfsHBaseLocation>` ser qualquer um dos locais de armazenamento acessíveis ao seu cluster de origem, e deve apontar para a pasta hbase utilizada pelo seu cluster de destino. Por exemplo, se tiver uma conta secundária de Armazenamento Azure anexada ao seu cluster de origem, e essa conta fornecer acesso ao recipiente utilizado pelo armazenamento padrão do cluster de destino, pode utilizar este comando:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

Após a exportação do instantâneo, sSH para o nó de cabeça do cluster de destino e restaurar o instantâneo usando o comando restore_snapshot como descrito anteriormente.

As imagens fornecem uma cópia completa de `snapshot` uma mesa no momento do comando. Os instantâneos não fornecem a capacidade de realizar instantâneos incrementais por janelas de tempo, nem especificar subconjuntos de colunas famílias para incluir no instantâneo.

## <a name="replication"></a>Replicação

[A replicação da Base Hempurra](https://hbase.apache.org/book.html#_cluster_replication) automaticamente as transações de um cluster de origem para um cluster de destino, utilizando um mecanismo assíncrono com sobrecarga mínima no cluster de origem. No HDInsight, pode configurar a replicação entre clusters onde:

* Os aglomerados de origem e destino estão na mesma rede virtual.
* Os clusters de origem e destinos estão em diferentes redes virtuais ligadas por um gateway VPN, mas ambos os clusters existem na mesma localização geográfica.
* Os clusters de aglomerados de origem e destinos estão em diferentes redes virtuais ligadas por um gateway VPN e cada cluster existe em uma localização geográfica diferente.

Os passos gerais para configurar a replicação são:

1. No aglomerado de origem, crie as tabelas e povoe os dados.
2. No cluster de destino, crie mesas de destino vazias com o esquema da mesa de origem.
3. Registe o cluster de destino como um par para o aglomerado de origem.
4. Ativar a replicação nas tabelas de origem desejadas.
5. Copie os dados existentes das tabelas de origem para as tabelas de destino.
6. A replicação copia automaticamente novas modificações de dados nas tabelas de origem nas tabelas de destino.

Para permitir a replicação no HDInsight, aplique uma Ação de Script no seu cluster HDInsight de origem corrente. Para uma passagem de habilitação à replicação no seu cluster, ou para experimentar a replicação em clusters de amostras criados em redes virtuais utilizando modelos de Gestão de Recursos Azure, consulte a replicação do [HBase De Apache Configure](apache-hbase-replication.md). Este artigo também inclui instruções para permitir a replicação dos metadados phoenix.

## <a name="next-steps"></a>Passos seguintes

* [Configure a replicação de Apache HBase](apache-hbase-replication.md)
* [Trabalhar com o Utilitário de Importação e Exportação e Importação de HBase](https://blogs.msdn.microsoft.com/data_otaku/2016/12/21/working-with-the-hbase-import-and-export-utility/)
