---
title: Gerir clusters do Apache Hadoop com CLI clássica do Azure - Azure HDInsight
description: Saiba como utilizar a CLI clássica do Azure para gerir clusters do Apache Hadoop no HDInsight do Azure.
services: hdinsight
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: tyfox
ms.openlocfilehash: 61be4088034d8cc557c67c5b6de4972766654a39
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202389"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-using-the-azure-classic-cli"></a>Gerir clusters do Apache Hadoop no HDInsight com a CLI clássica do Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como utilizar o [CLI clássica do Azure](../cli-install-nodejs.md) para gerir [Apache Hadoop](https://hadoop.apache.org/) clusters no Azure HDInsight. A CLI clássica é implementada no node. js. Pode ser utilizado em qualquer plataforma que suporte Node.js, incluindo Windows, Mac e Linux.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **CLI clássica do Azure** -veja [instalar e configurar a CLI clássica do Azure](../cli-install-nodejs.md) para obter informações de instalação e configuração.
* **Ligar ao Azure**, com o seguinte comando:

    ```cli
    azure login
    ```
  
    Para obter mais informações sobre a autenticação através de uma conta escolar ou profissional, consulte [ligar a uma subscrição do Azure a partir da CLI clássica do Azure](/cli/azure/authenticate-azure-cli).
* **Mude para o modo Azure Resource Manager**, utilizando o seguinte comando:
  
    ```cli
    azure config mode arm
    ```

Para obter ajuda, utilize o **-h** mudar.  Por exemplo:

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Criar clusters com a CLI
Ver [criar clusters no HDInsight com a CLI do Azure clássico](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Listar e mostrar detalhes do cluster
Utilize os seguintes comandos para listar e mostrar detalhes do cluster:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Vista de linha de comandos de lista de clusters][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Eliminar clusters
Utilize o seguinte comando para eliminar um cluster:

```cli
azure hdinsight cluster delete <Cluster Name>
```

Também pode eliminar um cluster ao eliminar o grupo de recursos que contém o cluster. Tenha em atenção, esta ação irá eliminar todos os recursos no grupo, incluindo a conta de armazenamento predefinida.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Dimensionar clusters
Para alterar o tamanho de cluster do Apache Hadoop:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Ativar/desativar o acesso HTTP para um cluster

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu como efetuar diferentes tarefas administrativas de cluster HDInsight. Para obter mais informações, consulte os artigos seguintes:

* [Gerir clusters do Apache Hadoop no HDInsight com o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight com o Azure PowerShell][hdinsight-admin-powershell]
* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)][hdinsight-get-started]
* [Como utilizar a CLI clássica do Azure][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Listar e Mostrar clusters"
