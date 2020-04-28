---
title: Use SCP com Apache Hadoop em Azure HDInsight
description: Este documento fornece informações sobre a ligação ao HDInsight utilizando os comandos SSH e SCP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 559746a817442602c76ba91f12c195be1d7f3cc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188434"
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
* [HDInsight usando o armazenamento](hdinsight-hadoop-use-data-lake-store.md)do lago De dados Azure .

## <a name="next-steps"></a>Passos seguintes

* [Utilizar SSH com o HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node) (Utilizar nós de extremidade no HDInsight)
