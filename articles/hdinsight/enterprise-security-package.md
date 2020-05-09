---
title: Pacote de segurança empresarial para Azure HDInsight
description: Conheça os componentes e versões do Pacote de Segurança Empresarial no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 07210350826001bd3e0be6e04be211c9de43695a
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997259"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Pacote de segurança empresarial para Azure HDInsight

Enterprise Security é um pacote opcional que pode adicionar no seu cluster HDInsight como parte da criação de fluxo de trabalho de cluster. O Pacote de Segurança Empresarial suporta:

* Integração com Diretório Ativo para autenticação.

    No passado, criou clusters HDInsight com o utilizador administrativo local e o utilizador local de SSH. O utilizador administrativo local pode aceder a todos os ficheiros, pastas, tabelas e colunas.  Com o Enterprise Security Package, permite o controlo de acesso baseado em funções, integrando o HDInsight com o seu Diretório Ativo. Que inclui o Ative Directory no local, os Serviços de Domínio de Diretório Ativo Azure. Ou Diretório Ativo na máquina virtual IaaS. O administrador de domínio do cluster pode conceder aos utilizadores a utilização do seu próprio nome de utilizador e palavra-passe corporativos (domínio).

    Para obter mais informações, consulte:

    * [Uma introdução à segurança apache Hadoop com clusters HDInsight unidos pelo domínio](./domain-joined/hdinsight-security-overview.md)

    * [Grupos Apache Hadoop unidos pelo domínio do Plan Azure em HDInsight](./domain-joined/apache-domain-joined-architecture.md)

    * [Configurar o ambiente sandbox associado a um domínio](./domain-joined/apache-domain-joined-configure.md)

    * [Configure clusters HDInsight unidos pelo domínio utilizando serviços de domínio de diretório ativo Azure](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* Autorização de dados

  * Integração com Apache Ranger para autorização para filas de Colmeia, Spark SQL e Fios.
  * Pode definir o controlo de acesso em ficheiros e pastas.

    Para mais informações, consulte as políticas de [Configure Apache Hive em HDInsight filiado em domínio](./domain-joined/apache-domain-joined-run-hive.md)

* Consulte os registos de auditoria para monitorizar os acessos e as políticas configuradas.

## <a name="supported-cluster-types"></a>Tipos de cluster suportados

Atualmente, apenas os seguintes tipos de cluster suportam o Pacote de Segurança Empresarial:

* Hadoop (apenas HDInsight 3.6)
* Spark
* Kafka
* HBase
* Interactive Query

## <a name="support-for-azure-data-lake-storage"></a>Suporte para armazenamento de lagos de dados azure

O Pacote de Segurança Empresarial suporta a utilização do Armazenamento de Lagos De Dados Azure como armazenamento primário e armazenamento de complemento.

## <a name="pricing-and-service-level-agreement-sla"></a>Acordo de preços e nível de serviço (SLA)

Para obter informações sobre preços e SLA para o Pacote de Segurança Empresarial, consulte [os preços hDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passos seguintes

* [Configuração de cluster para Apache Hadoop, Spark e muito mais no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Trabalhe em Apache Hadoop no HDInsight a partir de um PC do Windows](hdinsight-hadoop-windows-tools.md)
* [Notas de lançamento da Hortonworks associadas às versões Azure HDInsight](./hortonworks-release-notes.md)
* [Componentes Apache no HDInsight](./hdinsight-component-versioning.md)