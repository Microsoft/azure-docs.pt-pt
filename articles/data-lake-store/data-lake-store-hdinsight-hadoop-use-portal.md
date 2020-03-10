---
title: Crie clusters Azure HDInsight com Data Lake Storage Gen1 - portal
description: Utilize o portal Azure para criar e utilizar clusters HDInsight com Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1d1368ef8ffb474c6bec1240f567f043961597fb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389778"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Crie clusters HDInsight com Azure Data Lake Storage Gen1 utilizando o portal Azure

> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilizar o PowerShell (para armazenamento predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilizar o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Gestor de Recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar o portal Azure para criar um cluster HDInsight com uma conta De armazenamento de lagos Azure Data Gen1 como armazenamento padrão ou um armazenamento adicional. Embora o armazenamento adicional seja opcional para um cluster HDInsight, é recomendado armazenar os seus dados de negócio nas contas de armazenamento adicionais.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que preencheu os seguintes requisitos:

* **Uma subscrição do Azure**. Vá para [obter o teste gratuito de Azure.](https://azure.microsoft.com/pricing/free-trial/)
* **Uma conta Gen1 de Armazenamento de Lago**de Dados. Siga as instruções do [Get started com o Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md). Também deve criar uma pasta de raiz na conta.  Neste artigo, é utilizada uma pasta de raiz chamada __/clusters.__
* Um diretor de serviço de **Diretório Ativo Azure.** Este guia de como orientar fornece instruções sobre como criar um diretor de serviço no Azure Ative Directory (Azure AD). No entanto, para criar um diretor de serviço, deve ser administrador da AD Azure. Se for administrador, pode ignorar este pré-requisito e continuar.

>[!NOTE]
>Só pode criar um diretor de serviço se for administrador da AD Azure. O administrador da AD Azure deve criar um diretor de serviço antes de poder criar um cluster HDInsight com data Lake Storage Gen1. Além disso, o diretor de serviço deve ser criado com um certificado, conforme descrito na [Create a service principal com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Criar um cluster do HDInsight

Nesta secção, cria-se um cluster HDInsight com contas de Data Lake Storage Gen1 como padrão ou armazenamento adicional. Este artigo centra-se apenas na parte da configuração das contas da Gen1 de Armazenamento de Data Lake. Para obter informações e procedimentos gerais de criação de clusters, consulte [Create Hadoop clusters in HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Criar um cluster com Data Lake Storage Gen1 como armazenamento padrão

Para criar um cluster HDInsight com uma conta Gen1 de Armazenamento de Data Lake como conta de armazenamento padrão:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Siga [os clusters Create](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter informações gerais sobre a criação de clusters HDInsight.
3. Na lâmina **de armazenamento,** sob o tipo de **armazenamento primário,** selecione **Azure Data Lake Storage Gen1**, e, em seguida, introduza as seguintes informações:

    ![Adicionar principal de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Adicionar principal de serviço ao cluster HDInsight")

    * **Selecione data lake store account**: Selecione uma conta Gen1 de Armazenamento de Data Lake existente. É necessária uma conta gen1 de armazenamento de data lake existente.  Veja [Pré-requisitos](#prerequisites).
    * **Caminho raiz**: Insira um caminho onde os ficheiros específicos do cluster devem ser armazenados. Na imagem, é __/clusters/myhdiadlcluster/__ , em que a pasta __/clusters__ deve existir, e o Portal cria pasta *myhdicluster.*  O *myhdicluster* é o nome do cluster.
    * **Acesso da Data Lake Store**: Configure o acesso entre a conta Data Lake Storage Gen1 e o cluster HDInsight. Para obter instruções, consulte O acesso do [Configure Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).
    * Contas de **armazenamento adicionais:** Adicione as contas de armazenamento do Azure como contas de armazenamento adicionais para o cluster. Para adicionar contas adicionais de Data Lake Storage Gen1 é feito dando ao cluster permissões em dados em mais contas de Data Lake Storage Gen1 enquanto configura uma conta de Data Lake Storage Gen1 como o tipo de armazenamento primário. Consulte o acesso do [Configure Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

4. No acesso da **Data Lake Store,** clique em **Select**, e continue com a criação de cluster como descrito em [clusters Create Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Criar um cluster com Data Lake Storage Gen1 como armazenamento adicional

As seguintes instruções criam um cluster HDInsight com uma conta de armazenamento Azure como armazenamento padrão, e uma conta Data Lake Storage Gen1 como um armazenamento adicional.

Para criar um cluster HDInsight com uma conta Gen1 de Armazenamento de Data Lake como uma conta de armazenamento adicional:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Siga [os clusters Create](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter informações gerais sobre a criação de clusters HDInsight.
3. Na lâmina **de armazenamento,** sob o tipo de **armazenamento primário,** selecione **Armazenamento Azure,** e introduza as seguintes informações:

    ![Adicionar principal de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Adicionar principal de serviço ao cluster HDInsight")

    * Método de **seleção** - Para especificar uma conta de armazenamento que faça parte da sua subscrição Azure, selecione **Minhas subscrições**e, em seguida, selecione a conta de armazenamento. Para especificar uma conta de armazenamento que esteja fora da subscrição do Azure, selecione **a tecla Access**e, em seguida, forneça as informações para a conta de armazenamento externa.

    * **Recipiente predefinido** - Utilize o valor predefinido ou especifique o seu próprio nome.
    * Contas de **armazenamento adicionais** - Adicione mais contas de armazenamento Azure como armazenamento adicional.
    * **Acesso data Lake Store** - Configure o acesso entre a conta Data Lake Storage Gen1 e o cluster HDInsight. Para obter instruções consulte [Configurar o acesso](#configure-data-lake-storage-gen1-access)do Gen1 de armazenamento de dados do lago .

## <a name="configure-data-lake-storage-gen1-access"></a>Configure data lake storage Gen1

Nesta secção, configura o acesso da Gen1 de Armazenamento de Data Lake a partir de clusters HDInsight utilizando um diretor de serviço de Diretório Ativo Azure.

### <a name="specify-a-service-principal"></a>Especificar um diretor de serviço

A partir do portal Azure, pode utilizar um diretor de serviço existente ou criar um novo.

Para criar um diretor de serviço a partir do portal Azure:

1. Selecione o acesso da **Data Lake Store** a partir da lâmina de armazenamento.
1. Na lâmina de **acesso Gen1** de armazenamento data Lake, selecione **Criar nova**.
1. Selecione **o diretor**de serviço e, em seguida, siga as instruções para criar um diretor de serviço.
1. Faça o download do certificado se decidir utilizá-lo novamente no futuro. O download do certificado é útil se pretender utilizar o mesmo diretor de serviço quando criar clusters HDInsight adicionais.

    ![Adicionar principal de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Adicionar principal de serviço ao cluster HDInsight")

1. Selecione **Access** para configurar o acesso à pasta.  Consulte [as permissões](#configure-file-permissions)de ficheiro configure .

Utilizar um serviço existente do portal Azure:

1. Selecione **data lake store acesso**.
1. Na lâmina de acesso Gen1 de armazenamento de **data Lake,** selecione **Use existente**.
1. Selecione **o diretor de serviço**e, em seguida, selecione um diretor de serviço.
1. Faça upload do certificado (ficheiro.pfx) que está associado ao seu diretor de serviço selecionado e, em seguida, introduza a palavra-passe do certificado.

    ![Adicionar principal de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Adicionar principal de serviço ao cluster HDInsight")

1. Selecione **Access** para configurar o acesso à pasta.  Consulte [as permissões](#configure-file-permissions)de ficheiro configure .

### <a name="configure-file-permissions"></a>Configurar permissões de ficheiros

A configuração é diferente dependendo se a conta é usada como armazenamento predefinido ou uma conta de armazenamento adicional:

* Usado como armazenamento padrão

  * permissão no nível raiz da conta Data Lake Storage Gen1
  * permissão ao nível raiz do armazenamento do cluster HDInsight. Por exemplo, a pasta __/clusters__ usada anteriormente no tutorial.

* Usar como armazenamento adicional

  * Permissão nas pastas onde necessita de acesso a ficheiros.

Para atribuir permissão no nível raiz da conta data Lake Storage Gen1:

1. Na lâmina de acesso Gen1 de armazenamento de **data Lake,** selecione **Access**. A lâmina de **permissões** de ficheiro Select está aberta. Ele lista todas as contas data Lake Storage Gen1 na sua subscrição.
1. Hover (não clique) no rato sobre o nome da conta Data Lake Storage Gen1 para tornar a caixa de verificação visível e, em seguida, selecione a caixa de verificação.

    ![Adicionar principal de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Adicionar principal de serviço ao cluster HDInsight")

   Por defeito, __LEIA,__ __ESCREVA,__ __EXECUTE__ e executa todos são selecionados.

1. Clique em **Selecionar** na parte inferior da página.
1. Selecione **Executar** para atribuir permissão.
1. Selecione **Done** (Concluído).

Para atribuir permissão ao nível da raiz do cluster HDInsight:

1. Na lâmina de acesso Gen1 de armazenamento de **data Lake,** selecione **Access**. A lâmina de **permissões** de ficheiro Select está aberta. Ele lista todas as contas data Lake Storage Gen1 na sua subscrição.
1. A partir da lâmina de **permissões** de ficheiro Select, selecione o nome da conta Data Lake Storage Gen1 para mostrar o seu conteúdo.
1. Selecione a raiz de armazenamento do cluster HDInsight selecionando a caixa de verificação à esquerda da pasta. De acordo com a imagem anterior, a raiz de armazenamento do cluster é a pasta __/clusters__ que especificou ao selecionar data Lake Storage Gen1 como armazenamento padrão.
1. Detete as permissões na pasta.  Por defeito, leia, escreva e execute.
1. Clique em **Selecionar** na parte inferior da página.
1. Selecione **Executar**.
1. Selecione **Done** (Concluído).

Se estiver a utilizar o Data Lake Storage Gen1 como armazenamento adicional, deve atribuir permissão apenas para as pastas a que pretende aceder a partir do cluster HDInsight. Por exemplo, na imagem abaixo, fornece-se acesso apenas à pasta **mynewfolder** numa conta Gen1 de Armazenamento de Data Lake.

![Atribuir permissões principais de serviço para o cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Atribuir permissões principais de serviço para o cluster HDInsight")

## <a name="verify-cluster-set-up"></a>Verificar a configuração do cluster

Depois de concluída a configuração do cluster, na lâmina do cluster, verifique os seus resultados fazendo ambos os seguintes passos:

* Para verificar se o armazenamento associado para o cluster é a conta Data Lake Storage Gen1 que especificou, selecione contas de **Armazenamento** no painel esquerdo.

    ![Adicionar principal de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Adicionar principal de serviço ao cluster HDInsight")

* Para verificar se o diretor de serviço está corretamente associado ao cluster HDInsight, selecione **data Lake Storage Gen1 no** painel esquerdo.

    ![Adicionar principal de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Adicionar principal de serviço ao cluster HDInsight")

## <a name="examples"></a>Exemplos

Depois de configurar o cluster com data lake storage Gen1 como seu armazenamento, veja estes exemplos de como usar o cluster HDInsight para analisar os dados armazenados em Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Executar uma consulta de Colmeia contra dados numa conta Gen1 de Armazenamento de Data Lake (como armazenamento primário)

Para fazer uma consulta de Colmeia, use a interface de vistas da Colmeia no portal Ambari. Para obter instruções sobre como usar as vistas da Colmeia Ambari, consulte [Use a vista para a colmeia com hadoop em HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Quando trabalha com dados numa conta de Data Lake Storage Gen1, existem algumas cordas para mudar.

Se utilizar, por exemplo, o cluster que criou com data Lake Storage Gen1 como armazenamento primário, o caminho para os dados é: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/file*. Uma consulta da Hive para criar uma tabela a partir de dados de amostraque está armazenado na conta Data Lake Storage Gen1 parece a seguinte declaração:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descrições:

* `adl://hdiadlsg1storage.azuredatalakestore.net/` é a raiz da conta Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` é a raiz dos dados do cluster que especificou ao criar o cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` é a localização do ficheiro de amostra que usou na consulta.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Executar uma consulta de Hive contra dados numa conta Gen1 de Armazenamento de Data Lake (como armazenamento adicional)

Se o cluster que criou utilizar o armazenamento blob como armazenamento predefinido, os dados da amostra não estão contidos na conta Data Lake Storage Gen1 que é usada como armazenamento adicional. Nesse caso, primeiro transfira os dados do armazenamento blob para a conta Data Lake Storage Gen1, e depois executar as consultas como mostrado no exemplo anterior.

Para obter informações sobre como copiar dados do armazenamento blob para uma conta Gen1 de Armazenamento de Data Lake, consulte os seguintes artigos:

* [Utilize a Distcp para copiar dados entre as bolhas de armazenamento do Azure e o Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Utilize o AdlCopy para copiar dados de blobs de armazenamento de Azure para Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Use Data Lake Storage Gen1 com um cluster de faíscas

Você pode usar um cluster Spark para executar trabalhos spark em dados que são armazenados em uma conta Data Lake Storage Gen1. Para mais informações, consulte [o cluster Desonro HDInsight para analisar dados em Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Use Data Lake Storage Gen1 em uma topologia de tempestade

Você pode usar a conta Data Lake Storage Gen1 para escrever dados de uma topologia storm. Para obter instruções sobre como alcançar este cenário, consulte [Use Azure Data Lake Storage Gen1 com Apache Storm com HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Consulte também

* [Utilize data lake storage Gen1 com clusters Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: Criar um cluster HDInsight para usar data lake storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
