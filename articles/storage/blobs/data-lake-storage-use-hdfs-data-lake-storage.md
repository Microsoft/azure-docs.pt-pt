---
title: Utilização do HDFS CLI com Azure Data Lake Storage Gen2
description: Introdução ao HDFS CLI para Data Lake Storage Gen2
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d5313f3f0fff128dd09f9c9857b7dd9921ea4f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "69992210"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Utilização do HDFS CLI com Data Lake Storage Gen2

Pode aceder e gerir os dados na sua conta de armazenamento utilizando uma interface de linha de comando tal como faria com um Sistema de [Ficheiros Distribuídos Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Este artigo fornece alguns exemplos que o ajudarão a começar.

O HDInsight fornece acesso ao recipiente distribuído que está ligado localmente aos nódosos da computação. Pode aceder a este recipiente utilizando a concha que interage diretamente com o HDFS e os outros sistemas de ficheiros que hadoop suporta.

Para mais informações sobre o HDFS CLI, consulte a [documentação oficial](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) e o Guia de [Permissões HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Se estiver a utilizar os Bricks Azure em vez do HDInsight, e pretender interagir com os seus dados utilizando uma interface de linha de comando, pode utilizar o CLI de Databricks para interagir com o sistema de ficheiros Databricks. Ver [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Utilize o HDFS CLI com um cluster HDInsight Hadoop em Linux

Primeiro, estabelecer [acesso remoto aos serviços.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services) Se escolher o [SSH,](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) o código PowerShell da amostra será o seguinte:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
A cadeia de ligação pode ser encontrada na secção "SSH + Cluster login" da lâmina de cluster HDInsight no portal Azure. As credenciais SSH foram especificadas no momento da criação do cluster.

>[!IMPORTANT]
>A faturação do cluster HDInsight começa após a criação de um cluster e para quando o cluster é eliminado. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado. Para aprender a apagar um cluster, consulte o nosso [artigo sobre o tema](../../hdinsight/hdinsight-delete-cluster.md). No entanto, os dados armazenados numa conta de armazenamento com data Lake Storage Gen2 ativado persistem mesmo depois de um cluster HDInsight ser eliminado.

## <a name="create-a-container"></a>Criar um contentor

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/

* Substitua `<container-name>` o espaço reservado pelo nome que pretende dar ao seu recipiente.

* Substitua `<storage-account-name>` o espaço reservado com o nome da sua conta de armazenamento.

## <a name="get-a-list-of-files-or-directories"></a>Obtenha uma lista de ficheiros ou diretórios

    hdfs dfs -ls <path>

Substitua `<path>` o espaço reservado pelo URI do recipiente ou da pasta do contentor.

Por exemplo: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Criar um diretório

    hdfs dfs -mkdir [-p] <path>

Substitua `<path>` o espaço reservado pelo nome do recipiente de raiz ou por uma pasta dentro do seu recipiente.

Por exemplo: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Eliminar um ficheiro ou diretório

    hdfs dfs -rm <path>

Substitua `<path>` o espaço reservado pelo URI do ficheiro ou pasta que pretende eliminar.

Por exemplo: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Mostrar as Listas de Controlo de Acesso (ACLs) de ficheiros e diretórios

    hdfs dfs -getfacl [-R] <path>

Exemplo:

`hdfs dfs -getfacl -R /dir`

Ver [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Definir ACLs de ficheiros e diretórios

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Exemplo:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Ver [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Alterar o proprietário dos ficheiros

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Ver [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Alterar associação de grupo supor ficheiros

    hdfs dfs -chgrp [-R] <group> <URI>

Ver [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Alterar as permissões dos ficheiros

    hdfs dfs -chmod [-R] <mode> <URI>

Ver [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Pode ver a lista completa de comandos no Website do Guia de Membrana do Sistema de [Ficheiros Apache Hadoop 2.4.1.](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)

## <a name="next-steps"></a>Passos seguintes

* [Utilize uma conta de armazenamento de lagos de dados Azure Gen2 capaz em Tijolos de Dados Azure](./data-lake-storage-quickstart-create-databricks-account.md)

* [Conheça as listas de controlo de acesso em ficheiros e diretórios](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
