---
title: Utilizar Apache Phoenix e SQLLine com o HBase no HDInsight do Azure
description: Saiba como utilizar o Apache Phoenix no HDInsight. Além disso, saiba como instalar e configurar SQLLine no seu computador para ligar a um cluster do HBase no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: hrasheed
ms.openlocfilehash: 6dee4ac7cb863a08e9046b16189e7f4a7b04b810
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201675"
---
# <a name="use-apache-phoenix-with-linux-based-apache-hbase-clusters-in-hdinsight"></a>Utilizar Apache Phoenix com clusters do Apache HBase baseado em Linux no HDInsight
Aprenda a usar [Apache Phoenix](https://phoenix.apache.org/) no Azure HDInsight e como utilizar SQLLine. Para obter mais informações sobre a Phoenix, consulte [Apache Phoenix em 15 minutos ou menos](https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para a gramática de Phoenix, consulte [gramática Apache Phoenix](https://phoenix.apache.org/language/index.html).

> [!NOTE]  
> Para informações de versão de Phoenix sobre o HDInsight, consulte [quais são as novidades nas versões de cluster do Apache Hadoop fornecidas pelo HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Utilizar SQLLine
[SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comandos para executar o SQL.

### <a name="prerequisites"></a>Pré-requisitos
Antes de poder utilizar SQLLine, tem de ter os seguintes itens:

* **Um cluster do Apache HBase no HDInsight**. Para criar um, veja [introdução ao Apache HBase no HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Quando se liga a um cluster do HBase, tem de se ligar a um da [Apache ZooKeeper](https://zookeeper.apache.org/) VMs. Cada cluster do HDInsight tem três VMs ZooKeeper.

**Para obter o nome do anfitrião ZooKeeper**

1. Open [Apache Ambari](https://ambari.apache.org/) ao navegar para **https://\<nome do cluster\>. azurehdinsight.net**.
2. Para iniciar sessão, introduza o nome de utilizador HTTP (cluster) e a palavra-passe.
3. No menu da esquerda, selecione **ZooKeeper**. Três **servidor ZooKeeper** são apresentadas instâncias.
4. Selecione uma da **servidor ZooKeeper** instâncias. Sobre o **resumo** painel, encontrar o **nome do anfitrião**. Que é semelhante a *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Para utilizar SQLLine**

1. Ligar ao cluster utilizando SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. No SSH, utilize os seguintes comandos para executar SQLLine:

        cd /usr/hdp/current/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Para criar uma tabela de HBase e inserir alguns dados, execute os seguintes comandos:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Para obter mais informações, consulte a [SQLLine manual](http://sqlline.sourceforge.net/#manual) e [gramática Apache Phoenix](https://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar o Apache Phoenix no HDInsight. Para obter mais informações, veja estes artigos:

* [Descrição geral do HBase do HDInsight][hdinsight-hbase-overview].
  O Apache HBase é uma base de dados para os NoSQL de código-fonte aberto, Apache, que se baseada no Apache Hadoop que fornece acesso aleatório e consistência segura para grandes quantidades de dados não estruturados e semi-estruturados.
* [Aprovisionar o Apache HBase, clusters na Azure Virtual Network][hdinsight-hbase-provision-vnet].
  Com a integração de rede virtual, clusters do Apache HBase podem ser implementados para a mesma rede virtual que seus aplicativos, para que os aplicativos podem comunicar diretamente com o HBase.
* [Configurar a replicação de Apache HBase no HDInsight](apache-hbase-replication.md). Saiba como configurar a replicação de Apache HBase em dois datacenters do Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


