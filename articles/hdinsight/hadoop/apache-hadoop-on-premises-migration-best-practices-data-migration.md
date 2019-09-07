---
title: Migrar clusters de Apache Hadoop locais para o Azure HDInsight-migração de dados
description: Conheça as práticas recomendadas de migração de dados para migrar clusters Hadoop locais para o Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 567edca422237c71f0d69c862a17fbc0d2a72795
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735918"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrar clusters de Apache Hadoop locais para o Azure HDInsight-práticas recomendadas de migração de dados

Este artigo fornece recomendações para a migração de dados para o Azure HDInsight. É parte de uma série que fornece as práticas recomendadas para ajudar na migração de sistemas locais Apache Hadoop para o Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Migrar dados locais para o Azure

Há duas opções principais para migrar dados do local para o ambiente do Azure:

1.  Transferir dados pela rede com TLS
    1. Na Internet – você pode transferir dados para o armazenamento do Azure por meio de uma conexão de Internet regular usando qualquer uma das várias ferramentas, como: Gerenciador de Armazenamento do Azure, AzCopy, Azure PowerShell e CLI do Azure.  Consulte [movendo dados de e para o armazenamento do Azure](../../storage/common/storage-moving-data.md) para obter mais informações.
    2. O Express Route-ExpressRoute é um serviço do Azure que permite criar conexões privadas entre os data centers da Microsoft e a infraestrutura que está no local ou em uma instalação de colocalização. As ligações ExpressRoute não entram na Internet pública e oferecem maior segurança, fiabilidade e velocidade com latências mais baixas que as ligações normais pela Internet. Para obter mais informações, consulte [criar e modificar um circuito do ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    1. Data Box transferência de dados online – Data Box Edge e Gateway do Data Box são produtos de transferência de dados online que atuam como gateways de armazenamento de rede para gerenciar dados entre seu site e o Azure. O Data Box Edge, um dispositivo de rede no local, transfere dados de e para o Azure e utiliza computação edge ativada por inteligência artificial (AI) para processar dados. O Data Box Gateway é uma aplicação virtual com capacidades de gateway de armazenamento. Para obter mais informações, consulte [a documentação do Azure data Box – transferência online](https://docs.microsoft.com/azure/databox-online/).
1.  Enviando dados offline
    1. Data Box transferência de dados offline-Data Box, Disco do Data Box e dispositivos Data Box Heavy ajudam a transferir grandes quantidades de dados para o Azure quando a rede não é uma opção. Estes dispositivos de transferência de dados offline são enviados entre a organização e o centro de dados do Azure. Utilizam encriptação AES para ajudar a proteger os dados em trânsito e são submetidos a um processo de limpeza pós-carregamento para eliminar dados do dispositivo. Para obter mais informações sobre o Data Box dispositivos de transferência offline, consulte [Azure data Box Documentação-transferência offline](https://docs.microsoft.com/azure/databox/). Para obter mais informações sobre a migração de clusters Hadoop, consulte [usar Azure data box para migrar de um repositório HDFS local para o armazenamento do Azure](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

A tabela a seguir tem uma duração de transferência de dados aproximada com base no volume de dados e na largura de banda da rede. Use uma caixa de dados se espera-se que a migração de dados demore mais do que três semanas.

|**Qtd de dados**|**Largura de banda da rede**||||
|---|---|---|---|---|
|| **45 Mbps (T3)**|**100 Mbps**|**1 Gbps**|**10 Gbps**|
|1 TB|2 dias|1 dia| Duas horas|14 minutos|
|10 TB|22 dias|10 dias|1 dia|Duas horas|
|35 TB|76 dias|34 dias|3 dias|8 horas|
|80 TB|173 dias|78 dias|8 dias|19 horas|
|100 TB|216 dias|97 dias|10 dias|1 dia|
|200 TB|1 ano|194 dias|19 dias|2 dias|
|500 TB|3 anos|1 ano|49 dias|5 dias|
|1 PB|6 anos|3 anos|97 dias|10 dias|
|2 PB|12 anos|5 anos|194 dias|19 dias|

As ferramentas nativas para o Azure, como Apache Hadoop DistCp, Azure Data Factory e AzureCp, podem ser usadas para transferir dados pela rede. A ferramenta de terceiros WANDisco também pode ser usada para a mesma finalidade. Apache Kafka MirrorMaker e Apache Sqoop podem ser usados para transferência de dados contínua do local para os sistemas de armazenamento do Azure.


## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Considerações sobre desempenho ao usar o Apache Hadoop DistCp


DistCp é um projeto do Apache que usa um trabalho de mapa do MapReduce para transferir dados, lidar com erros e se recuperar desses erros. Ele atribui uma lista de arquivos de origem a cada tarefa de mapa. Em seguida, a tarefa de mapeamento copia todos os seus arquivos atribuídos para o destino. Há várias técnicas que podem melhorar o desempenho do DistCp.

### <a name="increase-the-number-of-mappers"></a>Aumentar o número de Mapeadores

O DistCp tenta criar tarefas de mapa para que cada uma delas Copie aproximadamente o mesmo número de bytes. Por padrão, os trabalhos do DistCp usam 20 Mapeadores. O uso de mais Mapeadores para Distcp (com o parâmetro ' M' na linha de comando) aumenta o paralelismo durante o processo de transferência de dados e diminui o tamanho da transferência de dados. No entanto, há duas coisas a considerar ao aumentar o número de Mapeadores:

1. A granularidade mais baixa do DistCp é um único arquivo. A especificação de um número de Mapeadores mais do que o número de arquivos de origem não ajuda e perderá os recursos de cluster disponíveis.
1. Considere a memória yarn disponível no cluster para determinar o número de Mapeadores. Cada tarefa de mapa é iniciada como um contêiner yarn. Supondo que nenhuma outra carga de trabalho pesada esteja em execução no cluster, o número de Mapeadores pode ser determinado pela seguinte fórmula: m = (número de nós \* de trabalho yarn memória para cada nó de trabalho)/tamanho de contêiner yarn. No entanto, se outros aplicativos estiverem usando memória, escolha usar apenas uma parte da memória YARN para trabalhos do DistCp.

### <a name="use-more-than-one-distcp-job"></a>Usar mais de um trabalho do DistCp

Quando o tamanho do conjunto de informações a ser movido for maior que 1 TB, use mais de um trabalho do DistCp. O uso de mais de um trabalho limita o impacto das falhas. Se algum trabalho falhar, você só precisará reiniciar esse trabalho específico em vez de todos os trabalhos.

### <a name="consider-splitting-files"></a>Considerar a divisão de arquivos

Se houver um pequeno número de arquivos grandes, considere dividi-los em partes de arquivo de 256 MB para obter uma simultaneidade mais potencial com mais Mapeadores.

### <a name="use-the-strategy-command-line-parameter"></a>Usar o parâmetro de linha de comando ' Strategy '

Considere o `strategy = dynamic` uso do parâmetro na linha de comando. O valor padrão do `strategy` parâmetro é `uniform size`, nesse caso, cada mapa copia aproximadamente o mesmo número de bytes. Quando esse parâmetro é alterado para `dynamic`, o arquivo de listagem é dividido em vários "arquivos de parte". O número de arquivos de parte é um múltiplo do número de mapas. Cada tarefa de mapa é atribuída a um dos arquivos de parte. Depois que todos os caminhos em um bloco são processados, a parte atual é excluída e uma nova parte é adquirida. O processo continua até que não haja mais partes disponíveis. Essa abordagem "dinâmica" permite que tarefas de mapa mais rápidas consumam mais caminhos do que os mais lentos, acelerando, assim, o trabalho de DistCp geral.

### <a name="increase-the-number-of-threads"></a>Aumentar o número de threads

Veja se o aumento `-numListstatusThreads` do parâmetro melhora o desempenho. Esse parâmetro controla o número de threads a serem usados para criar a listagem de arquivos e 40 é o valor máximo.

### <a name="use-the-output-committer-algorithm"></a>Usar o algoritmo confirmador de saída

Veja se a passagem do `-Dmapreduce.fileoutputcommitter.algorithm.version=2` parâmetro melhora o desempenho do DistCp. Esse algoritmo de confirmação de saída tem otimizações relacionadas à gravação de arquivos de saída no destino. O comando a seguir é um exemplo que mostra o uso de parâmetros diferentes:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migração de metadados

### <a name="apache-hive"></a>Apache Hive

O metastore do hive pode ser migrado usando os scripts ou usando a replicação do BD.

#### <a name="hive-metastore-migration-using-scripts"></a>Migração de metastore do Hive usando scripts

1. Gere o hive DDLs no local metastore do Hive. Esta etapa pode ser feita usando um [script de bash de wrapper](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Edite o DDL gerado para substituir a URL do HDFS por URLs WASB/ADLS/ABFS.
1. Execute o DDL atualizado no metastore do cluster HDInsight.
1. Verifique se a versão do metastore do Hive é compatível entre o local e a nuvem.

#### <a name="hive-metastore-migration-using-db-replication"></a>Migração de metastore do Hive usando a replicação de BD

- Configure a replicação de banco de dados entre o local metastore do Hive DB e o BD de metastore do HDInsight.
- Use a "metaferramenta do hive" para substituir a URL do HDFS por URLs WASB/ADLS/ABFS, por exemplo:

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="apache-ranger"></a>Apache Ranger

- Exportar políticas locais do Ranger para arquivos XML.
- Transforme caminhos específicos baseados em HDFS no local para WASB/ADLS usando uma ferramenta como XSLT.
- Importe as políticas para o Ranger em execução no HDInsight.

## <a name="next-steps"></a>Passos Seguintes

Leia o próximo artigo desta série:

- [Práticas recomendadas de segurança e DevOps para migração local para Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
