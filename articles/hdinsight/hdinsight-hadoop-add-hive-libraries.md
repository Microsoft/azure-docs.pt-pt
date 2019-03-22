---
title: Adicionar bibliotecas Apache Hive durante a criação do cluster do HDInsight - Azure
description: Saiba como adicionar bibliotecas Apache Hive (ficheiros jar,) para um cluster do HDInsight durante a criação do cluster.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 6001d291581dc317da89cadbf3891e334362062b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897593"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Adicionar bibliotecas de Apache Hive personalizadas, quando criar o cluster do HDInsight

Aprenda a carregar previamente [Apache Hive](https://hive.apache.org/) bibliotecas no HDInsight. Este documento contém informações sobre como utilizar uma ação de Script para pré-carregar bibliotecas durante a criação do cluster. Bibliotecas foi adicionadas com os passos neste documento são globalmente disponíveis no Hive - não é necessário usar [adicionar JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) para carregá-las.

## <a name="how-it-works"></a>Como funciona

Ao criar um cluster, pode utilizar uma ação de Script para modificar nós do cluster quando eles são criados. Este documento, o script aceita um único parâmetro, o que é a localização das bibliotecas. Esta localização tem de ser uma conta de armazenamento do Azure e as bibliotecas devem ser armazenadas como ficheiros jar.

Durante a criação do cluster, o script enumera os arquivos, copia-os para o `/usr/lib/customhivelibs/` diretório em nós principais e de trabalho, em seguida, adiciona-os para o `hive.aux.jars.path` propriedade no `core-site.xml` ficheiro. Em clusters baseados em Linux, também atualiza o `hive-env.sh` ficheiro com a localização dos ficheiros.

> [!NOTE]  
> Utilizar as ações de script neste artigo faz as bibliotecas disponíveis nos seguintes cenários:
>
> * **HDInsight baseado em Linux** - quando utilizar um cliente de Hive **WebHCat**, e **HiveServer2**.
> * **HDInsight baseado em Windows** - quando utilizar o cliente do Hive e **WebHCat**.

## <a name="the-script"></a>O script

**Localização do script**

Para **clusters baseados em Linux**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Para **clusters baseados em Windows**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

**Requisitos**

* Os scripts devem ser aplicados para ambos os **nós principais** e **nós de trabalho**.

* Jars que deseja instalar devem ser armazenados no armazenamento de Blobs do Azure numa **único contentor**.

* A conta de armazenamento que contém a biblioteca de ficheiros jar **tem** ligado ao cluster do HDInsight, durante a criação. Ele deve ser a conta de armazenamento predefinida ou uma conta adicionada através de __configuração opcional__.

* O caminho WASB para o contentor tem de ser especificado como um parâmetro para a ação de Script. Por exemplo, se o Intune é armazenados num contentor com o nome **libs** numa conta de armazenamento com o nome **mystorage**, o parâmetro seria **wasb://libs\@ mystorage.blob.Core.Windows.NET/**.

  > [!NOTE]  
  > Este documento assume que já tiver criado uma conta de armazenamento, o contentor de BLOBs e os ficheiros carregados ao mesmo.
  >
  > Se não tiver criado uma conta de armazenamento, pode fazer por isso, através da [portal do Azure](https://portal.azure.com). Em seguida, pode utilizar um utilitário como [Explorador de armazenamento do Azure](https://storageexplorer.com/) para criar um contentor na conta e carregar ficheiros para o mesmo.

## <a name="create-a-cluster-using-the-script"></a>Criar um cluster com o script

> [!NOTE]  
> Os passos seguintes criam um cluster do HDInsight baseado em Linux. Para criar um cluster baseado em Windows, selecione **Windows** que o sistema operacional ao criar o cluster e a utilização do script dos Windows (PowerShell) em vez do script de bash de cluster.
>
> Também pode utilizar a Azure PowerShell ou o SDK de .NET do HDInsight para criar um cluster com este script. Para obter mais informações sobre como usar esses métodos, consulte [HDInsight personalizar clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar o aprovisionamento de um cluster, utilizando os passos em [Provision HDInsight clusters no Linux](hdinsight-hadoop-provision-linux-clusters.md), mas não concluído o aprovisionamento.

2. Sobre o **configuração opcional** secção, selecione **ações de Script**e forneça as seguintes informações:

   * **NOME**: Introduza um nome amigável para a ação de script.

   * **URI DO SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh.

   * **HEAD**: Marque esta opção.

   * **FUNÇÃO DE TRABALHO**: Marque esta opção.

   * **ZOOKEEPER**: Deixe em branco.

   * **PARÂMETROS**: Introduza o endereço WASB para a conta de armazenamento e de contentor que contém os jars. Por exemplo, **wasb://libs\@mystorage.blob.core.windows.net/**.

3. Na parte inferior a **ações de Script**, utilize o **selecione** botão para guardar a configuração.

4. Sobre o **configuração opcional** secção, selecione **contas de armazenamento ligadas** e selecione o **adicionar uma chave de armazenamento** ligação. Selecione a conta de armazenamento que contém os jars. Em seguida, utilize o **selecionar** botões para guardar as definições e voltar a **configuração opcional**.

5. Para guardar a configuração opcional, utilize o **selecionar** na parte inferior do **configuração opcional** secção.

6. Continuar o aprovisionamento do cluster, conforme descrito em [Provision HDInsight clusters no Linux](hdinsight-hadoop-provision-linux-clusters.md).

Quando concluir a criação do cluster, é possível utilizar o Intune adicionados através deste script do Hive, sem ter de utilizar o `ADD JAR` instrução.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como trabalhar com o Hive, consulte [utilizar o Apache Hive com HDInsight](hadoop/hdinsight-use-hive.md)
