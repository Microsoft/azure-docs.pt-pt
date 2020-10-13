---
title: Modelo - HdInsight cluster com data lake storage gen1
description: Utilize modelos de Gestor de Recursos Azure para criar e utilizar clusters Azure HDInsight com Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 2a0471055e4648944aa07d10fef67f5e7235a76b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856929"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Crie um cluster HDInsight com Azure Data Lake Storage Gen1 usando o modelo de Gestor de Recursos Azure
> [!div class="op_single_selector"]
> * [Usando o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilização do PowerShell (para armazenamento predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilização do PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utilização do Gestor de Recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar o Azure PowerShell para configurar um cluster HDInsight com a Azure Data Lake Storage Gen1, **como armazenamento adicional**.

Para tipos de cluster suportados, a Data Lake Storage Gen1 pode ser usada como um armazenamento predefinido ou como uma conta de armazenamento adicional. Quando a Data Lake Storage Gen1 é utilizada como armazenamento adicional, a conta de armazenamento padrão para os clusters continuará a ser o armazenamento do Azure Blob (WASB) e os ficheiros relacionados com o cluster (tais como registos, etc.) ainda estão escritos para o armazenamento padrão, enquanto os dados que pretende processar podem ser armazenados numa conta da Data Lake Storage Gen1. A utilização da Data Lake Storage Gen1 como uma conta de armazenamento adicional não afeta o desempenho ou a capacidade de ler/escrever para o armazenamento a partir do cluster.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Utilização de Data Lake Storage Gen1 para armazenamento de cluster HDInsight

Aqui estão algumas considerações importantes para a utilização de HDInsight com Data Lake Storage Gen1:

* A opção de criar clusters HDInsight com acesso ao Data Lake Storage Gen1 como armazenamento padrão está disponível para a versão 3.5 e 3.6 do HDInsight.

* A opção de criar clusters HDInsight com acesso a Data Lake Storage Gen1 como armazenamento adicional está disponível para as versões HDInsight 3.2, 3.4, 3.5 e 3.6.

Neste artigo, disponibilizamos um cluster Hadoop com data lake storage gen1 como armazenamento adicional. Para obter instruções sobre como criar um cluster Hadoop com data lake storage gen1 como armazenamento padrão, consulte [Criar um cluster HDInsight com data lake storage gen1 usando o portal Azure](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de começar este tutorial, tem de ter o seguinte:

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/).
* **Azure Ative Directory Service Principal**. Passos neste tutorial fornecem instruções sobre como criar um diretor de serviço em Azure AD. No entanto, você deve ser um administrador AD Azure para ser capaz de criar um principal serviço. Se você é um administrador AD Azure, você pode saltar este pré-requisito e proceder com o tutorial.

    **Se não for administrador da AD Azure,** não poderá executar os passos necessários para criar um diretor de serviço. Neste caso, o seu administrador AD Azure deve primeiro criar um principal serviço antes de poder criar um cluster HDInsight com data lake storage gen1. Além disso, o principal de serviço deve ser criado com um certificado, conforme descrito na [Create a service principal com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Crie um cluster HDInsight com data lake storage gen1
O modelo de Gestor de Recursos, e os pré-requisitos para a utilização do modelo, estão disponíveis no GitHub no [Deploy a HDInsight Linux cluster com o novo Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Siga as instruções fornecidas neste link para criar um cluster HDInsight com data lake storage gen1 como armazenamento adicional.

As instruções no link acima mencionado requerem PowerShell. Antes de começar com essas instruções, certifique-se de iniciar sessão na sua conta Azure. A partir do seu ambiente de trabalho, abra uma nova janela Azure PowerShell e introduza os seguintes cortes. Quando solicitado para iniciar sessão, certifique-se de que faz login como um dos administradores/proprietário de subscrição:

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
* [Microsoft.Storage/storageAcontas](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Faça upload de dados de amostra para data lake storage gen1
O modelo de Gestor de Recursos cria uma nova conta de armazenamento com data lake storage gen1 e associa-a ao cluster HDInsight. Deve agora enviar alguns dados da amostra para a Data Lake Storage Gen1. Você precisará destes dados mais tarde no tutorial para executar empregos de um cluster HDInsight que acede a dados na conta de armazenamento com Data Lake Storage Gen1. Para obter instruções sobre como fazer o upload de dados, consulte [o Upload de um ficheiro para data lake storage gen1](data-lake-store-get-started-portal.md#uploaddata). Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Definir ACLs relevantes nos dados da amostra
Para se certificar de que os dados da amostra que envia estão acessíveis a partir do cluster HDInsight, deve certificar-se de que a aplicação AD Azure que é utilizada para estabelecer a identidade entre o cluster HDInsight e a Data Lake Storage Gen1 tem acesso ao ficheiro/pasta a que está a tentar aceder. Para isso, execute os seguintes passos.

1. Encontre o nome da aplicação AD Azure que está associada ao cluster HDInsight e à conta de armazenamento com data lake storage gen1. Uma maneira de procurar o nome é abrir a lâmina de cluster HDInsight que criou usando o modelo de Gestor de Recursos, clicar no **separador de identidade AD Azure cluster** e procurar o valor do Nome principal de exibição do **serviço.**
2. Agora, forneça acesso a esta aplicação AD Azure no ficheiro/pasta a que pretende aceder a partir do cluster HDInsight. Para definir os ACLs certos no ficheiro/pasta na Data Lake Storage Gen1, consulte [os dados de segurança na Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Executar trabalhos de teste no cluster HDInsight para usar data lake storage gen1
Depois de configurar um cluster HDInsight, pode executar trabalhos de teste no cluster para testar que o cluster HDInsight pode aceder à Data Lake Storage Gen1. Para tal, vamos executar uma amostra de trabalho da Hive que cria uma tabela usando os dados da amostra que você carregou anteriormente para a sua conta de armazenamento com data lake storage gen1.

Nesta secção, você SSH em um cluster HDInsight Linux e executar a consulta de hive amostra. Se estiver a utilizar um cliente Windows, recomendamos a utilização de **PuTTY,** que pode ser descarregado a partir de [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

Para obter mais informações sobre a utilização do PuTTY, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Uma vez ligado, inicie o CLI da Colmeia utilizando o seguinte comando:

   ```
   hive
   ```
2. Utilizando o CLI, introduza as seguintes declarações para criar uma nova tabela com o nome **de veículos** utilizando os dados da amostra na Data Lake Storage Gen1:

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


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Acesso Data Lake Storage Gen1 usando comandos HDFS
Uma vez configurado o cluster HDInsight para utilizar a Data Lake Storage Gen1, pode utilizar os comandos de concha HDFS para aceder à loja.

Nesta secção, você SSH em um cluster HDInsight Linux e executar os comandos HDFS. Se estiver a utilizar um cliente Windows, recomendamos a utilização de **PuTTY,** que pode ser descarregado a partir de [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

Para obter mais informações sobre a utilização do PuTTY, consulte [Use SSH com Hadoop baseado em Linux no HDInsight a partir do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Uma vez ligado, utilize o seguinte comando do sistema de ficheiros HDFS para listar os ficheiros na conta de armazenamento com a Data Lake Storage Gen1.

```
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

Isto deve listar o ficheiro que fez o upload anterior para a Data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Também pode usar o `hdfs dfs -put` comando para enviar alguns ficheiros para data lake storage gen1 e, em seguida, usar `hdfs dfs -ls` para verificar se os ficheiros foram carregados com sucesso.


## <a name="next-steps"></a>Passos seguintes
* [Copiar dados de Azure Storage Blobs para Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Use data lake storage Gen1 com clusters Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
