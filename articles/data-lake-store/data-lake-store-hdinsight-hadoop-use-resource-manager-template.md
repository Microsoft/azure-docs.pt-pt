---
title: Utilize modelos Azure para criar HDInsight com Azure Data Lake Storage Gen1 [ Microsoft Docs
description: Utilize modelos do Gestor de Recursos Azure para criar e utilizar clusters HDInsight com O Armazenamento de Lagos Azure Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b09ca2cc358107c5f95fe3426351d380380db3c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66161378"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Crie um cluster HDInsight com Azure Data Lake Storage Gen1 usando o modelo de Gestor de Recursos Azure
> [!div class="op_single_selector"]
> * [Usando o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilização do PowerShell (para armazenamento predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilização do PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usando o Gestor de Recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar o Azure PowerShell para configurar um cluster HDInsight com o Azure Data Lake Storage Gen1, **como armazenamento adicional**.

Para tipos de cluster suportados, o Data Lake Storage Gen1 pode ser usado como uma conta de armazenamento padrão ou adicional. Quando o Data Lake Storage Gen1 é usado como armazenamento adicional, a conta de armazenamento padrão para os clusters continuará a ser Blobs de Armazenamento Azure (WASB) e os ficheiros relacionados com o cluster (como registos, etc.) ainda estão escritos para o armazenamento predefinido, enquanto os dados que pretende o processo pode ser armazenado numa conta Gen1 de Armazenamento de Data Lake. A utilização do Data Lake Storage Gen1 como uma conta de armazenamento adicional não afeta o desempenho nem a capacidade de ler/escrever para o armazenamento do cluster.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Utilização de Data Lake Storage Gen1 para armazenamento de cluster HDInsight

Aqui estão algumas considerações importantes para usar o HDInsight com data lake storage Gen1:

* Opção de criar clusters HDInsight com acesso a Data Lake Storage Gen1 como armazenamento padrão está disponível para os versões 3.5 e 3.6 da HDInsight.

* Opção de criar clusters HDInsight com acesso a Data Lake Storage Gen1 como armazenamento adicional está disponível para as versões HDInsight 3.2, 3.4, 3.5 e 3.6.

Neste artigo, disponibilizamos um cluster Hadoop com Data Lake Storage Gen1 como armazenamento adicional. Para obter instruções sobre como criar um cluster Hadoop com data lake storage Gen1 como armazenamento padrão, consulte [Criar um cluster HDInsight com Data Lake Storage Gen1 usando o Portal Azure](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* **Azure Ative Diretor de Serviço**de Diretório Ativo. Os passos neste tutorial fornecem instruções sobre como criar um diretor de serviço em Azure AD. No entanto, deve ser administrador da AD Azure para poder criar um diretor de serviço. Se for administrador da AD Azure, pode ignorar este pré-requisito e prosseguir com o tutorial.

    Se não for administrador da **AD Azure,** não poderá executar os passos necessários para criar um diretor de serviço. Neste caso, o seu administrador Azure AD deve primeiro criar um diretor de serviço antes de poder criar um cluster HDInsight com data Lake Storage Gen1. Além disso, o diretor de serviço deve ser criado com um certificado, conforme descrito na [Create a service principal com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Crie um cluster HDInsight com Data Lake Storage Gen1
O modelo de Gestor de Recursos, e os pré-requisitos para a utilização do modelo, estão disponíveis no GitHub em [Deploy um cluster HDInsight Linux com o novo Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Siga as instruções fornecidas neste link para criar um cluster HDInsight com data Lake Storage Gen1 como armazenamento adicional.

As instruções no link acima mencionado requerem PowerShell. Antes de começar com essas instruções, certifique-se de fazer login na sua conta Azure. A partir do seu ambiente de trabalho, abra uma nova janela Azure PowerShell e introduza os seguintes cortes. Quando for solicitado para iniciar sessão, certifique-se de que faz login como um dos administradores/proprietários de subscrição:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

O modelo implementa estes tipos de recursos:

* [Microsoft.DataLakeStore/contas](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Enviar dados da amostra para Data Lake Storage Gen1
O modelo de Gestor de Recursos cria uma nova conta Gen1 de Armazenamento de Data Lake e associa-a ao cluster HDInsight. Agora deve enviar alguns dados da amostra para data Lake Storage Gen1. Você precisará destes dados mais tarde no tutorial para executar trabalhos de um cluster HDInsight que acede maçados na conta Data Lake Storage Gen1. Para obter instruções sobre como carregar dados, consulte [o upload de um ficheiro para a sua conta Data Lake Storage Gen1](data-lake-store-get-started-portal.md#uploaddata). Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Definir ACLs relevantes nos dados da amostra
Para se certificar de que os dados da amostra que envia estão acessíveis a partir do cluster HDInsight, deve certificar-se de que a aplicação Azure AD que é usada para estabelecer identidade entre o cluster HDInsight e data Lake Storage Gen1 tem acesso ao ficheiro/pasta a que está a tentar aceder. Para isso, execute os seguintes passos.

1. Encontre o nome da aplicação Azure AD que está associada ao cluster HDInsight e à conta Data Lake Storage Gen1. Uma maneira de procurar o nome é abrir a lâmina de cluster HDInsight que criou usando o modelo De Gestor de Recursos, clicar no separador **Identidade Cluster AAD** e procurar o valor do **Nome de Exibição Principal**do Serviço .
2. Agora, forneça acesso a esta aplicação Azure AD no ficheiro/pasta a que pretende aceder a partir do cluster HDInsight. Para definir os ACLs certos no ficheiro/pasta em Data Lake Storage Gen1, consulte [a segurança dos dados no Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Executar trabalhos de teste no cluster HDInsight para usar Data Lake Storage Gen1
Depois de configurar um cluster HDInsight, pode executar trabalhos de teste no cluster para testar que o cluster HDInsight pode aceder ao Data Lake Storage Gen1. Para tal, vamos executar uma amostra de trabalho da Hive que cria uma tabela usando os dados da amostra que você carregou anteriormente para a sua conta Data Lake Storage Gen1.

Nesta secção, você irá SSH em um cluster HDInsight Linux e executar uma consulta de hive de amostra. Se estiver a utilizar um cliente Windows, recomendamos a [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)utilização de **PuTTY,** que pode ser descarregado a partir de .

Para obter mais informações sobre a utilização do PuTTY, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Uma vez ligado, inicie o CLI da Colmeia utilizando o seguinte comando:

   ```
   hive
   ```
2. Utilizando o CLI, introduza as seguintes declarações para criar uma nova tabela de **veículos nomeados** utilizando os dados da amostra no Data Lake Storage Gen1:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Deverá ver um resultado semelhante ao seguinte:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Aceder ao Data Lake Storage Gen1 utilizando comandos HDFS
Depois de configurar o cluster HDInsight para utilizar data lake storage Gen1, pode utilizar os comandos de concha HDFS para aceder à loja.

Nesta secção, irá sSH num cluster HDInsight Linux e executará os comandos HDFS. Se estiver a utilizar um cliente Windows, recomendamos a [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)utilização de **PuTTY,** que pode ser descarregado a partir de .

Para obter mais informações sobre a utilização do PuTTY, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Uma vez ligado, utilize o seguinte comando do sistema de ficheiros HDFS para listar os ficheiros na conta Data Lake Storage Gen1.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Isto deve listar o ficheiro que fez o upload anteriormente para data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Também pode usar `hdfs dfs -put` o comando para enviar alguns ficheiros `hdfs dfs -ls` para data Lake Storage Gen1, e depois usar para verificar se os ficheiros foram carregados com sucesso.


## <a name="next-steps"></a>Passos seguintes
* [Copiar dados de Blobs de Armazenamento Azure para Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Utilize data lake storage Gen1 com clusters Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
