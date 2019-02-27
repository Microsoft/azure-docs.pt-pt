---
title: Utilizar modelos do Azure para criar o HDInsight com Gen1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Utilizar modelos Azure Resource Manager para criar e utilizar clusters do HDInsight com Gen1 de armazenamento do Azure Data Lake
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 5e8aa310d518ff26d2d0224797b2feb0745896d1
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56864911"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Criar um cluster do HDInsight com a geração 1 de armazenamento do Azure Data Lake com o modelo Azure Resource Manager
> [!div class="op_single_selector"]
> * [Utilizar o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Com o PowerShell (para o armazenamento predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Com o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Com o Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Saiba como utilizar o Azure PowerShell para configurar um cluster do HDInsight com o Azure Data Lake Storage Gen1, **como armazenamento adicional**.

Para tipos de cluster suportadas, a geração 1 de armazenamento do Data Lake pode ser utilizado como armazenamento predefinido ou conta de armazenamento adicional. Quando Gen1 de armazenamento do Data Lake é utilizado como armazenamento adicional, a conta de armazenamento predefinida para os clusters continuarão a estar Blobs de armazenamento do Azure (WASB) e os ficheiros relacionados com o cluster (por exemplo, registos, etc.) ainda são escritos para o armazenamento predefinido, enquanto os dados que pretende processo pode ser armazenado numa conta de geração 1 de armazenamento do Data Lake. Com a geração 1 de armazenamento do Data Lake como uma conta de armazenamento adicional não afeta desempenho ou a capacidade de leitura/escrita para o armazenamento do cluster.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Utilizar a geração 1 de armazenamento do Data Lake para o armazenamento de cluster do HDInsight

Seguem-se algumas considerações importantes para o HDInsight com geração 1 de armazenamento do Data Lake:

* Opção para criar clusters do HDInsight com acesso ao Data Lake Storage Gen1 como armazenamento predefinido está disponível para o HDInsight versão 3.5 e 3.6.

* Opção para criar clusters do HDInsight com acesso ao Data Lake Storage Gen1 como armazenamento adicional está disponível para versões do HDInsight 3.2, 3.4, 3.5 e 3.6.

Neste artigo, iremos aprovisionar um cluster do Hadoop com o Data Lake Storage Gen1 como armazenamento adicional. Para obter instruções sobre como criar um cluster do Hadoop com o Data Lake Storage Gen1 como armazenamento predefinido, consulte [criar um cluster do HDInsight com a geração 1 de armazenamento do Data Lake através do Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* **Principal de serviço de diretório do Azure Active Directory**. Os passos neste tutorial fornecem instruções sobre como criar um principal de serviço no Azure AD. No entanto, tem de ser um administrador do Azure AD para poder criar um principal de serviço. Se for um administrador do Azure AD, pode ignorar este pré-requisito e continuar o tutorial.

    **Se não for um administrador do Azure AD**, não será capaz de executar os passos necessários para criar um principal de serviço. Nesse caso, o administrador do Azure AD tem de criar um principal de serviço antes de poder criar um cluster do HDInsight com a geração 1 de armazenamento do Data Lake. Além disso, o principal de serviço tem de ser criado usando um certificado, conforme descrito em [criar um principal de serviço com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Criar um cluster do HDInsight com Data Lake Storage Gen1
O modelo do Resource Manager e os pré-requisitos para utilizar o modelo, estão disponíveis no GitHub em [implementar um cluster do Linux de HDInsight com o novo Data Lake Storage geração 1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Siga as instruções fornecidas neste link para criar um cluster do HDInsight com Data Lake Storage Gen1 como armazenamento adicional.

As instruções no link mencionadas acima requerem o PowerShell. Antes de começar com essas instruções, certifique-se de que inicia sessão na sua conta do Azure. Na área de trabalho, abra uma nova janela do PowerShell do Azure e introduza os seguintes fragmentos. Quando lhe for pedido para iniciar sessão, certifique-se de que inicia sessão como um do proprietário/administradores da subscrição:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

O modelo implementa esses tipos de recursos:

* [Microsoft.DataLakeStore/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Carregar dados de exemplo para a geração 1 de armazenamento do Data Lake
O modelo do Resource Manager cria uma nova conta de geração 1 de armazenamento do Data Lake e associa-a com o cluster do HDInsight. Agora tem de carregar alguns dados de exemplo para a geração 1 de armazenamento do Data Lake. Estes dados são necessários mais tarde no tutorial para executar tarefas a partir de um cluster do HDInsight que acedem a dados na conta de geração 1 de armazenamento do Data Lake. Para obter instruções sobre como carregar dados, consulte [carregar um ficheiro à sua conta do Data Lake Storage Gen1](data-lake-store-get-started-portal.md#uploaddata). Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Definir ACLs relevantes nos dados de exemplo
Para certificar-se de que os dados de exemplo que carregar são acessíveis a partir do cluster do HDInsight, tem de garantir que a aplicação do Azure AD que é utilizada para estabelecer a identidade entre o cluster do HDInsight e a geração 1 de armazenamento do Data Lake tem acesso ao ficheiro/pasta que está tentando acesso. Para tal, execute os seguintes passos.

1. Encontre o nome da aplicação do Azure AD que estão associado com o cluster do HDInsight e a conta de geração 1 de armazenamento do Data Lake. Uma forma de ver para o nome é abrir o painel de cluster do HDInsight que criou utilizando o modelo do Resource Manager, clique nas **identidade do AAD de Cluster** separador e procure o valor de **nome a apresentar do Principal de serviço**.
2. Agora, fornece acesso a esta aplicação do Azure AD no ficheiro/pasta que pretende aceder a partir do cluster do HDInsight. Para definir as ACLs corretas no ficheiro/pasta na geração 1 de armazenamento do Data Lake, veja [proteger dados no Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Executar tarefas de teste no cluster do HDInsight para utilizar a geração 1 de armazenamento do Data Lake
Depois de configurar um cluster do HDInsight, pode executar tarefas de teste no cluster para testar que o cluster do HDInsight pode aceder a geração 1 de armazenamento do Data Lake. Para tal, executamos uma tarefa do Hive de exemplo que cria uma tabela com os dados de exemplo carregado anteriormente para a sua conta de geração 1 de armazenamento do Data Lake.

Nesta secção, irá SSH num cluster do HDInsight Linux e execute a uma consulta do Hive de exemplo. Se estiver a utilizar um cliente Windows, recomendamos que utilize **PuTTY**, que pode ser transferido a partir [ http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre como utilizar o PuTTY, veja [utilizar o SSH com Hadoop baseado em Linux no HDInsight do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Assim que estiver ligado, inicie a CLI do Hive com o seguinte comando:

   ```
   hive
   ```
2. Com a CLI, introduza as seguintes instruções para criar uma nova tabela com o nome **veículos** com os dados de exemplo no Data Lake Storage Gen1:

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


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Aceder a geração 1 de armazenamento do Data Lake com comandos do HDFS
Depois de configurar o cluster do HDInsight para utilizar a geração 1 de armazenamento do Data Lake, pode utilizar os comandos da shell HDFS para aceder à loja.

Nesta secção, irá SSH num Linux de HDInsight cluster e execute os comandos HDFS. Se estiver a utilizar um cliente Windows, recomendamos que utilize **PuTTY**, que pode ser transferido a partir [ http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre como utilizar o PuTTY, veja [utilizar o SSH com Hadoop baseado em Linux no HDInsight do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Assim que estiver ligado, utilize o seguinte comando do sistema de ficheiros HDFS para listar os ficheiros na conta de geração 1 de armazenamento do Data Lake.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Isso deve listar o ficheiro que carregou anteriormente para a geração 1 de armazenamento do Data Lake.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Também pode utilizar o `hdfs dfs -put` comando para carregar alguns ficheiros para geração 1 de armazenamento do Data Lake e, em seguida, utilizar `hdfs dfs -ls` para verificar se os ficheiros foram carregados com êxito.


## <a name="next-steps"></a>Passos Seguintes
* [Copiar dados dos Blobs de armazenamento do Azure para a geração 1 de armazenamento do Data Lake](data-lake-store-copy-data-wasb-distcp.md)
* [Utilizar a geração 1 do Data Lake armazenamento com clusters do HDInsight do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
