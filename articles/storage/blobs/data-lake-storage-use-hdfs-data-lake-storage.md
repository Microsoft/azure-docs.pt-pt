---
title: Utilizar a CLI do HDFS com o Azure Data Lake Storage Gen2
description: Introdução à CLI do HDFS para a geração 2 do Data Lake Storage
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 167c8b4dff2e6f392dd6608d20d8924d446f95aa
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729938"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Utilizar a CLI do HDFS com o Data Lake Storage Gen2

Geração de armazenamento 2 do Azure Data Lake permite-lhe gerir e aceder aos dados, tal como faria com um [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Se tiver um cluster do HDInsight anexado ou executar uma tarefa de Apache Spark com o Azure Databricks para realizar análises em dados armazenados numa conta de armazenamento do Azure, pode utilizar a interface de linha de comandos (CLI) para recuperar e manipular os dados carregados.

## <a name="hdfs-cli-with-hdinsight"></a>CLI do HDFS com o HDInsight

O HDInsight fornece acesso ao sistema de ficheiros distribuído que está ligado localmente aos nós de computação. Este sistema de ficheiros pode ser acedido através da utilização da shell que interage diretamente com o HDFS e outros sistemas de ficheiros que suporta o Hadoop. Seguem-se os comandos comumente utilizados e os links para recursos úteis.

>[!IMPORTANT]
>Faturação do cluster do HDInsight é iniciado depois de um cluster é criado e termina quando o cluster é eliminado. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado. Para saber como eliminar um cluster, consulte nosso [artigo sobre o tópico](../../hdinsight/hdinsight-delete-cluster.md). No entanto, os dados armazenados numa conta de armazenamento com o Data Lake Storage Gen2 ativada persistir até mesmo depois de eliminar um cluster do HDInsight.

### <a name="create-a-file-system"></a>Criar um sistema de ficheiros

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* Substitua o `<file-system-name>` marcador de posição pelo nome que pretende dar o seu sistema de ficheiros.

* Substitua o `<storage-account-name>` marcador de posição pelo nome da sua conta de armazenamento.

### <a name="get-a-list-of-files-or-directories"></a>Obter uma lista de arquivos ou diretórios

    hdfs dfs -ls <path>

Substitua o `<path>` espaço reservado com o URI do sistema de ficheiros ou pasta de sistema de ficheiros.

Por exemplo: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

### <a name="create-a-directory"></a>Criar um diretório

    hdfs dfs -mkdir [-p] <path>

Substitua o `<path>` espaço reservado com o nome de sistema de ficheiros de raiz ou uma pasta dentro de seu sistema de ficheiros.

Por exemplo: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

### <a name="delete-a-file-or-directory"></a>Eliminar um ficheiro ou diretório

    hdfs dfs -rm <path>

Substitua o `<path>` espaço reservado com o URI do ficheiro ou pasta que pretende eliminar.

Por exemplo: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Utilizar a CLI do HDFS com um cluster de Hadoop do HDInsight no Linux

Em primeiro lugar, estabelecer [acesso remoto aos serviços](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Se escolher [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) o código do PowerShell de exemplo teria o aspeto da seguinte forma:

```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
A cadeia de ligação pode ser encontrada no "SSH + Cluster início de sessão" seção do painel do cluster do HDInsight no portal do Azure. Foram especificadas credenciais SSH no momento da criação do cluster.

Para obter mais informações sobre a CLI do HDFS, consulte a [documentação oficial](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) e o [guia de permissões do HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html). Para saber mais sobre as ACLs no Databricks, veja a [segredos CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli).

## <a name="hdfs-cli-with-azure-databricks"></a>CLI de HDFS com o Azure Databricks

O Databricks fornece uma CLI fácil de usar, criada sobre a API de REST do Databricks. O projeto de código-fonte aberto está hospedado no [GitHub](https://github.com/databricks/databricks-cli). Seguem-se os comandos comumente utilizados.

### <a name="get-a-list-of-files-or-directories"></a>Obter uma lista de arquivos ou diretórios

    dbfs ls [-l]

### <a name="create-a-directory"></a>Criar um diretório

    dbfs mkdirs

### <a name="delete-a-file"></a>Eliminar um ficheiro

    dbfs rm [-r]

Outra forma de interação com o Databricks são blocos de notas. Embora um bloco de notas tenha um idioma primário, pode combinar vários idiomas, especificando o idioma de % de comando mágico de linguagem no início de uma célula. Especificamente, % sh permite-lhe executar o código de shell no seu bloco de notas muito parecido com o exemplo de HDInsight no início deste artigo.

### <a name="get-a-list-of-files-or-directories"></a>Obter uma lista de arquivos ou diretórios

    %sh ls <args>

### <a name="create-a-directory"></a>Criar um diretório

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Eliminar um ficheiro ou diretório

    %sh rm [-skipTrash] URI [URI ...]

Depois de iniciar o cluster do Spark no Azure Databricks, irá criar um novo bloco de notas. O exemplo de script de bloco de notas será semelhante ao seguinte:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

Para obter mais informações sobre a CLI do Databricks, consulte a [documentação oficial](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Para obter mais informações nos blocos de notas, consulte a [blocos de notas](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) secção da documentação.

## <a name="set-file-and-directory-level-permissions"></a>Definir as permissões de nível de ficheiro e de diretório

Definir e obter as permissões de acesso ao nível do ficheiro e de diretório. Eis alguns comandos para começar. 

Para saber mais sobre permissões ao nível ficheiro e de diretório para o sistema de ficheiros de geração 2 do Azure Data Lake, veja [controlo de acesso no Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Exibir o controle de listas de acesso (ACLs) de arquivos e diretórios

    hdfs dfs -getfacl [-R] <path>

Exemplo:

`hdfs dfs -getfacl -R /dir`

Consulte [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

### <a name="set-acls-of-files-and-directories"></a>Definir ACLs de ficheiros e diretórios

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Exemplo:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Consulte [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

### <a name="change-the-owner-of-files"></a>Alterar o proprietário de ficheiros

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Consulte [chown faz parte dos](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

### <a name="change-group-association-of-files"></a>Alterar a associação ao grupo de ficheiros

    hdfs dfs -chgrp [-R] <group> <URI>

See [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

### <a name="change-the-permissions-of-files"></a>Alterar as permissões de ficheiros

    hdfs dfs -chmod [-R] <mode> <URI>

Consulte [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Pode ver a lista completa de comandos na [guia de Shell do sistema de ficheiros Apache Hadoop 2.4.1](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) Web site.

## <a name="next-steps"></a>Passos Seguintes

[Utilizar uma conta com capacidade de geração 2 de armazenamento do Azure Data Lake no Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md) 
