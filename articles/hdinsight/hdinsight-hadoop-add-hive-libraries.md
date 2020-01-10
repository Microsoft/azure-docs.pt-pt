---
title: Bibliotecas de Apache Hive durante a criação do cluster-Azure HDInsight
description: Saiba como adicionar bibliotecas de Apache Hive (arquivos jar) a um cluster HDInsight durante a criação do cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 57b4440a29dde470f91bbaae091bf65a0d2a1b51
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552275"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Adicionar bibliotecas de Apache Hive personalizadas ao criar seu cluster HDInsight

Saiba como carregar previamente [Apache Hive](https://hive.apache.org/) bibliotecas no HDInsight. Este documento contém informações sobre como usar uma ação de script para pré-carregar bibliotecas durante a criação do cluster. As bibliotecas adicionadas usando as etapas deste documento estão globalmente disponíveis no hive – não há necessidade de usar [Add jar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) para carregá-las.

## <a name="how-it-works"></a>Como funciona

Ao criar um cluster, você pode usar uma ação de script para modificar nós de cluster à medida que eles são criados. O script neste documento aceita um único parâmetro, que é o local das bibliotecas. Esse local deve estar em uma conta de armazenamento do Azure e as bibliotecas devem ser armazenadas como arquivos jar.

Durante a criação do cluster, o script enumera os arquivos, copia-os para o diretório `/usr/lib/customhivelibs/` em nós de cabeçalho e de trabalho e os adiciona à propriedade `hive.aux.jars.path` no arquivo `core-site.xml`. Em clusters baseados em Linux, ele também atualiza o arquivo de `hive-env.sh` com o local dos arquivos.

O uso da ação de script neste artigo torna as bibliotecas disponíveis ao usar um cliente do hive para **WebHCat**e **HiveServer2**.

## <a name="the-script"></a>O script

**Local do script**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

**Requisitos**

* Os scripts devem ser aplicados aos nós de **cabeçalho** e aos **nós de trabalho**.

* Os jars que você deseja instalar devem ser armazenados no armazenamento de BLOBs do Azure em um **único contêiner**.

* A conta de armazenamento que contém a biblioteca de arquivos jar **deve** ser vinculada ao cluster HDInsight durante a criação. Ele deve ser a conta de armazenamento padrão ou uma conta adicionada por meio de __configurações de conta de armazenamento__.

* O caminho WASB para o contêiner deve ser especificado como um parâmetro para a ação de script. Por exemplo, se os jars são armazenados em um contêiner chamado **bibliotecas** em uma conta de armazenamento chamada **mystorage**, o parâmetro seria `wasbs://libs@mystorage.blob.core.windows.net/`.

  > [!NOTE]  
  > Este documento pressupõe que você já criou uma conta de armazenamento, um contêiner de BLOB e carregou os arquivos nela.
  >
  > Se você não tiver criado uma conta de armazenamento, poderá fazer isso por meio do [portal do Azure](https://portal.azure.com). Você pode usar um utilitário como [Gerenciador de armazenamento do Azure](https://storageexplorer.com/) para criar um contêiner na conta e carregar arquivos nele.

## <a name="create-a-cluster-using-the-script"></a>Criar um cluster usando o script

1. Inicie o provisionamento de um cluster usando as etapas em [provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md), mas não conclua o provisionamento. Você também pode usar Azure PowerShell ou o SDK do .NET do HDInsight para criar um cluster usando esse script. Para obter mais informações sobre como usar esses métodos, consulte [Personalizar clusters HDInsight com ações de script](hdinsight-hadoop-customize-cluster-linux.md). Para o portal do Azure, você deve selecionar a opção **ir para a experiência de criação clássica** e, em seguida, **Personalizar (tamanho, configurações, aplicativos)** .

1. Para **armazenamento**, se a conta de armazenamento que contém a biblioteca de arquivos jar for diferente da conta usada para o cluster, conclua **as contas de armazenamento adicionais**.

1. Para **ações de script**, forneça as seguintes informações:

    |Propriedade |Valor |
    |---|---|
    |Tipo de script|-Personalizado|
    |Nome|Bibliotecas |
    |URI do script bash|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Tipo (s) de nó|Cabeçalho, trabalhador|
    |Parâmetros|Insira o endereço WASB para o contêiner e a conta de armazenamento que contém os jars. Por exemplo, `wasbs://libs@mystorage.blob.core.windows.net/`.|

1. Continue Provisionando o cluster conforme descrito em [provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md).

Quando a criação do cluster for concluída, você poderá usar os jars adicionados por meio desse script do hive sem precisar usar a instrução `ADD JAR`.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como trabalhar com o Hive, confira [usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
