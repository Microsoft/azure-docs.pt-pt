---
title: Pacote de segurança da empresa para Azure HDInsight
description: Aprenda os componentes e versões do Pacote de Segurança Empresarial em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 8801929412da614784c76dbdf7c8282b31364409
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92327897"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Pacote de segurança da empresa para Azure HDInsight

Enterprise Security é um pacote opcional que pode adicionar no seu cluster HDInsight como parte da criação de fluxo de trabalho de cluster. O Pacote de Segurança Empresarial suporta:

* Integração com Diretório Ativo para autenticação.

    No passado, criou clusters HDInsight com utilizador de administração local e utilizador local de SSH. O utilizador administrativo local pode aceder a todos os ficheiros, pastas, tabelas e colunas.  Com o Pacote de Segurança Empresarial, você ativa o controlo de acesso baseado em funções Azure, integrando HDInsight com os seus Serviços de Domínio do Diretório Ativo Azure.

    Para obter mais informações, consulte:

    * [Uma introdução à segurança apache Hadoop com clusters HDInsight unidos pelo domínio](./domain-joined/hdinsight-security-overview.md)

    * [Plan Azure juntam-se a aglomerados Apache Hadoop em HDInsight](./domain-joined/apache-domain-joined-architecture.md)

    * [Configurar o ambiente sandbox associado a um domínio](./domain-joined/apache-domain-joined-configure.md)

    * [Configurar clusters HDInsight unidos pelo domínio usando serviços de domínio de diretório ativo Azure](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* Autorização de dados

  * Integração com Apache Ranger para autorização para Colmeia, Spark SQL e Yarn Queues.
  * Pode definir o controlo de acesso em ficheiros e pastas.

    Para obter mais informações, consulte [as políticas de Configuração da Colmeia Apache em HDInsight unidas ao domínio](./domain-joined/apache-domain-joined-run-hive.md)

* Consulte os registos de auditoria para monitorizar os acessos e as políticas configuradas.

## <a name="supported-cluster-types"></a>Tipos de cluster suportados

Atualmente, apenas os seguintes tipos de cluster suportam o Pacote de Segurança Empresarial:

* Hadoop (apenas HDInsight 3.6)
* Spark
* Kafka
* HBase
* Interactive Query

## <a name="support-for-azure-data-lake-storage"></a>Suporte para armazenamento do lago de dados Azure

O Pacote de Segurança Empresarial suporta a utilização do Azure Data Lake Storage como o armazenamento primário e o armazenamento de complementos.

## <a name="pricing-and-service-level-agreement-sla"></a>Acordo de preços e nível de serviço (SLA)

Para obter informações sobre preços e SLA para o Pacote de Segurança [Empresarial, consulte os preços da HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passos seguintes

* [Configuração de cluster para Apache Hadoop, Spark, e muito mais em HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Trabalhar em Apache Hadoop em HDInsight a partir de um PC windows](hdinsight-hadoop-windows-tools.md)
* [Notas de lançamento da Hortonworks associadas às versões Azure HDInsight](./hortonworks-release-notes.md)
* [Componentes do Apache no HDInsight](./hdinsight-component-versioning.md)
