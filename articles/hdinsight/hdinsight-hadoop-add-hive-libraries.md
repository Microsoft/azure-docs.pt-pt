---
title: Bibliotecas Apache Hive durante a criação de cluster - Azure HDInsight
description: Aprenda a adicionar bibliotecas Apache Hive (ficheiros de frascos) a um cluster HDInsight durante a criação do cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: 0b746963cea5a950ba47d8b4dfeb074cb0910436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471028"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Adicione bibliotecas personalizadas da Hive Apache ao criar o seu cluster HDInsight

Saiba como pré-carregar as bibliotecas [apache hive](https://hive.apache.org/) no HDInsight. Este documento contém informações sobre a utilização de uma Ação de Script para pré-carregar bibliotecas durante a criação de clusters. As bibliotecas adicionadas usando os passos deste documento estão globalmente disponíveis na Colmeia - não há necessidade de usar [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) para carregá-las.

## <a name="how-it-works"></a>Como funciona

Ao criar um cluster, pode usar uma Ação de Script para modificar os nós de cluster à medida que são criados. O script neste documento aceita um único parâmetro, que é a localização das bibliotecas. Esta localização deve estar numa Conta de Armazenamento Azure, e as bibliotecas devem ser armazenadas como ficheiros de frascos.

Durante a criação do cluster, o script `/usr/lib/customhivelibs/` enumera os ficheiros, copia-os para `hive.aux.jars.path` o diretório na cabeça e nos nós dos trabalhadores, acrescentando-os depois à propriedade no `core-site.xml` ficheiro. Nos clusters baseados no Linux, `hive-env.sh` também atualiza o ficheiro com a localização dos ficheiros.

A utilização da ação do script neste artigo disponibiliza as bibliotecas quando se utiliza um cliente da Colmeia para **WebHCat**e **HiveServer2**.

## <a name="the-script"></a>O guião

**Localização do script**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Requisitos

* Os scripts devem ser aplicados tanto nos nós da **cabeça** como nos **nós do Trabalhador.**

* Os frascos que pretende instalar devem ser guardados no Armazenamento De Blob Azure num **único recipiente**.

* A conta de armazenamento que contém a biblioteca de ficheiros de frascos **deve** ser ligada ao cluster HDInsight durante a criação. Deve ser a conta de armazenamento por defeito ou uma conta adicionada através das __Definições da Conta de Armazenamento__.

* O caminho WASB para o recipiente deve ser especificado como parâmetro para a Ação do Script. Por exemplo, se os frascos forem guardados num recipiente chamado **libs** numa `wasbs://libs@mystorage.blob.core.windows.net/`conta de armazenamento chamada **mystorage,** o parâmetro seria .

  > [!NOTE]  
  > Este documento pressupõe que já criou uma conta de armazenamento, um recipiente blob, e carregou os ficheiros para o mesmo.
  >
  > Se não criou uma conta de armazenamento, pode fazê-lo através do [portal Azure.](https://portal.azure.com) Em seguida, pode utilizar um utilitário como o [Azure Storage Explorer](https://storageexplorer.com/) para criar um recipiente na conta e enviar ficheiros para o mesmo.

## <a name="create-a-cluster-using-the-script"></a>Criar um cluster usando o script

1. Comece a fornecer um cluster utilizando os passos em [clusters Provision HDInsight em Linux](hdinsight-hadoop-provision-linux-clusters.md), mas não complete o fornecimento. Também pode utilizar o Azure PowerShell ou o HDInsight .NET SDK para criar um cluster utilizando este script. Para obter mais informações sobre a utilização destes métodos, consulte [customize os clusters HDInsight com as Ações](hdinsight-hadoop-customize-cluster-linux.md)do Script . Para o portal Azure, a partir do separador **De Configuração + preços,** selecione a **ação + Adicionar script**.

1. Para **armazenamento**, se a conta de armazenamento que contém a biblioteca de ficheiros de frascos for diferente da conta utilizada para o cluster, complete contas de **armazenamento adicionais**.

1. Para **ações de script,** forneça as seguintes informações:

    |Propriedade |Valor |
    |---|---|
    |Tipo de script|- Personalizado|
    |Nome|Bibliotecas |
    |Roteiro de bash URI|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Tipo de nó(s)|Cabeça, Trabalhador|
    |Parâmetros|Introduza o endereço WASB no recipiente e na conta de armazenamento que contém os frascos. Por exemplo, `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Para Apache Spark 2.1, use `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh`este roteiro de bash URI: .

1. Continue a fornecer o cluster conforme descrito nos [clusters Provision HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md).

Uma vez que a criação do cluster complete, você é capaz de usar `ADD JAR` os frascos adicionados através deste script da Hive sem ter que usar a declaração.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o trabalho com a [Hive, consulte Use Apache Hive com HDInsight](hadoop/hdinsight-use-hive.md)
