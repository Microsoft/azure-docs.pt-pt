---
title: Azure HDInsight estudo de caso de arquitetura de solução altamente disponível
description: Este artigo é um estudo de caso fictício de uma possível arquitetura de solução Azure HDInsight altamente disponível.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop alta disponibilidade
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: a77dba165d76cc131f7a2a25a4b2f62e945a3089
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91844040"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Azure HDInsight estudo de caso de arquitetura de solução altamente disponível

Os mecanismos de replicação do Azure HDInsight podem ser integrados numa arquitetura de solução altamente disponível. Neste artigo, um estudo de caso fictício para a Contoso Retail é usado para explicar possíveis abordagens de recuperação de desastres de alta disponibilidade, considerações de custos e seus desenhos correspondentes.

Recomendações de recuperação de desastres de alta disponibilidade podem ter muitas permutações e combinações. Estas soluções devem ser tomadas depois de deliberar os prós e os contras de cada opção. Este artigo só discute uma solução possível.

## <a name="customer-architecture"></a>Arquitetura do cliente

A imagem que se segue retrata a arquitetura primária do Contoso Retail. A arquitetura consiste numa carga de trabalho de streaming, carga de trabalho de lote, camada de serviço, camada de consumo, camada de armazenamento e controlo de versão.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Arquitetura de retalho Contoso":::

### <a name="streaming-workload"></a>Carga de trabalho de streaming

Dispositivos e sensores produzem dados para a HDInsight Kafka, que constitui a estrutura de mensagens. Um consumidor de HDInsight Spark lê dos Tópicos Kafka. Spark transforma as mensagens recebidas e escreve-as para um cluster HBase HDInsight na camada de serviço.

### <a name="batch-workload"></a>Carga de trabalho do lote

Um cluster HDInsight Hadoop que executa a Hive e o MapReduce ingere dados de sistemas transacionais no local. Os dados brutos transformados pela Hive e mapReduce são armazenados em tabelas de Colmeia numa partição lógica do lago de dados apoiado por Azure Data Lake Storage Gen2. Os dados armazenados nas tabelas da Hive também são disponibilizados ao Spark SQL, que faz transformações de lote antes de armazenar os dados curados na HBase para servir.

### <a name="serving-layer"></a>Camada de fornecimento

Um cluster HDInsight HBase com Apache Phoenix é usado para servir dados para aplicações web e painéis de visualização. Um cluster HDInsight LLAP é utilizado para satisfazer os requisitos internos de reporte.

### <a name="consumption-layer"></a>Camada de consumo

Uma azure API Apps e uma camada de gestão API voltam a uma página pública virada para o público. Os requisitos internos de reporte são preenchidos pela Power BI.

### <a name="storage-layer"></a>Camada de armazenamento

Logicamente dividido Azure Data Lake Storage Gen2 é usado como um lago de dados da empresa. As metástarias HDInsight são apoiadas pela Azure SQL DB.

### <a name="version-control-system"></a>Sistema de controlo de versão

Um sistema de controlo de versão integrado num Azure Pipelines e alojado fora de Azure.

## <a name="customer-business-continuity-requirements"></a>Requisitos de continuidade do negócio do cliente

É importante determinar a funcionalidade mínima de negócio que precisa se houver um desastre.

### <a name="contoso-retails-business-continuity-requirements"></a>Os requisitos de continuidade do negócio da Contoso Retail

* Temos de ser protegidos contra uma falha regional ou uma questão de saúde dos serviços regionais.
* Os meus clientes nunca devem ver um erro de 404. O conteúdo público deve ser sempre servido. (RTO = 0)  
* Durante a maior parte do ano, podemos mostrar conteúdo público que está velho por 5 horas. (RPO = 5 horas)
* Durante a época festiva, o nosso conteúdo público deve estar sempre atualizado. (RPO = 0)
* Os meus requisitos internos de reporte não são considerados críticos para a continuidade do negócio.
* Otimizar os custos de continuidade do negócio.

## <a name="proposed-solution"></a>Solução proposta

A imagem a seguir mostra a arquitetura de recuperação de desastres de alta disponibilidade da Contoso Retail.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Arquitetura de retalho Contoso":::

**Kafka** usa a replicação [ativa – passiva](hdinsight-business-continuity-architecture.md#apache-kafka) para espelhar os tópicos kafka da região primária para a região secundária. Uma alternativa à replicação de Kafka poderia ser produzir para Kafka em ambas as regiões.

**A Colmeia e a Faísca** utilizam modelos de replicação secundária ativa e [on-demand](hdinsight-business-continuity-architecture.md#apache-spark) durante os tempos normais. O processo de replicação da Colmeia funciona periodicamente e acompanha a metastore hive Azure SQL e a replicação da conta de armazenamento de Colmeia. A conta de armazenamento Spark é periodicamente replicada utilizando a ADF DistCP. A natureza transitória destes clusters ajuda a otimizar os custos. As replicações são programadas a cada 4 horas para chegar a um RPO que está bem dentro do requisito de cinco horas.

A replicação **do HBase** utiliza o modelo [Leader – Follower](hdinsight-business-continuity-architecture.md#apache-hbase) em horários normais para garantir que os dados são sempre servidos independentemente da região e o RPO é zero.

Se houver uma falha regional na região primária, a página web e o conteúdo de backend são servidos da região secundária durante 5 horas com algum grau de estagnação. Se o painel de saúde do serviço Azure não indicar uma ETA de recuperação na janela de cinco horas, o Venda a Retalho Contoso criará a camada de transformação de Colmeia e Faísca na região secundária e, em seguida, apontará todas as fontes de dados a montante para a região secundária. Tornar a região secundária writable causaria um processo de recuo que envolve a replicação de volta ao primário.

Durante uma época de pico de compras, todo o gasoduto secundário está sempre ativo e em funcionamento. Os produtores de Kafka produzem para ambas as regiões e a replicação da HBase seria alterada de Leader-Follower para Leader-Leader para garantir que os conteúdos virados para o público estejam sempre atualizados.

Nenhuma solução de failover precisa de ser projetada para relatórios internos, uma vez que não é fundamental para a continuidade do negócio.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os itens discutidos neste artigo, consulte:

* [Referência Apache Ambari REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalar e configurar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
* [Instalar e configurar módulo Azure PowerShell Az](/powershell/azure/)
* [Gerir HDInsight usando Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Provision Clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)