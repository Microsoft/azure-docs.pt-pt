---
title: Visão geral do armazenamento Azure em HDInsight
description: Visão geral do Azure Storage em HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 40e1fdae5cdb1ec806e67dcacc70510a63093a82
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539467"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Visão geral do armazenamento Azure em HDInsight

O Azure Storage é uma solução de armazenamento de uso geral robusto que se integra perfeitamente com o HDInsight. O HDInsight pode utilizar um contentor de blobs no Armazenamento do Azure como o sistema de ficheiros predefinido para o cluster. Através de uma interface HDFS, todo o conjunto de componentes em HDInsight pode funcionar diretamente em dados estruturados ou não estruturados armazenados como bolhas.

Recomendamos a utilização de recipientes de armazenamento separados para o armazenamento padrão do seu cluster e os dados do seu negócio. A separação consiste em isolar os registos HDInsight e ficheiros temporários dos seus próprios dados de negócio. Recomendamos também a eliminação do recipiente de bolhas predefinidos, que contém registos de aplicação e sistema, após cada utilização para reduzir os custos de armazenamento. Certifique-se de que obtém os registos antes de eliminar o contentor.

Se optar por proteger a sua conta de armazenamento com as restrições **de Firewalls e redes virtuais** em redes **selecionadas,** certifique-se de ativar a exceção **Permitir serviços de Microsoft confiáveis...** . A exceção é para que o HDInsight possa aceder à sua conta de armazenamento.

## <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight

O diagrama a seguir proporciona uma visão abstrata da arquitetura HDInsight do Azure Storage:

!['HdInsight Storage Architecture'](./media/overview-azure-storage/storage-architecture.png "Arquitetura de armazenamento HDInsight")

O HDInsight fornece acesso ao sistema de ficheiros distribuído que está ligado localmente aos nós de computação. É possível aceder a este sistema de ficheiros ao utilizar o URI completamente qualificado, por exemplo:

`hdfs://<namenodehost>/<path>`

Através do HDInsight, também pode aceder a dados no Azure Storage. A sintaxe é a seguinte:

`wasb://<containername>@<accountname>.blob.core.windows.net/<path>`

Considere os seguintes princípios ao utilizar uma conta de Armazenamento Azure com clusters HDInsight:

* **Contentores nas contas do Storage ligadas a um cluster:** como o nome e a chave da conta são associados ao cluster durante a criação, tem acesso total aos blobs desses contentores.

* **Contentores públicos ou bolhas públicas em contas de armazenamento que não estejam ligadas a um cluster:** Tem autorização apenas para ler as bolhas nos contentores.
  
  > [!NOTE]  
  > Os contentores públicos permitem-lhe obter uma lista de todas as bolhas disponíveis nesse contentor e obter metadados de contentores. Os blobs públicos permitem aceder aos blobs apenas se souber o URL exato. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../storage/blobs/anonymous-read-access-configure.md) (Gerir o acesso de leitura anónima a contentores e blobs).

* **Contentores privados em contas de armazenamento que não estão ligadas a um cluster:** Não pode aceder às bolhas nos contentores a menos que defina a conta de armazenamento quando submete os trabalhos do WebHCat.

As contas do Storage definidas durante o processo de criação e as respetivas chaves são armazenadas em %HADOOP_HOME%/conf/core-site.xml nos nós do cluster. Por predefinição, o HDInsight utiliza as contas de armazenamento definidas no ficheiro core-site.xml. Pode modificar esta definição utilizando [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Vários trabalhos webHCat, incluindo Apache Hive. E mapReduce, apache Hadoop streaming, e Apache Pig, têm uma descrição de contas de armazenamento e metadados. (Este aspeto é atualmente verdadeiro para o Porco com contas de armazenamento, mas não para metadados.) Para obter mais informações, consulte [utilizar um cluster HDInsight com contas de armazenamento alternativas e metastores.](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)

Os blobs podem ser utilizados para dados estruturados e não estruturados. Os recipientes blob armazenam os dados como pares chave/valor e não têm hierarquia de diretório. No entanto, o nome chave pode incluir um carácter de corte (/ ) para fazê-lo parecer como se um ficheiro fosse armazenado dentro de uma estrutura de diretório. Por exemplo, a chave de uma bolha pode `input/log1.txt` ser. Nenhum `input` diretório real existe, mas por causa do caráter de corte no nome chave, a chave parece um caminho de arquivo.

## <a name="benefits-of-azure-storage"></a>Vantagens do Armazenamento do Azure

Os clusters de cálculo e os recursos de armazenamento que não são cotados têm custos de desempenho implícitos. Estes custos são atenuados pela forma como os clusters computativos são criados perto dos recursos da conta de armazenamento dentro da região de Azure. Nesta região, os nós computacional podem aceder eficientemente aos dados sobre a rede de alta velocidade dentro do Azure Storage.

Quando armazena os dados no Azure Storage em vez de HDFS, obtém vários benefícios:

* **Partilha e reutilização de dados:** os dados no HDFS estão localizados dentro do cluster de cálculo. Apenas as aplicações que têm acesso ao cluster de cálculo podem utilizar os dados ao utilizar as APIs do HDFS. Os dados em Azure Storage, pelo contrário, podem ser acedidos através das APIs HDFS ou das APIs de armazenamento blob. Devido a este arranjo, um conjunto maior de aplicações (incluindo outros clusters HDInsight) e ferramentas podem ser usadas para produzir e consumir os dados.

* **Arquivo de dados:** Quando os dados são armazenados no Azure Storage, os clusters HDInsight utilizados para a computação podem ser eliminados com segurança sem perder os dados do utilizador.

* **Custo de armazenamento de dados:** Armazenar dados em DFS a longo prazo é mais dispendioso do que armazenar os dados no Azure Storage. Porque o custo de um cluster de computação é superior ao custo do Azure Storage. Além disso, como os dados não têm de ser recarregados para cada geração de clusters de computação, também estás a poupar custos de carregamento de dados.

* **Aumento horizontal elástico:** embora o HDFS forneça um sistema de ficheiros ampliado horizontalmente, o dimensionamento é determinado pelo número de nós que cria para o cluster. Mudar a balança pode ser mais complicado do que as capacidades de escalonamento elástico que obtém automaticamente no Azure Storage.

* **Geo-replicação:** O seu Armazenamento Azure pode ser geo-replicado. Embora a geo-replicação lhe dê recuperação geográfica e redundância de dados, uma falha na localização geo-replicada afeta severamente o seu desempenho, e pode incorrer em custos adicionais. Por isso, escolha a geo-replicação com cautela e apenas se o valor dos dados justificar o custo adicional.

Certos trabalhos e pacotes mapReduce podem criar resultados intermédios que não gostaria de armazenar no Azure Storage. Nesse caso, pode optar por armazenar os dados no HDFS local. HDInsight usa DFS para vários destes resultados intermédios em trabalhos de Colmeia e outros processos.

> [!NOTE]  
> A maioria dos comandos HDFS (por `ls` exemplo, `copyFromLocal` , e ) funcionam como esperado no `mkdir` Azure Storage. Apenas os comandos específicos da implementação de HDFS nativo (que é referido como DFS), tais como `fschk` `dfsadmin` e, mostram comportamentos diferentes no Azure Storage.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
* [Descrição geral do Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)