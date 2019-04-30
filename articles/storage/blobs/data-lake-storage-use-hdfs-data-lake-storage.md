---
title: Utilizar a CLI do HDFS com o Azure Data Lake Storage Gen2
description: Introdução à CLI do HDFS para a geração 2 do Data Lake Storage
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: d1c9eff08a7b9cc50ccdca4ce798ac4d0f3d35f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481790"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Utilizar a CLI do HDFS com o Data Lake Storage Gen2

Pode aceder e gerir os dados na sua conta de armazenamento, utilizando uma interface de linha de comando, tal como faria com um [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Este artigo fornece alguns exemplos que lhe ajudarão a começar a utilizar.

O HDInsight fornece acesso ao sistema de ficheiros distribuído que está ligado localmente aos nós de computação. Pode aceder a este sistema de ficheiros utilizando a shell que interage diretamente com o HDFS e os outros sistemas de ficheiros que suporta o Hadoop.

Para obter mais informações sobre a CLI do HDFS, consulte a [documentação oficial](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) e o [guia de permissões do HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Se estiver a utilizar o Azure Databricks em vez do HDInsight e pretende interagir com os seus dados através de uma interface de linha de comando, pode utilizar a CLI do Databricks para interagir com o sistema de ficheiros de Databricks. Ver [CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Utilizar a CLI do HDFS com um cluster de Hadoop do HDInsight no Linux

Em primeiro lugar, estabelecer [acesso remoto aos serviços](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Se escolher [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) o código do PowerShell de exemplo teria o aspeto da seguinte forma:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
A cadeia de ligação pode ser encontrada no "SSH + Cluster início de sessão" seção do painel do cluster do HDInsight no portal do Azure. Foram especificadas credenciais SSH no momento da criação do cluster.

>[!IMPORTANT]
>Faturação do cluster do HDInsight é iniciado depois de um cluster é criado e termina quando o cluster é eliminado. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado. Para saber como eliminar um cluster, consulte nosso [artigo sobre o tópico](../../hdinsight/hdinsight-delete-cluster.md). No entanto, os dados armazenados numa conta de armazenamento com o Data Lake Storage Gen2 ativada persistir até mesmo depois de eliminar um cluster do HDInsight.

## <a name="create-a-file-system"></a>Criar um sistema de ficheiros

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* Substitua o `<file-system-name>` marcador de posição pelo nome que pretende dar o seu sistema de ficheiros.

* Substitua o `<storage-account-name>` marcador de posição pelo nome da sua conta de armazenamento.

## <a name="get-a-list-of-files-or-directories"></a>Obter uma lista de arquivos ou diretórios

    hdfs dfs -ls <path>

Substitua o `<path>` espaço reservado com o URI do sistema de ficheiros ou pasta de sistema de ficheiros.

Por exemplo: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Criar um diretório

    hdfs dfs -mkdir [-p] <path>

Substitua o `<path>` espaço reservado com o nome de sistema de ficheiros de raiz ou uma pasta dentro de seu sistema de ficheiros.

Por exemplo: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Eliminar um ficheiro ou diretório

    hdfs dfs -rm <path>

Substitua o `<path>` espaço reservado com o URI do ficheiro ou pasta que pretende eliminar.

Por exemplo: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Exibir o controle de listas de acesso (ACLs) de arquivos e diretórios

    hdfs dfs -getfacl [-R] <path>

Exemplo:

`hdfs dfs -getfacl -R /dir`

Consulte [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Definir ACLs de ficheiros e diretórios

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Exemplo:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Consulte [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Alterar o proprietário de ficheiros

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Consulte [chown faz parte dos](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Alterar a associação ao grupo de ficheiros

    hdfs dfs -chgrp [-R] <group> <URI>

See [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Alterar as permissões de ficheiros

    hdfs dfs -chmod [-R] <mode> <URI>

Consulte [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Pode ver a lista completa de comandos na [guia de Shell do sistema de ficheiros Apache Hadoop 2.4.1](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) Web site.

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar uma conta com capacidade de geração 2 de armazenamento do Azure Data Lake no Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Saiba mais sobre listas de controlo de acesso em arquivos e diretórios](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
