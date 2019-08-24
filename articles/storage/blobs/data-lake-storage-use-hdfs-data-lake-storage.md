---
title: Usando a CLI do HDFS com Azure Data Lake Storage Gen2
description: Introdução à CLI do HDFS para Data Lake Storage Gen2
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d5313f3f0fff128dd09f9c9857b7dd9921ea4f8
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992210"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Usando a CLI do HDFS com Data Lake Storage Gen2

Você pode acessar e gerenciar os dados em sua conta de armazenamento usando uma interface de linha de comando, assim como faria com um [sistema de arquivos distribuído do Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Este artigo fornece alguns exemplos que irão ajudá-lo a começar.

O HDInsight fornece acesso ao contêiner distribuído que está anexado localmente aos nós de computação. Você pode acessar esse contêiner usando o shell que interage diretamente com o HDFS e com os outros sistemas de arquivos aos quais o Hadoop dá suporte.

Para obter mais informações sobre a CLI do HDFS, consulte a [documentação oficial](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) e o [Guia de permissões do HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Se você estiver usando Azure Databricks em vez do HDInsight e desejar interagir com seus dados usando uma interface de linha de comando, poderá usar a CLI do databricks para interagir com o sistema de arquivos do databricks. Consulte a [CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)do databricks.

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Usar a CLI do HDFS com um cluster HDInsight Hadoop no Linux

Primeiro, estabeleça o [acesso remoto aos serviços](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Se você escolher [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) , o código do PowerShell de exemplo será semelhante ao seguinte:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
A cadeia de conexão pode ser encontrada na seção "SSH + logon de cluster" da folha do cluster HDInsight em portal do Azure. As credenciais SSH foram especificadas no momento da criação do cluster.

>[!IMPORTANT]
>A cobrança do cluster HDInsight começa depois que um cluster é criado e é interrompido quando o cluster é excluído. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado. Para saber como excluir um cluster, consulte nosso [artigo sobre o tópico](../../hdinsight/hdinsight-delete-cluster.md). No entanto, os dados armazenados em uma conta de armazenamento com Data Lake Storage Gen2 habilitado persistem mesmo depois que um cluster HDInsight é excluído.

## <a name="create-a-container"></a>Criar um contentor

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/

* Substitua o `<container-name>` espaço reservado pelo nome que você deseja dar ao seu contêiner.

* Substitua o `<storage-account-name>` espaço reservado pelo nome da sua conta de armazenamento.

## <a name="get-a-list-of-files-or-directories"></a>Obter uma lista de arquivos ou diretórios

    hdfs dfs -ls <path>

Substitua o `<path>` espaço reservado pelo URI do contêiner ou da pasta do contêiner.

Por exemplo: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Criar um diretório

    hdfs dfs -mkdir [-p] <path>

Substitua o `<path>` espaço reservado pelo nome do contêiner raiz ou por uma pasta dentro do contêiner.

Por exemplo: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Excluir um arquivo ou diretório

    hdfs dfs -rm <path>

Substitua o `<path>` espaço reservado pelo URI do arquivo ou da pasta que você deseja excluir.

Por exemplo: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Exibir as listas de controle de acesso (ACLs) de arquivos e diretórios

    hdfs dfs -getfacl [-R] <path>

Exemplo:

`hdfs dfs -getfacl -R /dir`

Consulte [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Definir ACLs de arquivos e diretórios

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Exemplo:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Consulte [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Alterar o proprietário dos arquivos

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Ver [proprietário](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown) da

## <a name="change-group-association-of-files"></a>Alterar Associação de grupo de arquivos

    hdfs dfs -chgrp [-R] <group> <URI>

Consulte [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Alterar as permissões de arquivos

    hdfs dfs -chmod [-R] <mode> <URI>

Consulte [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Você pode exibir a lista completa de comandos no site [Apache Hadoop guia do shell do sistema de arquivos 2.4.1](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) .

## <a name="next-steps"></a>Passos seguintes

* [Use uma conta compatível com Azure Data Lake Storage Gen2 no Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Saiba mais sobre as listas de controle de acesso em arquivos e diretórios](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
