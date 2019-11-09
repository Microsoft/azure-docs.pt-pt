---
title: Criar clusters do Azure HDInsight com o Data Lake Storage Gen1-Portal
description: Use o portal do Azure para criar e usar clusters HDInsight com Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1d1368ef8ffb474c6bec1240f567f043961597fb
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838182"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Criar clusters HDInsight com Azure Data Lake Storage Gen1 usando o portal do Azure

> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usar o PowerShell (para armazenamento padrão)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usar o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usar o Gerenciador de recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Saiba como usar o portal do Azure para criar um cluster HDInsight com uma conta de Azure Data Lake Storage Gen1 como o armazenamento padrão ou um armazenamento adicional. Embora o armazenamento adicional seja opcional para um cluster HDInsight, é recomendável armazenar seus dados corporativos nas contas de armazenamento adicionais.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você atende aos seguintes requisitos:

* **Uma subscrição do Azure**. Acesse [obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de data Lake Storage Gen1**. Siga as instruções de [introdução ao Azure data Lake Storage Gen1 usando o portal do Azure](data-lake-store-get-started-portal.md). Você também deve criar uma pasta raiz na conta.  Neste artigo, é usada uma pasta raiz chamada __/clusters__ .
* **Uma entidade de serviço Azure Active Directory**. Este guia de instrução fornece instruções sobre como criar uma entidade de serviço no Azure Active Directory (AD do Azure). No entanto, para criar uma entidade de serviço, você deve ser um administrador do Azure AD. Se você for um administrador, poderá ignorar esse pré-requisito e continuar.

>[!NOTE]
>Você poderá criar uma entidade de serviço somente se você for um administrador do Azure AD. O administrador do Azure AD deve criar uma entidade de serviço antes que você possa criar um cluster HDInsight com Data Lake Storage Gen1. Além disso, a entidade de serviço deve ser criada com um certificado, conforme descrito em [criar uma entidade de serviço com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Criar um cluster do HDInsight

Nesta seção, você cria um cluster HDInsight com contas de Data Lake Storage Gen1 como o padrão ou o armazenamento adicional. Este artigo se concentra apenas na parte da configuração de contas de Data Lake Storage Gen1. Para obter informações e procedimentos gerais de criação de cluster, consulte [Criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Criar um cluster com Data Lake Storage Gen1 como armazenamento padrão

Para criar um cluster HDInsight com uma conta de Data Lake Storage Gen1 como a conta de armazenamento padrão:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Siga [Criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter as informações gerais sobre a criação de clusters HDInsight.
3. Na folha **armazenamento** , em **tipo de armazenamento primário**, selecione **Azure data Lake Storage Gen1**e, em seguida, insira as seguintes informações:

    ![Adicionar entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Adicionar entidade de serviço ao cluster HDInsight")

    * **Selecionar conta de data Lake Store**: selecione uma conta de data Lake Storage Gen1 existente. Uma conta de Data Lake Storage Gen1 existente é necessária.  Veja [Pré-requisitos](#prerequisites).
    * **Caminho raiz**: Insira um caminho onde os arquivos específicos do cluster devem ser armazenados. Na captura de tela, é __/clusters/myhdiadlcluster/__ , em que a pasta __/clusters__ deve existir e o portal cria a pasta *myhdicluster* .  O *myhdicluster* é o nome do cluster.
    * **Acesso data Lake Store**: Configure o acesso entre a conta de data Lake Storage Gen1 e o cluster HDInsight. Para obter instruções, consulte [Configurar o acesso de data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).
    * **Contas de armazenamento adicionais**: Adicione contas de armazenamento do Azure como contas de armazenamento adicionais para o cluster. Para adicionar mais contas Data Lake Storage Gen1 é feito fornecendo as permissões de cluster em dados em mais Data Lake Storage Gen1 contas ao configurar uma conta de Data Lake Storage Gen1 como o tipo de armazenamento primário. Consulte [Configurar o acesso ao data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

4. Na **Data Lake Store acesso**, clique em **selecionar**e continue com a criação do cluster, conforme descrito em [Criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Criar um cluster com Data Lake Storage Gen1 como armazenamento adicional

As instruções a seguir criam um cluster HDInsight com uma conta de armazenamento do Azure como o armazenamento padrão e uma conta de Data Lake Storage Gen1 como um armazenamento adicional.

Para criar um cluster HDInsight com uma conta de Data Lake Storage Gen1 como uma conta de armazenamento adicional:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Siga [Criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter as informações gerais sobre a criação de clusters HDInsight.
3. Na folha **armazenamento** , em **tipo de armazenamento primário**, selecione **armazenamento do Azure**e, em seguida, insira as seguintes informações:

    ![Adicionar entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Adicionar entidade de serviço ao cluster HDInsight")

    * **Método de seleção** – para especificar uma conta de armazenamento que faça parte de sua assinatura do Azure, selecione **minhas assinaturas**e, em seguida, selecione a conta de armazenamento. Para especificar uma conta de armazenamento que esteja fora de sua assinatura do Azure, selecione **chave de acesso**e forneça as informações para a conta de armazenamento externa.

    * **Contêiner padrão** – use o valor padrão ou especifique seu próprio nome.
    * **Contas de armazenamento adicionais** – adicione mais contas de armazenamento do Azure como o armazenamento adicional.
    * **Acesso data Lake Store** -configure o acesso entre a conta de data Lake Storage Gen1 e o cluster HDInsight. Para obter instruções, consulte [Configurar o acesso de data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Configurar o acesso ao Data Lake Storage Gen1

Nesta seção, você configurará o acesso Data Lake Storage Gen1 de clusters HDInsight usando uma entidade de serviço Azure Active Directory.

### <a name="specify-a-service-principal"></a>Especificar uma entidade de serviço

No portal do Azure, você pode usar uma entidade de serviço existente ou criar uma nova.

Para criar uma entidade de serviço do portal do Azure:

1. Selecione **Data Lake Store acesso** na folha armazenamento.
1. Na folha **acesso data Lake Storage Gen1** , selecione **criar novo**.
1. Selecione **entidade de serviço**e siga as instruções para criar uma entidade de serviço.
1. Baixe o certificado se você decidir usá-lo novamente no futuro. O download do certificado será útil se você quiser usar a mesma entidade de serviço ao criar clusters HDInsight adicionais.

    ![Adicionar entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Adicionar entidade de serviço ao cluster HDInsight")

1. Selecione **acesso** para configurar o acesso à pasta.  Consulte [configurar permissões de arquivo](#configure-file-permissions).

Para usar uma entidade de serviço existente do portal do Azure:

1. Selecione **acesso a data Lake Store**.
1. Na folha **acesso data Lake Storage Gen1** , selecione **usar existente**.
1. Selecione **entidade de serviço**e, em seguida, selecione uma entidade de serviço.
1. Carregue o certificado (arquivo. pfx) associado à entidade de serviço selecionada e insira a senha do certificado.

    ![Adicionar entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Adicionar entidade de serviço ao cluster HDInsight")

1. Selecione **acesso** para configurar o acesso à pasta.  Consulte [configurar permissões de arquivo](#configure-file-permissions).

### <a name="configure-file-permissions"></a>Configurar permissões de arquivo

A configuração é diferente dependendo se a conta é usada como o armazenamento padrão ou uma conta de armazenamento adicional:

* Usado como armazenamento padrão

  * permissão no nível raiz da conta de Data Lake Storage Gen1
  * permissão no nível raiz do armazenamento do cluster HDInsight. Por exemplo, a pasta __/clusters__ usada anteriormente no tutorial.

* Usar como um armazenamento adicional

  * Permissão nas pastas onde você precisa de acesso ao arquivo.

Para atribuir permissão no nível raiz da conta de Data Lake Storage Gen1:

1. Na folha **acesso data Lake Storage Gen1** , selecione **acesso**. A folha **selecionar permissões de arquivo** é aberta. Ele lista todas as contas de Data Lake Storage Gen1 em sua assinatura.
1. Focalize (não clique) o mouse sobre o nome da conta de Data Lake Storage Gen1 para tornar a caixa de seleção visível e marque a caixa de seleção.

    ![Adicionar entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Adicionar entidade de serviço ao cluster HDInsight")

   Por padrão, __ler__, __gravar__e __executar__ estão todos selecionados.

1. Clique em **selecionar** na parte inferior da página.
1. Selecione **executar** para atribuir permissão.
1. Selecione **Done** (Concluído).

Para atribuir permissão no nível de raiz do cluster HDInsight:

1. Na folha **acesso data Lake Storage Gen1** , selecione **acesso**. A folha **selecionar permissões de arquivo** é aberta. Ele lista todas as contas de Data Lake Storage Gen1 em sua assinatura.
1. Na folha **selecionar permissões de arquivo** , selecione o nome da conta de data Lake Storage Gen1 para mostrar seu conteúdo.
1. Selecione a raiz de armazenamento do cluster HDInsight marcando a caixa de seleção à esquerda da pasta. De acordo com a captura de tela anterior, a raiz de armazenamento do cluster é a pasta __/clusters__ que você especificou ao selecionar data Lake Storage Gen1 como armazenamento padrão.
1. Defina as permissões na pasta.  Por padrão, ler, gravar e executar estão todos selecionados.
1. Clique em **selecionar** na parte inferior da página.
1. Selecione **Executar**.
1. Selecione **Done** (Concluído).

Se você estiver usando Data Lake Storage Gen1 como armazenamento adicional, deverá atribuir permissão somente para as pastas que você deseja acessar do cluster HDInsight. Por exemplo, na captura de tela abaixo, você fornece acesso somente à pasta **mynewfolder** em uma conta de data Lake Storage Gen1.

![Atribuir permissões de entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Atribuir permissões de entidade de serviço ao cluster HDInsight")

## <a name="verify-cluster-set-up"></a>Verificar a configuração do cluster

Após a conclusão da configuração do cluster, na folha do cluster, verifique os resultados executando uma ou ambas as etapas a seguir:

* Para verificar se o armazenamento associado para o cluster é a conta de Data Lake Storage Gen1 que você especificou, selecione **contas de armazenamento** no painel esquerdo.

    ![Adicionar entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Adicionar entidade de serviço ao cluster HDInsight")

* Para verificar se a entidade de serviço está corretamente associada ao cluster HDInsight, selecione **Data Lake Storage Gen1 acesso** no painel esquerdo.

    ![Adicionar entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Adicionar entidade de serviço ao cluster HDInsight")

## <a name="examples"></a>Exemplos

Depois de configurar o cluster com Data Lake Storage Gen1 como seu armazenamento, consulte estes exemplos de como usar o cluster HDInsight para analisar os dados armazenados no Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Executar uma consulta de Hive em relação a dados em uma conta de Data Lake Storage Gen1 (como armazenamento primário)

Para executar uma consulta de Hive, use a interface exibições do hive no portal do Ambari. Para obter instruções sobre como usar exibições do hive do Ambari, consulte [usar a exibição do hive com o Hadoop no HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Quando você trabalha com dados em uma conta de Data Lake Storage Gen1, há algumas cadeias de caracteres a serem alteradas.

Se você usar, por exemplo, o cluster que você criou com Data Lake Storage Gen1 como armazenamento primário, o caminho para os dados será: *ADL://< data_lake_storage_gen1_account_name >/azuredatalakestore.net/Path/to/file*. Uma consulta de Hive para criar uma tabela a partir de dados de exemplo armazenados na conta de Data Lake Storage Gen1 é semelhante à seguinte instrução:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descrições

* `adl://hdiadlsg1storage.azuredatalakestore.net/` é a raiz da conta de Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` é a raiz dos dados do cluster que você especificou ao criar o cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` é o local do arquivo de exemplo que você usou na consulta.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Executar uma consulta de Hive em relação a dados em uma conta de Data Lake Storage Gen1 (como armazenamento adicional)

Se o cluster que você criou usa o armazenamento de blob como armazenamento padrão, os dados de exemplo não estão contidos na conta de Data Lake Storage Gen1 que é usada como armazenamento adicional. Nesse caso, primeiro transfira os dados do armazenamento de BLOBs para a conta de Data Lake Storage Gen1 e, em seguida, execute as consultas, conforme mostrado no exemplo anterior.

Para obter informações sobre como copiar dados do armazenamento de BLOBs para uma conta de Data Lake Storage Gen1, consulte os seguintes artigos:

* [Use Distcp para copiar dados entre os blobs de armazenamento do Azure e Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Use AdlCopy para copiar dados de blobs de armazenamento do Azure para Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Usar Data Lake Storage Gen1 com um cluster Spark

Você pode usar um cluster Spark para executar trabalhos do Spark em dados armazenados em uma conta de Data Lake Storage Gen1. Para obter mais informações, consulte [usar o cluster HDInsight Spark para analisar dados em data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Usar Data Lake Storage Gen1 em uma topologia do Storm

Você pode usar a conta de Data Lake Storage Gen1 para gravar dados de uma topologia do Storm. Para obter instruções sobre como obter esse cenário, consulte [usar Azure data Lake Storage Gen1 com Apache Storm com o HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Consultar também

* [Usar Data Lake Storage Gen1 com clusters do Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: criar um cluster HDInsight para usar Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
