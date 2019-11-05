---
title: Bibliotecas de Apache Hive durante a criação do cluster-Azure HDInsight
description: Saiba como adicionar bibliotecas de Apache Hive (arquivos jar) a um cluster HDInsight durante a criação do cluster.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 51a93aaec4abdb2dd9d8fad042c079a48d4ea7a3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494841"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Adicionar bibliotecas de Apache Hive personalizadas ao criar seu cluster HDInsight

Saiba como carregar previamente [Apache Hive](https://hive.apache.org/) bibliotecas no HDInsight. Este documento contém informações sobre como usar uma ação de script para pré-carregar bibliotecas durante a criação do cluster. As bibliotecas adicionadas usando as etapas deste documento estão globalmente disponíveis no hive – não é necessário usar [Add jar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) para carregá-las.

## <a name="how-it-works"></a>Como funciona

Ao criar um cluster, você pode usar uma ação de script para modificar nós de cluster à medida que eles são criados. O script neste documento aceita um único parâmetro, que é o local das bibliotecas. Esse local deve estar em uma conta de armazenamento do Azure e as bibliotecas devem ser armazenadas como arquivos jar.

Durante a criação do cluster, o script enumera os arquivos, copia-os para o diretório `/usr/lib/customhivelibs/` em nós de cabeçalho e de trabalho e os adiciona à propriedade `hive.aux.jars.path` no arquivo `core-site.xml`. Em clusters baseados em Linux, ele também atualiza o arquivo de `hive-env.sh` com o local dos arquivos.

> [!NOTE]  
> O uso das ações de script neste artigo torna as bibliotecas disponíveis nos seguintes cenários:
>
> * **HDInsight baseado em Linux** – ao usar um cliente do hive, **WebHCat**e **HiveServer2**.
> * **HDInsight baseado em Windows** – ao usar o cliente do hive e o **WebHCat**.

## <a name="the-script"></a>O script

**Local do script**

Para **clusters baseados em Linux**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Para **clusters baseados no Windows**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

**Requisitos**

* Os scripts devem ser aplicados aos nós de **cabeçalho** e aos **nós de trabalho**.

* Os jars que você deseja instalar devem ser armazenados no armazenamento de BLOBs do Azure em um **único contêiner**.

* A conta de armazenamento que contém a biblioteca de arquivos jar **deve** ser vinculada ao cluster HDInsight durante a criação. Ele deve ser a conta de armazenamento padrão ou uma conta adicionada por meio da __configuração opcional__.

* O caminho WASB para o contêiner deve ser especificado como um parâmetro para a ação de script. Por exemplo, se os jars são armazenados em um contêiner chamado **bibliotecas** em uma conta de armazenamento chamada **mystorage**, o parâmetro seria **WASB://libs\@mystorage.blob.Core.Windows.net/** .

  > [!NOTE]  
  > Este documento pressupõe que você já criou uma conta de armazenamento, um contêiner de BLOB e carregou os arquivos nela.
  >
  > Se você não tiver criado uma conta de armazenamento, poderá fazer isso por meio do [portal do Azure](https://portal.azure.com). Você pode usar um utilitário como [Gerenciador de armazenamento do Azure](https://storageexplorer.com/) para criar um contêiner na conta e carregar arquivos nele.

## <a name="create-a-cluster-using-the-script"></a>Criar um cluster usando o script

> [!NOTE]  
> As etapas a seguir criam um cluster HDInsight baseado em Linux. Para criar um cluster baseado no Windows, selecione **Windows** como o sistema operacional do cluster ao criar o cluster e use o script do Windows (PowerShell) em vez do script bash.
>
> Você também pode usar Azure PowerShell ou o SDK do .NET do HDInsight para criar um cluster usando esse script. Para obter mais informações sobre como usar esses métodos, consulte [Personalizar clusters HDInsight com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie o provisionamento de um cluster usando as etapas em [provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md), mas não conclua o provisionamento.

2. Na seção **configuração opcional** , selecione **ações de script**e forneça as seguintes informações:

   * **Nome**: Insira um nome amigável para a ação de script.

   * **URI do script**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh.

   * **Cabeçalho**: Marque esta opção.

   * **Trabalho**: Marque esta opção.

   * **ZOOKEEPER**: deixe em branco.

   * **Parâmetros**: Insira o endereço WASB para o contêiner e a conta de armazenamento que contém os jars. Por exemplo, **wasb://libs\@mystorage.blob.Core.Windows.net/** .

3. Na parte inferior das **ações de script**, use o botão **selecionar** para salvar a configuração.

4. Na seção **configuração opcional** , selecione **contas de armazenamento vinculadas** e selecione o link **Adicionar uma chave de armazenamento** . Selecione a conta de armazenamento que contém os jars. Em seguida, use os botões **selecionar** para salvar as configurações e retornar a **configuração opcional**.

5. Para salvar a configuração opcional, use o botão **selecionar** na parte inferior da seção de **configuração opcional** .

6. Continue Provisionando o cluster conforme descrito em [provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md).

Quando a criação do cluster for concluída, você poderá usar os jars adicionados por meio desse script do hive sem precisar usar a instrução `ADD JAR`.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como trabalhar com o Hive, confira [usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
