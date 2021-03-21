---
title: Use SCP com Apache Hadoop em Azure HDInsight
description: Este documento fornece informações sobre a ligação ao HDInsight utilizando os comandos ssh e scp.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 927b8c55008c3e01d8ff1dd09c46cfa3c6618026
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946281"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>Use SCP com Apache Hadoop em Azure HDInsight

Este artigo fornece informações sobre a transferência segura de ficheiros com o seu cluster HDInsight.

## <a name="copy-files"></a>Copiar ficheiros

O utilitário `scp` pode ser utilizado para copiar ficheiros de e para os nós individuais do cluster. Por exemplo, o comando seguinte copia o diretório `test.txt` do sistema local para o nó principal primário:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Uma vez que não foi especificado nenhum caminho após `:`, o ficheiro é colocado no diretório raiz `sshuser`.

O exemplo seguinte copia o ficheiro `test.txt` do diretório raiz `sshuser` no nó principal primário para o sistema local:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

`scp` apenas pode aceder ao sistema de ficheiros de nós individuais dentro do cluster. Não pode ser usado para aceder a dados no armazenamento compatível com HDFS para o cluster.

Utilize `scp` quando tiver de carregar um recurso para utilização a partir de uma sessão SSH. Por exemplo, carregue um script de Python e, em seguida, execute o script a partir de uma sessão SSH.

Para obter informações sobre o carregamento de dados diretamente para o armazenamento compatível com HDFS, consulte os seguintes documentos:

* [HDInsight com o Armazenamento do Azure](hdinsight-hadoop-use-blob-storage.md).
* [HDInsight usando Azure Data Lake Storage Gen1](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md).

## <a name="next-steps"></a>Passos seguintes

* [Utilizar SSH com o HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node) (Utilizar nós de extremidade no HDInsight)
