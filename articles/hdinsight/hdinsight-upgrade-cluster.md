---
title: Migrar cluster para uma versão mais recente
titleSuffix: Azure HDInsight
description: Aprenda as diretrizes para migrar o seu cluster Azure HDInsight para uma versão mais recente.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 95892bba1b3152dda718a4af3c5f45072054fdae
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935496"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Migrar o cluster HDInsight para uma versão mais recente

Para tirar partido das mais recentes funcionalidades hDInsight, recomendamos que os clusters HDInsight sejam regularmente migrados para a versão mais recente. O HDInsight não suporta atualizações no local onde um cluster existente é atualizado para uma versão componente mais recente. É necessário criar um novo cluster com o componente e a versão de plataforma pretendidos e depois migrar as suas aplicações para utilizar o novo cluster. Siga as seguintes diretrizes para migrar as versões do cluster HDInsight.

> [!NOTE]  
> Para obter informações sobre as versões suportadas do HDInsight, consulte [as versões componentes HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Tarefas de migração

O fluxo de trabalho para atualizar o Cluster HDInsight é o seguinte.
![o diagrama de fluxo de trabalho de upgrade da HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Leia cada secção deste documento para compreender as alterações que podem ser necessárias ao atualizar o seu cluster HDInsight.
2. Crie um cluster como um ambiente de garantia de teste/qualidade. Para obter mais informações sobre a criação de um cluster, consulte [Saiba como criar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Copiar empregos existentes, fontes de dados e afundar para o novo ambiente.
4. Efetue testes de validação para garantir que os seus trabalhos funcionem como esperado no novo cluster.

Depois de verificar que tudo funciona como esperado, marque o tempo de paragem para a migração. Durante este tempo de inatividade, faça as seguintes ações:

1. Faça backup de todos os dados transitórios armazenados localmente nos nós do cluster. Por exemplo, se tiver dados armazenados diretamente num nó de cabeça.
1. [Eliminar o cluster existente](./hdinsight-delete-cluster.md).
1. Crie um cluster na mesma subnet VNET com versão HDI mais recente (ou suportada) utilizando a mesma loja de dados padrão que o cluster anterior utilizou. Isto permite que o novo cluster continue a trabalhar contra os dados de produção existentes.
1. Importe quaisquer dados transitórios que tenha apoiado.
1. Iniciar trabalhos/continuar o processamento usando o novo cluster.

## <a name="workload-specific-guidance"></a>Orientação específica da carga de trabalho

Os seguintes documentos fornecem orientações sobre como migrar cargas de trabalho específicas:

* [Base HEmigração](/hbase/apache-hbase-migrate-new-version.md)
* [Emigrar Kafka](/kafka/migrate-versions.md)
* [Colmeia migratória/Consulta Interativa](/interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

Para obter mais informações sobre a cópia de segurança e restauro da base de dados, consulte Recuperar uma base de [dados Azure SQL utilizando cópias](../sql-database/sql-database-recovery-using-backups.md)de dados automatizadas .

## <a name="next-steps"></a>Passos seguintes

* [Saiba como criar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Ligar ao HDInsight através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gerencie um cluster baseado em Linux usando Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Notas de lançamento hDInsight](./hdinsight-version-release.md)
