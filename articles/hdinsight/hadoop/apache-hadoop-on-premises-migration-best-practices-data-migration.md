---
title: 'Migração de dados: Apache Hadoop no local para Azure HDInsight'
description: Aprenda as melhores práticas de migração de dados para migrar no local aglomerados Hadoop para Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 41112359408497d84243ed9bb06f396acf008dc5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74666006"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrar no local Apache Hadoop clusters para Azure HDInsight - boas práticas de migração de dados

Este artigo dá recomendações para a migração de dados para o Azure HDInsight. Faz parte de uma série que fornece as melhores práticas para ajudar na migração dos sistemas Apache Hadoop para o Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Migrar dados no local para Azure

Existem duas opções principais para migrar dados do local para o ambiente Azure:

* Transferir dados sobre a rede com TLS
    * Através da internet - Pode transferir dados para o armazenamento do Azure através de uma ligação regular à Internet utilizando qualquer uma das várias ferramentas, tais como: Azure Storage Explorer, AzCopy, Azure Powershell e Azure CLI. Para mais informações, consulte [os dados em movimento de e para o Armazenamento Do Azure.](../../storage/common/storage-moving-data.md)

    * Express Route - ExpressRoute é um serviço Azure que permite criar ligações privadas entre datacenters da Microsoft e infraestruturas que estão nas suas instalações ou numa instalação de colocalização. As ligações ExpressRoute não passam pela Internet pública e oferecem maior segurança, fiabilidade e velocidades com latências mais baixas do que as ligações típicas através da Internet. Para mais informações, consulte [Criar e modificar um circuito ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).

    * Transferência de dados on-line Data Box - Data Box Edge e Data Box Gateway são produtos de transferência de dados online que funcionam como gateways de armazenamento de rede para gerir dados entre o seu site e o Azure. Data Box Edge, um dispositivo de rede no local, transfere dados de e para o Azure e utiliza uma computação de borda ativada por Inteligência Artificial (IA) para processar dados. Data Box Gateway é um aparelho virtual com capacidades de gateway de armazenamento. Para mais informações, consulte documentação da Caixa de [Dados do Azure - Transferência Online](https://docs.microsoft.com/azure/databox-online/).

* Dados de envio offline

    Transferência de dados offline data Box - Data Box, Data Box Disk e Data Box Os dispositivos pesados ajudam-no a transferir grandes quantidades de dados para o Azure quando a rede não é uma opção. Estes dispositivos de transferência de dados offline são enviados entre a sua organização e o centro de dados Azure. Utilizam encriptação AES para ajudar a proteger os seus dados em trânsito e passam por um processo de sanitização pós-upload completo para eliminar os seus dados do dispositivo. Para obter mais informações sobre os dispositivos de transferência offline da Data Box, consulte a Documentação da Caixa de [Dados do Azure - Transferência Offline](https://docs.microsoft.com/azure/databox/). Para obter mais informações sobre a migração de clusters Hadoop, consulte [use Azure Data Box para migrar de uma loja HDFS no local para o Armazenamento Azure.](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)

O quadro seguinte tem uma duração aproximada de transferência de dados com base no volume de dados e na largura de banda da rede. Utilize uma caixa de dados se a migração de dados demorar mais de três semanas.

|Data Qty|Largura de banda de rede||||
|---|---|---|---|---|
|| **45 Mbps (T3)**|**100 Mbps**|**1 Gbps**|**10 Gbps**|
|1 TB|2 dias|1 dia| Duas horas|14 minutos|
|10 TB|22 dias|10 dias|1 dia|Duas horas|
|35 TB|76 dias|34 dias|3 dias|8 horas|
|80 TB|173 dias|78 dias|8 dias|19 horas|
|100 TB|216 dias|97 dias|10 dias|1 dia|
|200 TB|1 ano|194 dias|19 dias|2 dias|
|500 TB|3 anos|1 ano|49 dias|5 dias|
|1 PB|6 anos|3 anos|97 dias|10 dias|
|2 PB|12 anos|5 anos|194 dias|19 dias|

Ferramentas nativas do Azure, como Apache Hadoop DistCp, Azure Data Factory e AzureCp, podem ser usadas para transferir dados pela rede. A ferramenta de terceiros WANDisco também pode ser usada para o mesmo fim. Apache Kafka Mirrormaker e Apache Sqoop podem ser usados para transferência contínua de dados do local para sistemas de armazenamento Azure.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Considerações de desempenho ao utilizar Apache Hadoop DistCp

DistCp é um projeto Apache que usa um trabalho mapreduce map para transferir dados, lidar com erros e recuperar desses erros. Atribui uma lista de ficheiros de origem a cada tarefa do Mapa. A tarefa do Mapa copia então todos os seus ficheiros atribuídos para o destino. Existem várias técnicas que podem melhorar o desempenho do DistCp.

### <a name="increase-the-number-of-mappers"></a>Aumentar o número de Mappers

A DistCp tenta criar tarefas de mapa para que cada um copie aproximadamente o mesmo número de bytes. Por padrão, os empregos da DistCp usam 20 mappers. A utilização de mais Mappers para Distcp (com o parâmetro 'm' na linha de comando) aumenta o paralelismo durante o processo de transferência de dados e diminui o comprimento da transferência de dados. No entanto, há duas coisas a considerar ao mesmo tempo que aumenta o número de Mappers:

* A granularidade mais baixa da DistCp é um único ficheiro. Especificar uma série de Mappers mais do que o número de ficheiros de origem não ajuda e irá desperdiçar os recursos de cluster disponíveis.

* Considere a memória de Fios disponível no cluster para determinar o número de Mappers. Cada tarefa do Mapa é lançada como recipiente de fios. Assumindo que nenhuma outra carga de trabalho pesada está a funcionar no cluster, o número de Mappers pode ser determinado pela seguinte fórmula: m = (número de nós de trabalhador memória \* YARN para cada nó de trabalhador) / Tamanho do recipiente YARN. No entanto, se outras aplicações estiverem a utilizar a memória, opte por utilizar apenas uma parte da memória yARN para trabalhos distCp.

### <a name="use-more-than-one-distcp-job"></a>Use mais do que um trabalho distCp

Quando o tamanho do conjunto de dados a mover for maior do que 1 TB, utilize mais do que um trabalho distCp. Usar mais de um trabalho limita o impacto das falhas. Se algum trabalho falhar, só precisa de reiniciar esse trabalho específico em vez de todos os empregos.

### <a name="consider-splitting-files"></a>Considere dividir ficheiros

Se houver um pequeno número de ficheiros grandes, então considere dividi-los em pedaços de ficheiro sumo de 256 MB para obter mais potencial conmoeda com mais Mappers.

### <a name="use-the-strategy-command-line-parameter"></a>Utilize o parâmetro de linha de comando 'estratégia'

Considere `strategy = dynamic` usar o parâmetro na linha de comando. O valor padrão `strategy` do `uniform size`parâmetro é, caso em que cada mapa copia aproximadamente o mesmo número de bytes. Quando este parâmetro é `dynamic`alterado para , o ficheiro de listagem é dividido em vários "ficheiros de pedaços". O número de ficheiros é um múltiplo do número de mapas. Cada tarefa do mapa é atribuída a um dos ficheiros. Depois de todos os caminhos num pedaço serem processados, o pedaço atual é eliminado e um novo pedaço é adquirido. O processo prossegue até que não existam mais pedaços disponíveis. Esta abordagem "dinâmica" permite que tarefas de mapas mais rápidas consumam mais caminhos do que os mais lentos, acelerando assim o trabalho da DistCp em geral.

### <a name="increase-the-number-of-threads"></a>Aumentar o número de fios

Veja se `-numListstatusThreads` o aumento do parâmetro melhora o desempenho. Este parâmetro controla o número de fios a utilizar para a listagem de ficheiros de construção e 40 é o valor máximo.

### <a name="use-the-output-committer-algorithm"></a>Use o algoritmo de compromisso de saída

Veja se passar `-Dmapreduce.fileoutputcommitter.algorithm.version=2` o parâmetro melhora o desempenho da DistCp. Este algoritmo de saída comprometedor tem otimizações em torno de escrever ficheiros de saída para o destino. O seguinte comando é um exemplo que mostra o uso de diferentes parâmetros:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migração de metadados

### <a name="apache-hive"></a>Colmeia Apache

A metaloja da colmeia pode ser migrada utilizando os scripts ou utilizando a Replicação DB.

#### <a name="hive-metastore-migration-using-scripts"></a>Migração de metaloja da colmeia usando scripts

1. Gere os DDLs da Colmeia a partir das instalações da metaloja da Hive. Este passo pode ser feito usando um roteiro de [bash wrapper](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Editar o DDL gerado para substituir o url HDFS por URLs WASB/ADLS/ABFS.
1. Executar o DDL atualizado na metaloja a partir do cluster HDInsight.
1. Certifique-se de que a versão da metaloja da Hive é compatível entre as instalações e a nuvem.

#### <a name="hive-metastore-migration-using-db-replication"></a>Migração de metaloja da colmeia usando replicação DB

- Configurar replicação de base de dados entre as instalações da metastore da Hive DB e a metastore HDInsight DB.
- Utilize o "Hive MetaTool" para substituir o url HDFS por urls WASB/ADLS/ABFS, por exemplo:

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- Exportar as políticas dos Rangers no local para ficheiros xml.
- Transforme em caminhos baseados em HDFS específicos para WASB/ADLS utilizando uma ferramenta como xSLT.
- Importe as políticas para ranger em execução no HDInsight.

## <a name="next-steps"></a>Passos seguintes

Leia o próximo artigo desta série:

- [Segurança e DevOps boas práticas para a migração de Hadoop Azure HDInsight](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
