---
title: Configurar a cópia de segurança do Apache HBase e o Apache Phoenix e replicação - Azure HDInsight
description: Configure a cópia de segurança e replicação HBase e Phoenix.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: e60aef7b1848197f41f96a1b5f5414bb0c8f4a15
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64696384"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Configurar a cópia de segurança e a replicação para o Apache HBase e o Apache Phoenix no HDInsight

O Apache HBase suporta várias abordagens para proteção contra perda de dados:

* Copiar o `hbase` pasta
* Exportar, em seguida, importar
* Copiar tabelas
* Instantâneos
* Replicação

> [!NOTE]  
> O Apache Phoenix armazena seus metadados nas tabelas de HBase, para que os metadados de cópia de segurança quando copia em segurança as tabelas de catálogo do sistema de HBase.

As secções seguintes descrevem o cenário de utilização para cada uma dessas abordagens.

## <a name="copy-the-hbase-folder"></a>Copie a pasta de hbase

Com esta abordagem, copiar todos os dados do HBase, sem a capacidade de selecionar um subconjunto de tabelas ou famílias de colunas. Abordagens subseqüentes fornecem maior controle.

HBase no HDInsight utiliza o armazenamento de predefinido selecionado quando criar o cluster, blobs de armazenamento do Azure ou de armazenamento do Azure Data Lake. Em ambos os casos, o HBase armazena seus arquivos de dados e metadados no caminho seguinte:

    /hbase

* Numa conta de armazenamento do Azure a `hbase` pasta reside na raiz do contentor de BLOBs:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* No armazenamento do Azure Data Lake o `hbase` pasta reside no caminho de raiz que especificou quando aprovisionar um cluster. Este caminho de raiz normalmente tem um `clusters` pasta, com uma subpasta com o nome depois do cluster do HDInsight:

    ```
    /clusters/<clusterName>/hbase
    ```

Em ambos os casos, o `hbase` pasta contém todos os dados que HBase tem liberadas para o disco, mas não pode conter os dados na memória. Também pode utilizar esta pasta como uma representação precisa dos dados do HBase, tem de encerrar o cluster.

Depois de eliminar o cluster, pode deixar os dados no local ou copiar os dados para uma nova localização:

* Crie uma nova instância de HDInsight apontando para a localização de armazenamento atual. A nova instância é criada com todos os dados existentes.

* Copiar o `hbase` pasta para um armazenamento do Azure diferente BLOBs contentor ou localização de armazenamento do Data Lake e, em seguida, inicie um novo cluster com os dados. Armazenamento do Azure, utilize [AzCopy](../../storage/common/storage-use-azcopy.md)e para utilização do armazenamento do Data Lake [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportar, em seguida, importar

No cluster de HDInsight origem, utilize o utilitário de exportação (incluído com o HBase) para exportar dados de uma tabela de origem para o armazenamento predefinido anexado. Pode, em seguida, copie a pasta exportada para a localização de armazenamento de destino e execute o utilitário de importação no cluster do HDInsight de destino.

Para exportar uma tabela, o SSH primeiro para o nó principal do cluster do HDInsight de origem e, em seguida, execute o seguinte `hbase` comando:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Para importar uma tabela, o SSH para o nó principal do cluster do HDInsight de destino e, em seguida, execute o seguinte `hbase` comando:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Especifique o caminho de exportação completa para o armazenamento predefinido ou para qualquer uma das opções de armazenamento ligado. Por exemplo, no armazenamento do Azure:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

No ger2 de armazenamento do Azure Data Lake, a sintaxe é:

    abfs://<containername>@<accountname>.dfs.core.windows.net/<path>

Na Gen1 de armazenamento do Azure Data Lake, a sintaxe é:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Essa abordagem oferece a granularidade do nível de tabela. Também pode especificar um intervalo de datas para as linhas incluir, que permite efetuar o processo de forma incremental. Cada data é em milissegundos desde a época de Unix.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Tenha em atenção que tem de especificar o número de versões de cada linha para exportar. Para incluir todas as versões no intervalo de datas, defina `<numberOfVersions>` para um valor maior do que as versões de máximo possível de linha, como 100000.

## <a name="copy-tables"></a>Copiar tabelas

O utilitário de CopyTable copia dados de uma tabela de origem, linha por linha, para uma tabela de destino existente com o mesmo esquema de origem. A tabela de destino pode estar no mesmo cluster ou outro cluster HBase.

Para utilizar CopyTable dentro de um cluster, o SSH para o nó principal do cluster do HDInsight de origem e, em seguida, executar isso `hbase` comando:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

Para utilizar CopyTable para copiar para uma tabela num cluster diferente, adicione o `peer` mudar com o endereço do cluster de destino:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

O endereço de destino é composto por três partes seguintes:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>` é uma lista separada por vírgulas de nós do Apache ZooKeeper, por exemplo:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` nas predefinições do HDInsight para 2181, e `<ZnodeParent>` é `/hbase-unsecure`, por isso, todo `<destinationAddress>` seria:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Ver [recolher manualmente a lista de quórum do Apache ZooKeeper](#manually-collect-the-apache-zookeeper-quorum-list) neste artigo para obter detalhes sobre como obter estes valores para o seu cluster do HDInsight.

O utilitário de CopyTable também oferece suporte a parâmetros para especificar o intervalo de tempo de linhas para copiar e para especificar o subconjunto de famílias de colunas numa tabela para copiar. Para ver a lista completa de parâmetros que têm suporte CopyTable, execute CopyTable sem quaisquer parâmetros:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable analisa o conteúdo da tabela de origem inteira que sejam copiado para a tabela de destino. Isso pode reduzir o desempenho do seu cluster HBase enquanto executa CopyTable.

> [!NOTE]  
> Para automatizar a cópia dos dados entre as tabelas, veja a `hdi_copy_table.sh` script no [Utils do Azure HBase](https://github.com/Azure/hbase-utils/tree/master/replication) repositório no GitHub.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Recolher manualmente o lista de quórum do Apache ZooKeeper

Quando ambos os clusters HDInsight estão na mesma rede virtual, conforme descrito anteriormente, resolução de nomes de anfitrião interno é automática. Para utilizar CopyTable para clusters do HDInsight em duas redes virtuais separados ligados por um Gateway de VPN, terá de fornecer ao host endereços IP de nós do Zookeeper no quórum.

Para obter os nomes de anfitrião do quórum, execute o comando curl seguinte:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

O comando curl obtém um documento JSON com informações de configuração de HBase e o comando de grep retorna apenas a entrada de "hbase.zookeeper.quorum", por exemplo:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

O valor de nomes de anfitrião do quórum é toda a cadeia à direita dos dois pontos.

Para obter os endereços IP para estes anfitriões, utilize o comando curl seguinte para cada anfitrião na lista anterior:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

Neste comando curl, `<zookeeperHostFullName>` é o nome DNS completo de um anfitrião ZooKeeper, como o exemplo `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. A saída do comando contém o endereço IP para o anfitrião especificado, por exemplo:

    100    "ip" : "10.0.0.9",

Depois de recolher os endereços IP para todos os nós do ZooKeeper no seu quórum, recrie o endereço de destino:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

No nosso exemplo:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Instantâneos

Instantâneos permitem-lhe efetuar um ponto anterior no tempo cópia de segurança de dados no seu arquivo de dados do HBase. Instantâneos têm overhead mínimo e concluídas em segundos, porque uma operação de instantâneo é efetivamente uma operação de metadados, captura os nomes de todos os ficheiros no armazenamento nesse instante. No momento de um instantâneo, não existem dados reais são copiados. Os instantâneos baseiam-se sobre a natureza imutável dos dados armazenados no HDFS, onde as atualizações, eliminações e inserções são todos representadas como novos dados. Pode restaurar (*clone*) um instantâneo no mesmo cluster ou exportar um instantâneo para outro cluster.

Para criar um instantâneo, o SSH para o nó principal do HBase do HDInsight cluster e iniciar o `hbase` shell:

    hbase shell

Dentro do shell de hbase, utilize o comando de instantâneo com os nomes da tabela e deste instantâneo:

    snapshot '<tableName>', '<snapshotName>'

Para restaurar um instantâneo por nome dentro do `hbase` shell, primeiro desativar a tabela, em seguida, restaurar o instantâneo e volte a ativar a tabela:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Para restaurar um instantâneo para uma nova tabela, utilize clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Para exportar um instantâneo para HDFS para utilização por outro cluster, primeiro de criar o instantâneo, tal como descrito anteriormente e, em seguida, utilize o utilitário de ExportSnapshot. Executar este utilitário de dentro da sessão SSH ao nó principal, não dentro do `hbase` shell:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

O `<hdfsHBaseLocation>` pode ser a qualquer uma das localizações de armazenamento acessível ao seu cluster de origem e deve apontar para a pasta de hbase utilizada pelo seu cluster de destino. Por exemplo, se tiver uma conta de armazenamento do Azure secundária, anexada ao seu cluster de origem, e essa conta fornece acesso ao contentor utilizado pelo armazenamento predefinido do cluster de destino, poderia usar este comando:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

Depois do instantâneo é exportado, comando SSH para o nó principal do cluster de destino e restaurar o instantâneo utilizando o restore_snapshot conforme descrito anteriormente.

Instantâneos de fornecem um backup completo de uma tabela no momento do `snapshot` comando. Os instantâneos não fornecem a capacidade para executar instantâneos incrementais pelo windows de tempo, nem para especificar a subconjuntos de famílias de colunas a incluir no instantâneo.

## <a name="replication"></a>Replicação

Replicação do HBase envia automaticamente transações de um cluster de origem para um cluster de destino, com um mecanismo assíncrono com sobrecarga mínima no cluster de origem. No HDInsight, pode configurar a replicação entre clusters em que:

* Os clusters de origem e de destino estão na mesma rede virtual.
* Os clusters de origem e os destinos estão em diferentes redes virtuais ligadas por um gateway VPN, mas ambos os clusters de existir na mesma localização geográfica.
* Os clusters de destinos e o cluster de origem estão em diferentes redes virtuais ligadas por um gateway VPN e cada cluster existe num local geográfico diferente.

Os passos gerais para configurar a replicação são:

1. No cluster de origem, criar as tabelas e preencher os dados.
2. No cluster de destino, crie tabelas de destino vazia com o esquema da tabela de origem.
3. Registre-se o cluster de destino como um elemento de rede para o cluster de origem.
4. Ative a replicação nas tabelas de origem pretendido.
5. Copie dados existentes de tabelas de origem para as tabelas de destino.
6. Replicação copia automaticamente o novo modificações de dados para as tabelas de origem para as tabelas de destino.

Para ativar a replicação no HDInsight, aplicam-se uma ação de Script para o seu cluster do HDInsight de origem em execução. Para obter instruções de ativação da replicação no seu cluster ou para fazer experiências com replicação em clusters de exemplo criados nas redes virtuais através de modelos do Azure Resource Management, consulte [replicação de configurar o Apache HBase](apache-hbase-replication.md). Esse artigo também inclui instruções para ativar a replicação dos metadados de Phoenix.

## <a name="next-steps"></a>Passos Seguintes

* [Configurar a replicação do HBase do Apache](apache-hbase-replication.md)
