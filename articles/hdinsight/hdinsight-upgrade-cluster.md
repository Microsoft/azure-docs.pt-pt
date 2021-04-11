---
title: Migrar cluster para uma versão mais recente
titleSuffix: Azure HDInsight
description: Aprenda as diretrizes para migrar o seu cluster Azure HDInsight para uma versão mais recente.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 4aa25368e156ce793e969f866490352e253559fc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871729"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Migrar o cluster HDInsight para uma versão mais recente

Para tirar partido das mais recentes funcionalidades hdInsight, recomendamos que os clusters HDInsight sejam regularmente migrados para a versão mais recente. O HDInsight não suporta atualizações no local onde um cluster existente é atualizado para uma versão de componente mais recente. Tem de criar um novo cluster com a versão de componente e plataforma desejada e, em seguida, migrar as suas aplicações para utilizar o novo cluster. Siga as diretrizes abaixo para migrar as suas versões de cluster HDInsight.

> [!NOTE]  
> Para obter informações sobre versões suportadas do HDInsight, consulte as [versões do componente HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Tarefas de migração

O fluxo de trabalho para atualizar o HDInsight Cluster é o seguinte.
:::image type="content" source="./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png" alt-text="Diagrama de fluxo de trabalho de upgrade HDInsight" border="false":::

1. Leia cada secção deste documento para compreender as alterações que podem ser necessárias ao atualizar o seu cluster HDInsight.
2. Criar um cluster como ambiente de garantia de teste/qualidade. Para obter mais informações sobre a criação de um cluster, consulte [Saiba como criar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Copiar os empregos existentes, fontes de dados e afundar para o novo ambiente.
4. Realize testes de validação para garantir que os seus trabalhos funcionam como esperado no novo cluster.

Depois de verificar que tudo funciona como esperado, agende o tempo de inatividade para a migração. Durante este tempo de inatividade, faça as seguintes ações:

1. Ressari quaisquer dados transitórios armazenados localmente nos nós do cluster. Por exemplo, se tiver dados armazenados diretamente num nó de cabeça.
1. [Eliminar o cluster existente.](./hdinsight-delete-cluster.md)
1. Crie um cluster na mesma sub-rede VNET com a versão HDI mais recente (ou suportada) utilizando a mesma loja de dados predefinida que o cluster anterior utilizou. Isto permite que o novo cluster continue a trabalhar contra os dados de produção existentes.
1. Importe quaisquer dados transitórios que tenha apoiado.
1. Iniciar trabalhos/continuar a processar utilizando o novo cluster.

## <a name="workload-specific-guidance"></a>Orientação específica da carga de trabalho

Os seguintes documentos fornecem orientações sobre como migrar cargas de trabalho específicas:

* [Migrar HBase](./hbase/apache-hbase-migrate-new-version.md)
* [Migrar Kafka](./kafka/migrate-versions.md)
* [Consulta migratória colmeia/interativa](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

Para obter mais informações sobre a cópia de segurança da base de dados e restaurar, consulte [recuperar uma base de dados na Base de Dados Azure SQL utilizando cópias de dados automáticas](../azure-sql/database/recovery-using-backups.md).

## <a name="next-steps"></a>Passos seguintes

* [Saiba como criar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Ligar ao HDInsight através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gerir um cluster baseado em Linux usando Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Notas de lançamento hdInsight](./hdinsight-version-release.md)
