---
title: Bibliotecas apaches colmeias durante a criação de cluster - Azure HDInsight
description: Saiba como adicionar bibliotecas Apache Hive (ficheiros de frascos) a um cluster HDInsight durante a criação do cluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: b6695e5e985a30d6f912095225c4899e1c910e34
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945962"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Adicione bibliotecas personalizadas da Hive Apache ao criar o seu cluster HDInsight

Aprenda a pré-carregar bibliotecas [apache hive](https://hive.apache.org/) em HDInsight. Este documento contém informações sobre a utilização de uma Ação de Script para pré-carregar bibliotecas durante a criação do cluster. As bibliotecas adicionadas usando os passos deste documento estão globalmente disponíveis na Hive - não há necessidade de usar [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) para carregá-las.

## <a name="how-it-works"></a>Como funciona

Ao criar um cluster, pode utilizar uma Ação de Script para modificar os nós do cluster à medida que são criados. O script neste documento aceita um único parâmetro, que é a localização das bibliotecas. Esta localização deve estar numa conta de armazenamento Azure, e as bibliotecas devem ser armazenadas como ficheiros de frascos.

Durante a criação do cluster, o script enumera os ficheiros, copia-os para o `/usr/lib/customhivelibs/` diretório na cabeça e nos nós dos trabalhadores, e depois adiciona-os à `hive.aux.jars.path` propriedade no `core-site.xml` ficheiro. Nos clusters baseados no Linux, também atualiza o `hive-env.sh` ficheiro com a localização dos ficheiros.

A utilização da ação de script neste artigo disponibiliza as bibliotecas ao utilizar um cliente Hive para **WebHCat,** e **HiveServer2**.

## <a name="the-script"></a>O guião

**Localização do script**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Requisitos

* Os scripts devem ser aplicados tanto nos **nós da Cabeça como** nos nós do **Trabalhador**.

* Os frascos que pretende instalar devem ser guardados num **único recipiente.**

* A conta de armazenamento que contém a biblioteca de ficheiros de frascos **deve** ser ligada ao cluster HDInsight durante a criação. Deve ser a conta de armazenamento predefinido ou uma conta adicionada através __das Definições de Conta de Armazenamento.__

* O caminho WASB para o recipiente deve ser especificado como um parâmetro para a Ação do Script. Por exemplo, se os frascos forem armazenados num recipiente denominado **libs** numa conta de armazenamento denominada **mystorage,** o parâmetro seria `wasbs://libs@mystorage.blob.core.windows.net/` .

  > [!NOTE]  
  > Este documento pressupõe que já criou uma conta de armazenamento, um recipiente blob e fez o upload dos ficheiros para o mesmo.
  >
  > Se não criou uma conta de armazenamento, pode fazê-lo através do [portal Azure.](https://portal.azure.com) Em seguida, pode utilizar um utilitário como [o Azure Storage Explorer](https://storageexplorer.com/) para criar um contentor na conta e enviar ficheiros para a sua conta.

## <a name="create-a-cluster-using-the-script"></a>Criar um cluster usando o script

1. Comece a agrupar um cluster utilizando os passos em [clusters De provision HDInsight no Linux,](hdinsight-hadoop-provision-linux-clusters.md)mas não complete o provisionamento. Também pode utilizar o Azure PowerShell ou o HDInsight .NET SDK para criar um cluster utilizando este script. Para obter mais informações sobre a utilização destes métodos, consulte [os clusters HDInsight personalizados com ações de script](hdinsight-hadoop-customize-cluster-linux.md). Para o portal Azure, a partir do **separador Configuração + preços,** selecione a **ação de script + Adicionar**.

1. Para **armazenamento**, se a conta de armazenamento que contém a biblioteca de ficheiros de frascos for diferente da conta utilizada para o cluster, preencha **contas de armazenamento adicionais**.

1. Para **ações de script**, forneça as seguintes informações:

    |Propriedade |Valor |
    |---|---|
    |Tipo de script|- Personalizado|
    |Name|Bibliotecas |
    |URI de guião de bash|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Tipo de nó(s)|Cabeça, Trabalhador|
    |Parâmetros|Introduza o endereço WASB na conta de contentores e armazenamento que contenham os frascos. Por exemplo, `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Para Apache Spark 2.1, use este guião de bash URI: `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh` .

1. Continue a agrupar o cluster descrito nos [clusters Provision HDInsight em Linux](hdinsight-hadoop-provision-linux-clusters.md).

Uma vez concluída a criação de clusters, você pode usar os frascos adicionados através deste script da Hive sem ter que usar a `ADD JAR` declaração.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o trabalho com a Colmeia, consulte [Use Apache Hive com HDInsight](hadoop/hdinsight-use-hive.md)
