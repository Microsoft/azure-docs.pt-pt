---
title: Migrar clusters do Apache Hadoop no local para o Azure HDInsight - práticas recomendadas de migração de dados
description: Conheça práticas recomendadas com uma migração de dados para migrar clusters do Hadoop no local para Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 488e1adb6d533dc88994e1dc08513870f56bd9c0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433047"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrar clusters do Apache Hadoop no local para o Azure HDInsight - práticas recomendadas de migração de dados

Este artigo oferece recomendações para a migração de dados para o Azure HDInsight. É parte de uma série que fornece as práticas recomendadas para ajudar a migrar sistemas de Apache Hadoop no local para Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Migrar dados no local para o Azure

Existem duas opções principais para migrar os dados no local para o ambiente do Azure:

1.  Transferir dados através de rede com TLS
    1. Internet - pode transferir dados para o armazenamento do Azure através de uma ligação de internet regulares através de qualquer uma das várias ferramentas, como: Explorador de armazenamento do Azure, o AzCopy, o Azure Powershell e o Azure CLI.  Ver [mover dados para e do armazenamento do Azure](../../storage/common/storage-moving-data.md) para obter mais informações.
    2. Expressroute – o ExpressRoute é um serviço do Azure que lhe permite criar ligações privadas entre os datacenters da Microsoft e de infraestrutura no local ou numa instalação de colocalização. As ligações ExpressRoute não entram na Internet pública e oferecem maior segurança, fiabilidade e velocidade com latências mais baixas que as ligações normais pela Internet. Para obter mais informações, consulte [criar e modificar um circuito do ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    1. Transferência de dados online de caixa de dados - limite de caixa de dados e dados de caixa de Gateway são produtos de transferência de dados online que atuam como gateways de armazenamento para gerir os dados entre o Azure e o site de rede. O Data Box Edge, um dispositivo de rede no local, transfere dados de e para o Azure e utiliza computação edge ativada por inteligência artificial (AI) para processar dados. O Data Box Gateway é uma aplicação virtual com capacidades de gateway de armazenamento. Para obter mais informações, consulte [documentação de caixa de dados do Azure - transferir Online](https://docs.microsoft.com/azure/databox-online/).
1.  O envio de dados Offline
    1. Importar / exportar serviço - pode enviar os discos físicos para o Azure e eles serão carregados por si. Para obter mais informações, consulte [o que é o serviço importar/exportar do Azure?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).
    1. Dados de caixa de transferência de dados offline - Data Box, disco do Data Box, e dados de caixa pesada dispositivos ajudá-lo a transferir grandes quantidades de dados para o Azure quando a rede não é uma opção. Estes dispositivos de transferência de dados offline são enviados entre a organização e o centro de dados do Azure. Utilizam encriptação AES para ajudar a proteger os dados em trânsito e são submetidos a um processo de limpeza pós-carregamento para eliminar dados do dispositivo. Para obter mais informações, consulte [Azure documentação do Data Box - Offline transferir](https://docs.microsoft.com/azure/databox/).

A tabela a seguir com duração de transferência de dados aproximada com base na largura de banda de rede e de volume de dados. Use uma caixa de dados se a migração de dados poderá demorar a demorar mais de três semanas.

|**Dados. de defeitos**|**Largura de banda de rede**|||
|---|---|---|---|
|| **45 Mbps (T3)**|**100 Mbps**|**1 Gbps**|**10 Gbps**
|1 TB|2 dias|1 dia| 2 horas|14 minutos|
|10 TB|22 dias|10 dias|1 dia|2 horas|
|35 TB|dias de 76|dias de 34|3 dias|8 horas|
|80 TB|dias 173|dias de 78|8 dias|19 horas|
|100 TB|dias 216|97 dias|10 dias|1 dia|
|200 TB|um ano|dias 194|dias de 19|2 dias|
|500 TB|3 anos|um ano|dias de 49|5 dias|
|1 PB|6 anos|3 anos|97 dias|10 dias|
|2 PB|há 12 anos|5 anos|dias 194|dias de 19|

Ferramentas nativas para o Azure, como o Apache Hadoop DistCp, Azure Data Factory e AzureCp, podem ser usadas para transferir dados através da rede. A ferramenta de terceiros que wandisco também pode ser utilizado com o mesmo objetivo. Apache Kafka Mirrormaker e Apache Sqoop podem ser utilizado para transferência de dados em curso no local para sistemas de armazenamento do Azure.


## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Considerações de desempenho ao utilizar o DistCp do Apache Hadoop


DistCp é um projeto do Apache que utiliza uma tarefa de MapReduce mapa para transferir dados, manipular os erros e recuperar esses erros. Atribui uma lista de ficheiros de origem para cada tarefa de mapa. A tarefa de mapa, em seguida, copia todos os seus ficheiros atribuídos para o destino. Existem várias técnicas podem melhorar o desempenho de DistCp.

### <a name="increase-the-number-of-mappers"></a>Aumentar o número de Mapeadores

DistCp tenta criar tarefas de mapa, de modo a que cada um deles aproximadamente copia o mesmo número de bytes. Por predefinição, as tarefas de DistCp utilizam 20 mapeadores. Com mais Mapeadores para Distcp (com o estou ' parâmetro na linha de comando) aumenta o paralelismo durante o processo de transferência de dados e diminui o tamanho de transferência de dados. No entanto, existem dois aspetos a considerar ao aumento do número de Mapeadores:

1. Granularidade de mais baixa do DistCp é um único arquivo. Especificar um número de Mapeadores mais do que o número de ficheiros de origem não ajuda e irá perder os recursos de cluster disponível.
1. Considere a memória de Yarn disponível no cluster para determinar o número de Mapeadores. Cada tarefa de mapa é iniciada como um contêiner de Yarn. Partindo do princípio que não existem outras cargas de trabalho pesadas estão em execução no cluster, o número de Mapeadores pode ser determinado pela seguinte fórmula: m = (número de nós de trabalho \* memória YARN para cada nó de trabalho) / tamanho do contentor do YARN. No entanto, se outros aplicativos estiver usando a memória, em seguida, selecione utilizar apenas uma parte da memória YARN para as tarefas de DistCp.

### <a name="use-more-than-one-distcp-job"></a>Utilizar mais do que uma tarefa de DistCp

Quando o tamanho do conjunto de dados sejam movidos é maior do que 1 TB, utilize mais do que uma tarefa de DistCp. Usando mais do que um trabalho limita o impacto das falhas. Se qualquer tarefa falhar, apenas terá de reiniciar esse trabalho específico em vez de todas as tarefas.

### <a name="consider-splitting-files"></a>Considere a divisão de ficheiros

Se existir um pequeno número de ficheiros grandes, considere dividi-los em segmentos de ficheiro de 256 MB para obter mais simultaneidade potencial com mais Mapeadores.

### <a name="use-the-strategy-command-line-parameter"></a>Utilize o parâmetro de linha de comandos de "estratégia"

Considere a utilização de `strategy = dynamic` parâmetro na linha de comandos. O valor predefinido da `strategy` parâmetro é `uniform size`, caso em que cada mapa aproximadamente copia o mesmo número de bytes. Quando este parâmetro for alterado para `dynamic`, o ficheiro de listagem é dividido em vários "segmentos-files". O número de ficheiros de bloco é um múltiplo do número de mapas. Cada tarefa de mapa é atribuída um dos arquivos de segmentos. Depois de todos os caminhos num segmento são processados, o segmento atual é eliminado e é obtido um novo bloco. O processo continua até que não existem mais segmentos estão disponíveis. Essa abordagem "dynamic" permite que mais rápido do mapa-tarefas consumir mais caminhos que os mais lentos, assim, acelerar a tarefa de DistCp geral.

### <a name="increase-the-number-of-threads"></a>Aumentar o número de threads

Se aumentar o `-numListstatusThreads` parâmetro melhora o desempenho. Este parâmetro controla o número de threads a utilizar para a criação de listagem de ficheiros e 40 é o valor máximo.

### <a name="use-the-output-committer-algorithm"></a>Utilizar o algoritmo de committer de saída

Veja se passando o parâmetro `-Dmapreduce.fileoutputcommitter.algorithm.version=2` melhora o desempenho de DistCp. Algoritmo de committer esta saída tem otimizações em torno de escrever ficheiros de saída para o destino. O comando seguinte é um exemplo que mostra o uso de parâmetros diferentes:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migração de metadados

### <a name="apache-hive"></a>Apache Hive

O metastore hive pode ser migrado usando os scripts ou utilizando a replicação de DB.

#### <a name="hive-metastore-migration-using-scripts"></a>Migração de metastore Hive através de scripts

1. Gere DDLs do Hive no metastore de Hive local. Este passo pode ser feito usando uma [script de bash de wrapper](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Edite a DDL gerada para substituir o url HDFS pelo ADLS/WASB/ABFS URLs.
1. Execute a DDL atualizada no metastore do HDInsight cluster.
1. Certifique-se de que a versão de metastore Hive é compatível entre no local e na cloud.

#### <a name="hive-metastore-migration-using-db-replication"></a>Migração de metastore Hive utilizando a replicação de DB

- Configure a replicação de base de dados entre locais metastore de Hive DB e HDInsight metastore DB.
- Utilize "MetaTool do Hive" para substituir o url HDFS com urls do ADLS/WASB/ABFS, por exemplo:

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="apache-ranger"></a>Apache Ranger

- Exporte políticas do Ranger no local para arquivos xml.
- Transforme no local em caminhos específicos com base no HDFS para WASB/ADLS usando uma ferramenta como XSLT.
- Importe as políticas de sessão no Ranger em execução no HDInsight.

## <a name="next-steps"></a>Passos Seguintes

Leia o artigo seguinte nesta série:

- [Práticas recomendadas de segurança e DevOps no local para a migração do Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-security-devops.md)