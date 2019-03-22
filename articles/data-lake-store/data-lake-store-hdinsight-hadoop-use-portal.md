---
title: Utilizar o portal do Azure para criar clusters do HDInsight do Azure com Gen1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Utilizar o portal do Azure para criar e utilizar clusters do HDInsight com Gen1 de armazenamento do Azure Data Lake
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 25e4c308b55b66038be6825a239f185e5e813af5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085767"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Criar clusters do HDInsight com Gen1 de armazenamento do Azure Data Lake com o portal do Azure
> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilize o PowerShell (para o armazenamento predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilize o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utilize o Gestor de recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Saiba como utilizar o portal do Azure para criar um cluster do HDInsight com uma conta de geração 1 de armazenamento do Azure Data Lake, como o armazenamento predefinido ou um armazenamento adicional. Apesar de armazenamento adicional é opcional para um cluster do HDInsight, é recomendado para armazenar os dados de negócio nas contas de armazenamento adicional.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, certifique-se de que cumpriu os seguintes requisitos:

* **Uma subscrição do Azure**. Aceda a [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Data Lake Storage Gen1**. Siga as instruções em [introdução ao Gen1 de armazenamento do Azure Data Lake com o portal do Azure](data-lake-store-get-started-portal.md). Também tem de criar uma pasta raiz na conta.  Neste tutorial, uma pasta de raiz denominada __/clusters__ é utilizado.
* **Um principal de serviço do Azure Active Directory**. Este tutorial fornece instruções sobre como criar um serviço principal no Azure Active Directory (Azure AD). No entanto, para criar um principal de serviço, tem de ser um administrador do Azure AD. Se for um administrador, pode ignorar este pré-requisito e continuar o tutorial.

    >[!NOTE]
    >Pode criar um principal de serviço somente se for administrador do Azure AD. Administrador do Azure AD tem de criar um serviço principal antes de poder criar um cluster do HDInsight com a geração 1 de armazenamento do Data Lake. Além disso, o principal de serviço tem de ser criado com um certificado, conforme descrito em [criar um principal de serviço com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Criar um cluster do HDInsight

Nesta secção, vai criar um cluster do HDInsight com contas de geração 1 de armazenamento do Data Lake como a predefinição ou o armazenamento adicional. Este artigo concentra-se apenas a parte da configuração de contas de geração 1 de armazenamento do Data Lake.  Para as informações de criação de cluster geral e procedimentos, consulte [criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Criar um cluster com geração 1 de armazenamento do Data Lake como armazenamento predefinido

**Para criar um cluster do HDInsight com uma conta do Data Lake Storage Gen1, como a conta de armazenamento predefinida**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Siga [criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter as informações gerais sobre a criação de clusters do HDInsight.
3. Sobre o **armazenamento** painel, em **tipo de armazenamento primário**, selecione **Gen1 de armazenamento do Azure Data Lake**e, em seguida, introduza as seguintes informações:

    ![Adicionar principal de serviço para o cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "principal de serviço de adicionar ao cluster do HDInsight")

    - **Conta de selecionar Data Lake Store**: Selecione uma conta de geração 1 do Data Lake armazenamento existente. É necessária uma conta de geração 1 de armazenamento do Data Lake existente.  Veja [Pré-requisitos](#prerequisites).
    - **Caminho da raiz**: Introduza um caminho em que os arquivos específicos do cluster devem ser armazenados. Na captura de ecrã, é __/clusters/myhdiadlcluster/__, na qual o __/clusters__ pasta tem de existir e o Portal cria *myhdicluster* pasta.  O *myhdicluster* é o nome do cluster.
    - **Acesso do Data Lake Store**: Configure o acesso entre a conta de geração 1 do Data Lake Storage e o cluster do HDInsight. Para obter instruções, consulte Configurar Gen1 de armazenamento do Data Lake acesso.
    - **Contas de armazenamento adicionais**: Adicione contas de armazenamento do Azure como contas de armazenamento adicional para o cluster. Para adicionar mais contas de geração 1 de armazenamento do Data Lake é feito ao dar as permissões de cluster nos dados de mais contas de geração 1 de armazenamento do Data Lake ao configurar uma conta de geração 1 de armazenamento do Data Lake como o tipo de armazenamento primário. Acesso de armazenamento Gen1 consulte Configurar o Data Lake.

4. Sobre o **acesso do Data Lake Store**, clique em **selecione**e, em seguida, continuar com a criação do cluster, conforme descrito na [criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Criar um cluster com geração 1 de armazenamento do Data Lake como armazenamento adicional

As instruções seguintes criar um cluster do HDInsight com uma conta de armazenamento do Azure como armazenamento predefinido e uma conta de geração 1 de armazenamento do Data Lake como um armazenamento adicional.

**Para criar um cluster do HDInsight com uma conta de geração 1 de armazenamento do Data Lake como uma conta de armazenamento adicional**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Siga [criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter as informações gerais sobre a criação de clusters do HDInsight.
3. Sobre o **armazenamento** painel, em **tipo de armazenamento primário**, selecione **armazenamento do Azure**e, em seguida, introduza as seguintes informações:

    ![Adicionar principal de serviço para o cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "principal de serviço de adicionar ao cluster do HDInsight")

    - **Método de seleção**: Utilize uma das seguintes opções:

        * Para especificar uma conta de armazenamento que faz parte da sua subscrição do Azure, selecione **minhas inscrições**e, em seguida, selecione a conta de armazenamento.
        * Para especificar uma conta de armazenamento que está fora da sua subscrição do Azure, selecione **chave de acesso**e, em seguida, forneça as informações para a conta de armazenamento externo.

    - **Contentor predefinido**: utilizar o valor de predefinição ou especificar seu próprio nome.

    - Contas de armazenamento adicionais: adicionar contas de armazenamento mais do Azure como armazenamento adicional.
    - Acesso do Data Lake Store: configurar o acesso entre a conta de geração 1 do Data Lake Storage e o cluster do HDInsight. Para obter instruções, consulte [configurar Gen1 de armazenamento do Data Lake acesso](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Configurar o acesso de geração 1 de armazenamento do Data Lake 

Nesta secção, configurar o acesso de geração 1 do Data Lake armazenamento de clusters do HDInsight com um principal de serviço do Azure Active Directory. 

### <a name="specify-a-service-principal"></a>Especifique um principal de serviço

No portal do Azure, pode utilizar um principal de serviço existente ou crie um novo.

**Para criar um principal de serviço a partir do portal do Azure**

1. Clique em **acesso do Data Lake Store** partir do painel de armazenamento.
2. Sobre o **acesso de geração 1 de armazenamento do Data Lake** painel, clique em **criar nova**.
3. Clique em **principal de serviço**e, em seguida, siga as instruções para criar um principal de serviço.
4. Transfira o certificado se optar por utilizá-lo novamente no futuro. Transferir o certificado é útil se pretender utilizar o mesmo principal de serviço ao criar clusters do HDInsight adicionais.

    ![Adicionar principal de serviço para o cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "principal de serviço de adicionar ao cluster do HDInsight")

4. Clique em **acesso** para configurar o acesso a pastas.  Ver [configurar permissões de arquivo](#configure-file-permissions).


**Para utilizar um principal de serviço existente do portal do Azure**

1. Clique em **acesso do Data Lake Store**.
1. Sobre o **acesso de geração 1 de armazenamento do Data Lake** painel, clique em **utilizar existente**.
2. Clique em **principal de serviço**e, em seguida, selecione um principal de serviço. 
3. Carregue o certificado (ficheiro. pfx) que está associada a seu principal de serviço selecionado e, em seguida, introduza a palavra-passe do certificado.

    ![Adicionar principal de serviço para o cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "principal de serviço de adicionar ao cluster do HDInsight")

4. Clique em **acesso** para configurar o acesso a pastas.  Ver [configurar permissões de arquivo](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Configurar permissões de arquivo

O configura são diferentes, dependendo se a conta é utilizada como o armazenamento predefinido ou uma conta de armazenamento adicional:

- Utilizado como armazenamento predefinido

    - permissão no nível de raiz da conta do Data Lake Storage Gen1
    - permissão no nível de raiz do armazenamento de cluster do HDInsight. Por exemplo, o __/clusters__ pasta utilizada anteriormente no tutorial.
- Utilizar como armazenamento adicional

    - Permissão em pastas em que precisa de acesso aos ficheiros do.

**Para atribuir permissões ao nível de raiz da conta de geração 1 de armazenamento do Data Lake**

1. Sobre o **acesso de geração 1 de armazenamento do Data Lake** painel, clique em **acesso**. O **selecionar permissões de ficheiros** é aberto o painel. Ele lista todas as contas de geração 1 de armazenamento do Data Lake na sua subscrição.
2. Coloque o cursor (não clique) o mouse sobre o nome da conta do Data Lake Storage Gen1 para tornar a caixa de verificação visível, em seguida, selecione a caixa de verificação.

    ![Adicionar principal de serviço para o cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "principal de serviço de adicionar ao cluster do HDInsight")

   Por predefinição, __ler__, __escrever__, e __EXECUTE__ são todos os selecionados.

3. Clique em **selecione** na parte inferior da página.
4. Clique em **executar** para atribuir permissão.
5. Clique em **Concluído**.

**Para atribuir permissões ao nível de raiz do cluster do HDInsight**

1. Sobre o **acesso de geração 1 de armazenamento do Data Lake** painel, clique em **acesso**. O **selecionar permissões de ficheiros** é aberto o painel. Ele lista todas as contas de geração 1 de armazenamento do Data Lake na sua subscrição.
1. Partir do **selecionar permissões de ficheiros** painel, clique no nome de conta de geração 1 de armazenamento do Data Lake para mostrar o seu conteúdo.
2. Selecione a raiz de armazenamento de cluster do HDInsight ao selecionar a caixa de verificação à esquerda da pasta. De acordo com a captura de ecrã anterior, a raiz de armazenamento de cluster é __/clusters__ pasta que especificou ao selecionar a geração 1 de armazenamento do Data Lake como armazenamento predefinido.
3. Defina as permissões na pasta.  Por predefinição, ler, escrever e executar são todos os selecionados.
4. Clique em **selecione** na parte inferior da página.
5. Clique em **Executar**.
6. Clique em **Concluído**.

Se estiver a utilizar o Data Lake Storage Gen1 como armazenamento adicional, tem de atribuir a permissão apenas para as pastas que pretende aceder a partir do cluster do HDInsight. Por exemplo, a captura de ecrã abaixo, fornecer acesso apenas para o **mynewfolder** pasta numa conta de geração 1 de armazenamento do Data Lake.

![Atribuir permissões de principal de serviço para o cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "atribuir permissões de principal de serviço para o cluster do HDInsight")


## <a name="verify-cluster-set-up"></a>Certifique-se a configuração de cluster

Depois de concluída a configuração de cluster, no painel do cluster, verifique se os resultados ao fazer um ou ambos dos seguintes passos:

* Para verificar que o armazenamento associado para o cluster é a conta de geração 1 de armazenamento do Data Lake que especificou, clique em **contas de armazenamento** no painel esquerdo.

    ![Adicionar principal de serviço para o cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "principal de serviço de adicionar ao cluster do HDInsight")

* Para verificar se o principal de serviço está corretamente associado com o cluster do HDInsight, clique em **acesso de geração 1 de armazenamento do Data Lake** no painel esquerdo.

    ![Adicionar principal de serviço para o cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "principal de serviço de adicionar ao cluster do HDInsight")


## <a name="examples"></a>Exemplos

Após ter configurado o cluster com a geração 1 de armazenamento do Data Lake como seu armazenamento, consulte estes exemplos de como utilizar o cluster do HDInsight para analisar os dados armazenados no Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Executar uma consulta do Hive em relação a dados de uma conta do Data Lake Storage Gen1 (como armazenamento primário)

Para executar uma consulta do Hive, utilize a interface de vistas do Hive no portal do Ambari. Para obter instruções sobre como utilizar as vistas do Ambari Hive, consulte [utilizar a vista do Hive com o Hadoop no HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Ao trabalhar com dados de uma conta do Data Lake Storage Gen1, existem algumas cadeias de caracteres para alterar.

Se usar, por exemplo, o cluster que criou com a geração 1 de armazenamento do Data Lake como armazenamento primário, o caminho para os dados é: *adl: / / < data_lake_storage_gen1_account_name > /azuredatalakestore.net/path/to/file*. Uma consulta do Hive para criar uma tabela de dados de exemplo que são armazenados na conta do Data Lake Storage Gen1 se parece com a seguinte instrução:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descrições:
* `adl://hdiadlsg1storage.azuredatalakestore.net/` é a raiz da conta do Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` é a raiz dos dados de cluster que especificou ao criar o cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` é a localização do ficheiro de exemplo que utilizou na consulta.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Executar uma consulta do Hive em relação a dados de uma conta do Data Lake Storage Gen1 (como armazenamento adicional)

Se o cluster que criou utiliza o armazenamento de Blob como armazenamento predefinido, os dados de exemplo não se encontra na conta de geração 1 de armazenamento do Data Lake, que é utilizada como armazenamento adicional. Nesse caso, em primeiro lugar transferir os dados do armazenamento de BLOBs para a conta de geração 1 de armazenamento do Data Lake e, em seguida, execute as consultas, conforme mostrado no exemplo anterior.

Para obter informações sobre como copiar dados de armazenamento de BLOBs para uma conta de geração 1 de armazenamento do Data Lake, veja os artigos seguintes:

* [Utilizar o Distcp para copiar dados entre os blobs de armazenamento do Azure e de geração 1 de armazenamento do Data Lake](data-lake-store-copy-data-wasb-distcp.md)
* [Utilizar o AdlCopy para copiar dados dos blobs de armazenamento do Azure para a geração 1 de armazenamento do Data Lake](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Utilizar a geração 1 de armazenamento do Data Lake com um cluster do Spark
Pode utilizar um cluster do Spark para executar tarefas do Spark nos dados que são armazenados numa conta de geração 1 de armazenamento do Data Lake. Para obter mais informações, consulte [cluster utilizar o Spark do HDInsight para analisar dados no Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Utilize a geração 1 de armazenamento do Data Lake numa topologia Storm
Pode utilizar a conta de geração 1 de armazenamento do Data Lake para escrever dados de uma topologia do Storm. Para obter instruções sobre como obter neste cenário, consulte [utilização do Azure Data Lake armazenamento Gen1 com o Apache Storm com HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Consulte também
* [Utilizar a geração 1 do Data Lake armazenamento com clusters do HDInsight do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: Criar um cluster do HDInsight para utilizar a geração 1 de armazenamento do Data Lake](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
