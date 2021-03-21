---
title: Cópia de segurança & replicação para Apache HBase, Phoenix - Azure HDInsight
description: Configurar backup e replicação para Apache HBase e Apache Phoenix em Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 1d5bcf9c04ad02eaf297f8971aa0f4ff599888c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942999"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Configurar backup e replicação para Apache HBase e Apache Phoenix em HDInsight

Apache HBase suporta várias abordagens para proteger contra a perda de dados:

* Copiar a `hbase` pasta
* Exportar então Importar
* Tabelas de cópias
* Instantâneos
* Replicação

> [!NOTE]  
> A Apache Phoenix armazena os seus metadados em tabelas HBase, para que os metadados seja apoiado quando fizer o back up nas tabelas de catálogos do sistema HBase.

As secções seguintes descrevem o cenário de utilização de cada uma destas abordagens.

## <a name="copy-the-hbase-folder"></a>Copiar a pasta hbase

Com esta abordagem, você copia todos os dados HBase, sem ser capaz de selecionar um subconjunto de tabelas ou famílias de colunas. As abordagens subsequentes proporcionam um maior controlo.

O HBase em HDInsight utiliza o armazenamento predefinido selecionado ao criar o cluster, quer as bolhas de armazenamento Azure, quer o Armazenamento de Dados Azure. Em qualquer dos casos, a HBase armazena os seus ficheiros de dados e metadados no seguinte caminho:

`/hbase`

* Numa conta de Armazenamento Azure, a `hbase` pasta reside na raiz do recipiente blob:

  `wasbs://<containername>@<accountname>.blob.core.windows.net/hbase`

* No Azure Data Lake Storage, a `hbase` pasta reside sob o caminho raiz especificado ao agrupar um cluster. Este caminho de raiz tem tipicamente uma `clusters` pasta, com uma subpasta nomeada em homenagem ao seu cluster HDInsight:

  `/clusters/<clusterName>/hbase`

Em qualquer dos casos, a `hbase` pasta contém todos os dados que o HBase descarregou para o disco, mas pode não conter os dados de memória. Antes de poder confiar nesta pasta como uma representação precisa dos dados HBase, tem de desligar o cluster.

Depois de eliminar o cluster, pode deixar os dados no lugar ou copiar os dados para um novo local:

* Crie um novo exemplo hdInsight que indique a localização de armazenamento atual. A nova instância é criada com todos os dados existentes.

* Copie a `hbase` pasta para um recipiente de blob de armazenamento Azure ou para o armazenamento do Data Lake e, em seguida, inicie um novo cluster com esses dados. Para armazenamento Azure, utilize [a AzCopy,](../../storage/common/storage-use-azcopy-v10.md)e para o armazenamento de data lake use [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportar então Importar

No cluster de origem HDInsight, utilize o [utilitário Export](https://hbase.apache.org/book.html#export) (incluído com HBase) para exportar dados de uma tabela de origem para o armazenamento em anexo predefinido. Em seguida, pode copiar a pasta exportada para o local de armazenamento do destino e executar o [utilitário Import](https://hbase.apache.org/book.html#import) no cluster de destino HDInsight.

Para exportar dados de tabela, primeiro o SSH no nó de cabeça do seu cluster hdinsight de origem e, em seguida, executar o seguinte `hbase` comando:

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"
```

O diretório de exportação já não deve existir. O nome da mesa é sensível a maiôs.

Para importar dados de tabela, SSH no nó de cabeça do seu cluster de destino HDInsight e, em seguida, executar o seguinte `hbase` comando:

```console
hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"
```

A mesa já deve existir.

Especifique a trajetória de exportação completa para o armazenamento predefinido ou para qualquer uma das opções de armazenamento anexadas. Por exemplo, no Armazenamento Azure:

`wasbs://<containername>@<accountname>.blob.core.windows.net/<path>`

No Azure Data Lake Storage Gen2, a sintaxe é:

`abfs://<containername>@<accountname>.dfs.core.windows.net/<path>`

No Azure Data Lake Storage Gen1, a sintaxe é:

`adl://<accountName>.azuredatalakestore.net:443/<path>`

Esta abordagem oferece granularidade ao nível da mesa. Também pode especificar um intervalo de datas para as linhas a incluir, o que lhe permite realizar o processo de forma incremental. Cada data está em milissegundos desde a época Unix.

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>
```

Note que tem de especificar o número de versões de cada linha para exportar. Para incluir todas as versões na gama de datas, definir `<numberOfVersions>` um valor superior ao seu máximo possível versões de linha, como 100000.

## <a name="copy-tables"></a>Tabelas de cópias

O [utilitário CopyTable copia](https://hbase.apache.org/book.html#copy.table) os dados de uma tabela de origem, fila a linha, para uma tabela de destino existente com o mesmo esquema que a fonte. A tabela de destino pode estar no mesmo cluster ou num cluster HBase diferente. Os nomes das mesas são sensíveis a casos.

Para utilizar copyTable dentro de um cluster, SSH no nó de cabeça do seu cluster hdinsight de origem e, em seguida, executar este `hbase` comando:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>
```

Para utilizar copyTable para copiar para uma tabela num cluster diferente, adicione o `peer` interruptor com o endereço do cluster de destino:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>
```

O endereço de destino é composto pelas seguintes três partes:

`<destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>`

* `<ZooKeeperQuorum>` é uma lista separada por vírgulas de nóns Apache ZooKeeper, por exemplo:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` em HDInsight predefinições para 2181, e `<ZnodeParent>` `/hbase-unsecure` é, assim o completo `<destinationAddress>` seria:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Consulte [manualmente a Lista de Quoorum Apache ZooKeeper](#manually-collect-the-apache-zookeeper-quorum-list) neste artigo para obter mais detalhes sobre como recuperar estes valores para o seu cluster HDInsight.

O utilitário CopyTable também suporta parâmetros para especificar o intervalo de tempo das linhas a copiar e especificar o subconjunto de famílias de colunas numa tabela para copiar. Para ver a lista completa de parâmetros suportados pelo CopyTable, execute CopyTable sem parâmetros:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable
```

CopyTable digitaliza todo o conteúdo da tabela de origem que será copiado para a tabela de destino. Isto pode reduzir o desempenho do seu cluster HBase enquanto o CopyTable executa.

> [!NOTE]  
> Para automatizar a cópia de dados entre tabelas, consulte o `hdi_copy_table.sh` script no [repositório Azure HBase Utils](https://github.com/Azure/hbase-utils/tree/master/replication) no GitHub.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Recolha manualmente a Lista de quórum apache zookeeper

Quando ambos os clusters HDInsight estão na mesma rede virtual, como descrito anteriormente, a resolução interna do nome do anfitrião é automática. Para utilizar copyTable para clusters HDInsight em duas redes virtuais separadas ligadas por um Gateway VPN, você precisará fornecer os endereços IP hospedeiros dos nós zookeeper no quórum.

Para adquirir os nomes dos anfitriões do quórum, executar o seguinte comando curl:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"
```

O comando curl recupera um documento JSON com informações de configuração HBase, e o comando grep devolve apenas a entrada "hbase.zookeeper.qurum", por exemplo:

```output
"hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"
```

O valor dos nomes dos anfitriões do quórum é toda a corda à direita do cólon.

Para recuperar os endereços IP para estes anfitriões, utilize o seguinte comando curl para cada anfitrião na lista anterior:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"
```

Neste comando curl, `<zookeeperHostFullName>` está o nome DNS completo de um anfitrião ZooKeeper, como o exemplo `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net` . A saída do comando contém o endereço IP para o anfitrião especificado, por exemplo:

`100    "ip" : "10.0.0.9",`

Depois de recolher os endereços IP para todos os nós do ZooKeeper no seu quórum, reconstroem o endereço de destino:

`<destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>`

No nosso exemplo:

`<destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure`

## <a name="snapshots"></a>Instantâneos

[As imagens](https://hbase.apache.org/book.html#ops.snapshots) permitem-lhe obter uma cópia de segurança pontual dos dados na sua loja de dados HBase. As imagens têm uma sobrecarga mínima e completam-se em segundos, porque uma operação instantânea é efetivamente uma operação de metadados que captura os nomes de todos os ficheiros armazenados nesse instante. No momento de uma foto, nenhum dado real é copiado. As imagens baseiam-se na natureza imutável dos dados armazenados em HDFS, onde as atualizações, eliminações e inserções são todos representados como novos dados. Você pode restaurar *(clone)* uma imagem instantânea no mesmo cluster, ou exportar um instantâneo para outro cluster.

Para criar uma imagem instantânea, SSH no nó de cabeça do seu cluster HDInsight HBase e iniciar a `hbase` concha:

```console
hbase shell
```

Dentro da concha hbase, use o comando instantâneo com os nomes da tabela e deste instantâneo:

```console
snapshot '<tableName>', '<snapshotName>'
```

Para restaurar um instantâneo pelo nome dentro da `hbase` concha, primeiro desative a tabela, depois restaure o instantâneo e reative a tabela:

```console
disable '<tableName>'
restore_snapshot '<snapshotName>'
enable '<tableName>'
```

Para restaurar uma imagem instantânea para uma nova mesa, use clone_snapshot:

```console
clone_snapshot '<snapshotName>', '<newTableName>'
```

Para exportar um instantâneo para HDFS para ser usado por outro cluster, primeiro crie o instantâneo como descrito anteriormente e, em seguida, use o utilitário ExportSnapshot. Executar este utilitário de dentro da sessão SSH para o nó de cabeça, não dentro da `hbase` concha:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>
```

`<hdfsHBaseLocation>`Pode ser qualquer um dos locais de armazenamento acessíveis ao seu cluster de origem, e deve indicar para a pasta hbase utilizada pelo seu cluster de destino. Por exemplo, se tiver uma conta secundária de Armazenamento Azure anexada ao seu cluster de origem, e essa conta fornecer acesso ao contentor utilizado pelo armazenamento predefinido do cluster de destino, poderá utilizar este comando:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Se não tiver uma conta secundária de Armazenamento Azure anexada ao seu cluster de origem ou se o seu cluster de origem for um cluster no local (ou cluster não HDI), poderá experimentar problemas de autorização quando tentar aceder à conta de armazenamento do seu cluster HDI. Para resolver isto, especifique a chave da sua conta de armazenamento como um parâmetro de linha de comando como mostrado no exemplo seguinte. Pode obter a chave da sua conta de armazenamento no portal Azure.

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.myaccount.blob.core.windows.net=mykey -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Se o seu cluster de destino for um cluster ADLS Gen 2, altere o comando anterior para ajustar para as configurações que são usadas pela ADLS Gen 2:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.<account_name>.dfs.core.windows.net=<key> -Dfs.azure.account.auth.type.<account_name>.dfs.core.windows.net=SharedKey -Dfs.azure.always.use.https.<account_name>.dfs.core.windows.net=false -Dfs.azure.account.keyprovider.<account_name>.dfs.core.windows.net=org.apache.hadoop.fs.azurebfs.services.SimpleKeyProvider -snapshot 'Snapshot1' -copy-to 'abfs://<container>@<account_name>.dfs.core.windows.net/hbase'
```

Após a exportação do instantâneo, sSH no nó de cabeça do cluster de destino e restaurar o instantâneo usando o `restore_snapshot` comando como descrito anteriormente.

As imagens fornecem uma cópia de segurança completa de uma mesa no momento do `snapshot` comando. Os instantâneos não fornecem a capacidade de realizar instantâneos incrementais por janelas de tempo, nem especificar subconjuntos de colunas famílias para incluir no instantâneo.

## <a name="replication"></a>Replicação

[A replicação hBase](https://hbase.apache.org/book.html#_cluster_replication) empurra automaticamente as transações de um cluster de origem para um cluster de destino, utilizando um mecanismo assíncronoso com uma sobrecarga mínima no cluster de origem. Em HDInsight, pode configurar a replicação entre clusters onde:

* Os clusters de origem e destino estão na mesma rede virtual.
* Os clusters de origem e destinos estão em diferentes redes virtuais ligadas por uma porta de entrada VPN, mas ambos os clusters existem na mesma localização geográfica.
* Os clusters de origem e destinos estão em diferentes redes virtuais ligadas por uma porta VPN e cada cluster existe em uma localização geográfica diferente.

Os passos gerais para configurar a replicação são:

1. No cluster de origem, crie as tabelas e povoe dados.
2. No cluster de destino, crie mesas de destino vazias com o esquema da tabela de origem.
3. Registe o cluster de destino como um par para o cluster de origem.
4. Ativar a replicação nas tabelas de origem desejadas.
5. Copie os dados existentes das tabelas de origem para as tabelas de destino.
6. A replicação copia automaticamente novas modificações de dados nas tabelas de origem nas tabelas de destino.

Para ativar a replicação no HDInsight, aplique uma Ação de Script no seu cluster HDInsight de fonte de execução. Para uma passagem de ativação da replicação no seu cluster, ou para experimentar a replicação em agrupamentos de amostras criados em redes virtuais utilizando modelos de Gestão de Recursos Azure, consulte [a replicação Configure Apache HBase](apache-hbase-replication.md). Este artigo também inclui instruções para permitir a replicação de metadados Phoenix.

## <a name="next-steps"></a>Passos seguintes

* [Configurar a replicação apache HBase](apache-hbase-replication.md)
* [Trabalhar com a HBase Import and Export Utility](/archive/blogs/data_otaku/working-with-the-hbase-import-and-export-utility)
