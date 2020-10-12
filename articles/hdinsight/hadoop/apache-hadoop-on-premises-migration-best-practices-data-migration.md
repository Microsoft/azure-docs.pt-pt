---
title: 'Migração de dados: Apache Hadoop para Azure HDInsight'
description: Aprenda as melhores práticas de migração de dados para migrar no local os clusters Hadoop para Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 9794dd47949dc7dea891893dbcf261808ab335fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86521382"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrar para os aglomerados Apache Hadoop para Azure HDInsight - as melhores práticas de migração de dados

Este artigo dá recomendações para a migração de dados para a Azure HDInsight. Faz parte de uma série que fornece as melhores práticas para ajudar a migrar sistemas Apache Hadoop para Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Migrar dados no local para Azure

Existem duas opções principais para migrar dados do ambiente do local para o Azure:

* Transferir dados sobre rede com TLS
    * Através da internet - Pode transferir dados para o armazenamento Azure através de uma ligação regular à Internet utilizando qualquer uma das várias ferramentas tais como: Azure Storage Explorer, AzCopy, Azure Powershell e Azure CLI. Para obter mais informações, consulte [dados de e para o Armazenamento Azure.](../../storage/common/storage-moving-data.md)

    * Express Route - ExpressRoute é um serviço Azure que permite criar ligações privadas entre datacenters da Microsoft e infraestruturas que estão nas suas instalações ou numa instalação de cosão. As ligações ExpressRoute não passam pela Internet pública e oferecem maior segurança, fiabilidade e velocidades com latências mais baixas do que as ligações típicas através da Internet. Para obter mais informações, consulte [Criar e modificar um circuito ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).

    * Transferência de dados on-line Data Box - Data Box Edge e Data Box Gateway são produtos de transferência de dados on-line que funcionam como portais de armazenamento de rede para gerir dados entre o seu site e o Azure. Data Box Edge, um dispositivo de rede no local, transfere dados de e para a Azure e utiliza o cálculo de bordas ativados em inteligência artificial (IA) para processar dados. Data Box Gateway é um aparelho virtual com capacidades de gateway de armazenamento. Para mais informações, consulte [a documentação da Caixa de Dados Azure - Transferência Online.](https://docs.microsoft.com/azure/databox-online/)

* Envio de dados offline

    Transferência de dados offline da Caixa de Dados - Data Box, Data Box Disk e Data Box Os dispositivos pesados ajudam-no a transferir grandes quantidades de dados para o Azure quando a rede não é uma opção. Estes dispositivos offline de transferência de dados são enviados entre a sua organização e o centro de dados Azure. Eles usam encriptação AES para ajudar a proteger os seus dados em trânsito, e eles são submetidos a um processo de saneamento pós-upload completo para eliminar os seus dados do dispositivo. Para obter mais informações sobre os dispositivos de transferência offline da Caixa de Dados, consulte [a documentação da Caixa de Dados Azure - Transferência Offline](https://docs.microsoft.com/azure/databox/). Para obter mais informações sobre a migração de clusters Hadoop, consulte [Use Azure Data Box para migrar de uma loja HDFS no local para o Azure Storage](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

O quadro seguinte tem duração de transferência de dados aproximadamente com base no volume de dados e na largura de banda da rede. Utilize uma caixa de dados se a migração de dados demorar mais de três semanas.

|Qty de dados | Largura de banda de rede<br>de<br>**45 Mbps (T3)**|Largura de banda de rede<br>de<br>**100 Mbps**|Largura de banda de rede<br>de<br>**1 Gbps**|Largura de banda de rede<br>de<br>**10 Gbps**|
|---|:---:|:---:|:---:|:---:|
|1 TB|2 dias|1 dia| Duas horas|14 minutos|
|10 TB|22 dias|10 dias|1 dia|Duas horas|
|35 TB|76 dias|34 dias|3 dias|8 horas|
|80 TB|173 dias|78 dias|8 dias|19 horas|
|100 TB|216 dias|97 dias|10 dias|1 dia|
|200 TB|1 ano|194 dias|19 dias|2 dias|
|500 TB|3 anos|1 ano|49 dias|5 dias|
|1 PB|6 anos|3 anos|97 dias|10 dias|
|2 PB|12 anos|5 anos|194 dias|19 dias|

Ferramentas nativas de Azure, como Apache Hadoop DistCp, Azure Data Factory e AzureCp, podem ser usadas para transferir dados pela rede. A ferramenta de terceiros WANDisco também pode ser usada para o mesmo fim. Apache Kafka Mirrormaker e Apache Sqoop podem ser usados para transferência contínua de dados de sistemas de armazenamento Azure.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Considerações de desempenho ao utilizar o Apache Hadoop DistCp

DistCp é um projeto Apache que usa um trabalho do MapReduce Map para transferir dados, lidar com erros e recuperar desses erros. Atribui uma lista de ficheiros de origem a cada tarefa do Mapa. A tarefa do Mapa copia então todos os seus ficheiros atribuídos para o destino. Existem várias técnicas que podem melhorar o desempenho do DistCp.

### <a name="increase-the-number-of-mappers"></a>Aumentar o número de Mappers

O DistCp tenta criar tarefas de mapa para que cada um copie aproximadamente o mesmo número de bytes. Por defeito, os empregos da DistCp usam 20 mappers. A utilização de mais Mappers para Distcp (com o parâmetro 'm' na linha de comando) aumenta o paralelismo durante o processo de transferência de dados e diminui o comprimento da transferência de dados. No entanto, há duas coisas a considerar ao mesmo tempo que aumenta o número de Mappers:

* A granularidade mais baixa da DistCp é um único ficheiro. Especificar um número de Mappers mais do que o número de ficheiros de origem não ajuda e desperdiçará os recursos de cluster disponíveis.

* Considere a memória de fios disponível no cluster para determinar o número de Mappers. Cada tarefa do Mapa é lançada como um recipiente de fios. Assumindo que não estão a correr outras cargas de trabalho pesadas no cluster, o número de Mappers pode ser determinado pela seguinte fórmula: m = (número de nós de trabalhadores \* memória YARN para cada nó de trabalhador) / Tamanho do contentor YARN. No entanto, se outras aplicações estiverem a usar a memória, então opte por utilizar apenas uma parte da memória YARN para trabalhos DistCp.

### <a name="use-more-than-one-distcp-job"></a>Use mais de um trabalho DistCp

Quando o tamanho do conjunto de dados a ser movido for superior a 1 TB, utilize mais do que um trabalho DistCp. A utilização de mais de um trabalho limita o impacto das falhas. Se algum trabalho falhar, só tens de reiniciar esse trabalho específico em vez de todos os empregos.

### <a name="consider-splitting-files"></a>Considere dividir ficheiros

Se houver um pequeno número de ficheiros grandes, então considere dividi-los em pedaços de ficheiro de 256-MB para obter mais concuência potencial com mais Mappers.

### <a name="use-the-strategy-command-line-parameter"></a>Use o parâmetro da linha de comando 'estratégia'

Considere usar `strategy = dynamic` o parâmetro na linha de comando. O valor predefinido do `strategy` parâmetro `uniform size` é, nesse caso, cada mapa copia aproximadamente o mesmo número de bytes. Quando este parâmetro é alterado para `dynamic` , o ficheiro de listagem é dividido em vários "ficheiros de pedaços". O número de ficheiros de pedaços é um múltiplo do número de mapas. Cada tarefa de mapa é atribuída a um dos ficheiros de pedaços. Depois de todos os caminhos em um pedaço são processados, o pedaço atual é eliminado e um novo pedaço é adquirido. O processo continua até que não existam mais pedaços disponíveis. Esta abordagem "dinâmica" permite que tarefas de mapa mais rápidas consumam mais caminhos do que os mais lentos, acelerando assim o trabalho da DistCp em geral.

### <a name="increase-the-number-of-threads"></a>Aumentar o número de fios

Veja se o aumento do parâmetro melhora o `-numListstatusThreads` desempenho. Este parâmetro controla o número de fios a utilizar para a listagem de ficheiros de construção e 40 é o valor máximo.

### <a name="use-the-output-committer-algorithm"></a>Use o algoritmo do comprometidor de saída

Veja se a passagem do parâmetro melhora o `-Dmapreduce.fileoutputcommitter.algorithm.version=2` desempenho do DistCp. Este algoritmo de compromisso de saída tem otimizações em torno da escrita de ficheiros de saída para o destino. O seguinte comando é um exemplo que mostra a utilização de diferentes parâmetros:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migração de metadados

### <a name="apache-hive"></a>Colmeia Apache

A metástore da colmeia pode ser migrada usando os scripts ou usando a replicação DB.

#### <a name="hive-metastore-migration-using-scripts"></a>Migração de metástasias de colmeia usando scripts

1. Gere os DDLs da Hive nas instalações Da metástaria da Hive. Este passo pode ser feito usando um [script de festa de invólucro](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Editar o DDL gerado para substituir o url HDFS por URLs WASB/ADLS/ABFS.
1. Executar o DDL atualizado na meta-loja a partir do cluster HDInsight.
1. Certifique-se de que a versão metaloja da Colmeia é compatível entre as instalações e a nuvem.

#### <a name="hive-metastore-migration-using-db-replication"></a>Migração de metástaria de colmeia usando replicação DB

- Configurar a replicação da base de dados entre as metástasias de Colmeia DB e a metásta dB da metásta hdinsight.
- Utilize o "Hive MetaTool" para substituir o url HDFS por urls WASB/ADLS/ABFS, por exemplo:

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- Exportar no local as políticas ranger para ficheiros XML.
- Transforme em caminhos específicos de HDFS para WASB/ADLS usando uma ferramenta como o XSLT.
- Importe as políticas para ranger em execução em HDInsight.

## <a name="next-steps"></a>Passos seguintes

Leia o próximo artigo desta série:

- [Segurança e DevOps boas práticas para a migração de Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
