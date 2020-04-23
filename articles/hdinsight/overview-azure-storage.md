---
title: Visão geral do armazenamento azure no HDInsight
description: Visão geral do Armazenamento Azure no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 43d948a2a98407bacc212ddc6e065c67a105f332
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873384"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Visão geral do armazenamento azure no HDInsight

O Azure Storage é uma solução robusta de armazenamento geral que se integra perfeitamente com o HDInsight. O HDInsight pode utilizar um contentor de blobs no Armazenamento do Azure como o sistema de ficheiros predefinido para o cluster. Através de uma interface HDFS, o conjunto completo de componentes no HDInsight pode funcionar diretamente em dados estruturados ou não estruturados armazenados como bolhas.

Recomendamos a utilização de recipientes de armazenamento separados para o seu armazenamento de cluster sinuoso e os seus dados de negócio. A separação é isolar os registos HDInsight e ficheiros temporários dos seus próprios dados de negócio. Recomendamos também a apagar o recipiente de bolhas predefinido, que contém registos de aplicação e sistema, após cada utilização para reduzir o custo de armazenamento. Certifique-se de que obtém os registos antes de eliminar o contentor.

Se optar por proteger a sua conta de armazenamento com as **firewalls e** as restrições de redes virtuais nas **redes Selecionadas,** certifique-se de que permite a exceção **Permitir serviços microsoft fidedignos...**. A exceção é para que o HDInsight possa aceder à sua conta de armazenamento.

## <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight

O diagrama seguinte proporciona uma visão abstrata da arquitetura HDInsight do Armazenamento Azure:

!['HDInsight Storage Architecture'](./media/overview-azure-storage/storage-architecture.png "Arquitetura de armazenamento HDInsight")

O HDInsight fornece acesso ao sistema de ficheiros distribuído que está ligado localmente aos nós de computação. É possível aceder a este sistema de ficheiros ao utilizar o URI completamente qualificado, por exemplo:

    hdfs://<namenodehost>/<path>

Através do HDInsight, também pode aceder a dados no Armazenamento Azure. A sintaxe é a seguinte:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Considere os seguintes princípios ao utilizar uma conta de Armazenamento Azure com clusters HDInsight:

* **Contentores nas contas do Storage ligadas a um cluster:** como o nome e a chave da conta são associados ao cluster durante a criação, tem acesso total aos blobs desses contentores.

* **Contentores públicos ou bolhas públicas em contas de armazenamento que não estejam ligadas a um cluster:** Tem apenas autorização para ler as bolhas nos contentores.
  
  > [!NOTE]  
  > Os contentores públicos permitem-lhe obter uma lista de todas as bolhas que estão disponíveis nesse contentor e obter metadados de contentores. Os blobs públicos permitem aceder aos blobs apenas se souber o URL exato. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../storage/blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).

* **Contentores privados em contas de armazenamento que não estão ligados a um cluster:** Não pode aceder às bolhas nos contentores a menos que defina a conta de armazenamento quando submete os trabalhos webHCat.

As contas do Storage definidas durante o processo de criação e as respetivas chaves são armazenadas em %HADOOP_HOME%/conf/core-site.xml nos nós do cluster. Por predefinição, o HDInsight utiliza as contas de armazenamento definidas no ficheiro core-site.xml. Pode modificar esta definição utilizando [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Vários trabalhos webHCat, incluindo Apache Hive. E mapReduce, streaming Apache Hadoop, e Apache Pig, têm uma descrição de contas de armazenamento e metadados. (Este aspeto é atualmente verdadeiro para o Porco com contas de armazenamento, mas não para metadados.) Para mais informações, consulte [Utilizar um cluster HDInsight com contas de armazenamento alternativas e metalojas](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Os blobs podem ser utilizados para dados estruturados e não estruturados. Os recipientes blob armazenam dados como par de chaves/valor e não têm hierarquia de direção. No entanto, o nome-chave pode incluir um personagem de corte (/ ) para fazer parecer que um ficheiro está armazenado dentro de uma estrutura de diretório. Por exemplo, a chave de `input/log1.txt`uma bolha pode ser. Não `input` existe um diretório real, mas por causa do personagem slash no nome chave, a chave parece um caminho de arquivo.

## <a name="benefits-of-azure-storage"></a>Vantagens do Armazenamento do Azure

Os clusters computacionais e os recursos de armazenamento que não estão colocalizados têm custos de desempenho implícitos. Estes custos são atenuados pela forma como os clusters de cálculo são criados perto dos recursos da conta de armazenamento dentro da região de Azure. Nesta região, os nós de computação podem aceder eficientemente aos dados sobre a rede de alta velocidade dentro do Armazenamento Azure.

Ao armazenar os dados no Armazenamento Azure em vez de HDFS, obtém vários benefícios:

* **Partilha e reutilização de dados:** os dados no HDFS estão localizados dentro do cluster de cálculo. Apenas as aplicações que têm acesso ao cluster de cálculo podem utilizar os dados ao utilizar as APIs do HDFS. Os dados em Armazenamento Azure, pelo contrário, podem ser acedidos através das APIs hdfs ou do armazenamento Blob REST APIs. Devido a este arranjo, um conjunto maior de aplicações (incluindo outros clusters HDInsight) e ferramentas podem ser usadas para produzir e consumir os dados.

* **Arquivamento de dados:** Quando os dados são armazenados no Armazenamento Azure, os clusters HDInsight utilizados para a computação podem ser eliminados com segurança sem perder dados do utilizador.

* **Custo de armazenamento de dados:** Armazenar dados em DFS a longo prazo é mais dispendioso do que armazenar os dados no Armazenamento Azure. Porque o custo de um cluster de computação é superior ao custo do Armazenamento Azure. Além disso, como os dados não têm de ser recarregados para cada geração de cluster de cálculo, também estás a poupar custos de carregamento de dados.

* **Aumento horizontal elástico:** embora o HDFS forneça um sistema de ficheiros ampliado horizontalmente, o dimensionamento é determinado pelo número de nós que cria para o cluster. Mudar a balança pode ser mais complicado do que as capacidades elásticas de escala que obtém automaticamente no Armazenamento Azure.

* **Geo-replicação:** O seu Armazenamento Azure pode ser geo-replicado. Embora a geo-replicação lhe dê recuperação geográfica e redundância de dados, uma falha na localização geo-replicada afeta severamente o seu desempenho, e pode incorrer em custos adicionais. Por isso, escolha a geo-replicação com cautela e apenas se o valor dos dados justificar o custo adicional.

Certos trabalhos e pacotes MapReduce podem criar resultados intermédios que não gostaria de armazenar no Armazenamento Azure. Nesse caso, pode optar por armazenar os dados no HDFS local. O HDInsight utiliza o DFS para vários destes resultados intermédios em empregos da Colmeia e outros processos.

> [!NOTE]  
> A maioria dos comandos HDFS (por exemplo, `ls`e `copyFromLocal` `mkdir`) funcionam como esperado no Armazenamento Azure. Apenas os comandos específicos da implementação nativa do HDFS (que é referido como DFS), tais como `fschk` e `dfsadmin`, mostram diferentes comportamentos no Armazenamento Azure.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Armazenamento de Lagos De Dados Azure Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
* [Visão geral do Armazenamento do Lago Azure Data Gen1](./overview-data-lake-storage-gen1.md)