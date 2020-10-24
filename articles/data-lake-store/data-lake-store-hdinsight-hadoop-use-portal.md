---
title: Criar clusters Azure HDInsight com Data Lake Storage Gen1 - portal
description: Utilize o portal Azure para criar e utilizar clusters HDInsight com Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 41ba9d9e66fa1d7f622550bde68951573af4bb96
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92484989"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Criar clusters HDInsight com Azure Data Lake Storage Gen1 utilizando o portal Azure

> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utilize o PowerShell (para armazenamento predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utilize o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Use Gestor de Recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar o portal Azure para criar um cluster HDInsight com a Azure Data Lake Storage Gen1 como armazenamento padrão ou um armazenamento adicional. Embora o armazenamento adicional seja opcional para um cluster HDInsight, é recomendado armazenar os dados do seu negócio nas contas de armazenamento adicionais.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que cumpriu os seguintes requisitos:

* **Uma assinatura Azure**. Vá para [obter o julgamento gratuito de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Azure Data Lake Storage Gen1**. Siga as instruções de [Começar com a Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md). Também deve criar uma pasta de raiz na conta.  Neste artigo, é utilizada uma pasta de raiz chamada __/clusters.__
* **Um diretor de serviço do Azure Ative Directory**. Este guia de como fazer fornece instruções sobre como criar um diretor de serviço no Azure Ative Directory (Azure AD). No entanto, para criar um principal de serviço, você deve ser um administrador AD Azure. Se for um administrador, pode saltar este pré-requisito e continuar.

>[!NOTE]
>Só pode criar um principal de serviço se for administrador da AD da Azure. O administrador AD Azure deve criar um principal de serviço antes de criar um cluster HDInsight com a Data Lake Storage Gen1. Além disso, o principal de serviço deve ser criado com um certificado, conforme descrito na [Create a service com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Criar um cluster do HDInsight

Nesta secção, você cria um cluster HDInsight com Data Lake Storage Gen1 como padrão ou armazenamento adicional. Este artigo centra-se apenas na parte de configurar data lake storage gen1. Para obter informações e procedimentos gerais de criação de clusters, consulte [os clusters Create Hadoop em HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Crie um cluster com data lake storage gen1 como armazenamento padrão

Para criar um cluster HDInsight com uma Data Lake Storage Gen1 como conta de armazenamento padrão:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Siga [Criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter informações gerais sobre a criação de clusters HDInsight.
3. Na lâmina **de armazenamento,** sob o **tipo de armazenamento primário,** selecione **Azure Data Lake Storage Gen1**, e, em seguida, introduza as seguintes informações:

    ![Definições de conta de armazenamento HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png)

    * **Selecione conta Data Lake Store**: Selecione uma conta gen1 de armazenamento de datas existente. É necessária uma conta Gen1 de armazenamento de dados existente.  Veja [Pré-requisitos](#prerequisites).
    * **Caminho da raiz**: Introduza um caminho onde os ficheiros específicos do cluster devem ser armazenados. Na imagem, é __/clusters/myhdiadlcluster/__, no qual a pasta __/clusters__ deve existir, e o Portal cria uma pasta *de myhdicluster.*  O *meu myhdicluster* é o nome do cluster.
    * **Data Lake Store acesso**: Configure o acesso entre a conta Dese de Armazenamento de Dados Gen1 e o cluster HDInsight. Para obter instruções, consulte [o acesso à Gen1 de armazenamento de dados configure.](#configure-data-lake-storage-gen1-access)
    * **Contas de armazenamento adicionais**: Adicione as contas de armazenamento Azure como contas de armazenamento adicionais para o cluster. Para adicionar contas adicionais de Data Lake Storage Gen1 é feito dando ao cluster permissões de dados em mais contas de Data Lake Storage Gen1 enquanto configura uma conta Desagure de Armazenamento de Dados Gen1 como o tipo de armazenamento primário. Consulte o [acesso Configure Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

4. No **acesso**à Data Lake Store , clique **em Select**, e, em seguida, continue com a criação de clusters como descrito nos [clusters Create Hadoop em HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Criar um cluster com data lake storage gen1 como armazenamento adicional

As seguintes instruções criam um cluster HDInsight com uma conta de armazenamento Azure Blob como armazenamento padrão, e uma conta de armazenamento com Data Lake Storage Gen1 como um armazenamento adicional.

Para criar um cluster HDInsight com data lake storage gen1 como uma conta de armazenamento adicional:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Siga [Criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter informações gerais sobre a criação de clusters HDInsight.
3. Na lâmina **de armazenamento,** sob o **tipo de armazenamento primário,** selecione **Azure Storage**, e, em seguida, introduza as seguintes informações:

    ![Definições de conta de armazenamento HDInsight armazenamento adicional](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png)

    * **Método de seleção** - Para especificar uma conta de armazenamento que faça parte da sua subscrição Azure, selecione **As Minhas subscrições**e, em seguida, selecione a conta de armazenamento. Para especificar uma conta de armazenamento que esteja fora da sua subscrição Azure, selecione **a chave de acesso**e, em seguida, forneça as informações para a conta de armazenamento exterior.

    * **Recipiente predefinido** - Utilize o valor predefinido ou especifique o seu próprio nome.
    * **Contas de armazenamento adicionais** - Adicione mais contas de armazenamento Azure como armazenamento adicional.
    * **Data Lake Store acesso** -Configure o acesso entre a conta Desebose de Armazenamento de Dados Gen1 e o cluster HDInsight. Para obter instruções consulte o [acesso à Gen1 de armazenamento de dados configure.](#configure-data-lake-storage-gen1-access)

## <a name="configure-data-lake-storage-gen1-access"></a>Configure data lake storage gen1 acesso

Nesta secção, configura o acesso da Data Lake Storage Gen1 a partir de clusters HDInsight utilizando um diretor de serviço do Azure Ative Directory.

### <a name="specify-a-service-principal"></a>Especificar um diretor de serviço

A partir do portal Azure, você pode usar um principal de serviço existente ou criar um novo.

Para criar um diretor de serviço a partir do portal Azure:
1. Consulte [Criar O Título de Serviço e Certificados](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) utilizando o Diretório Ativo Azure.

Para utilizar um principal de serviço existente a partir do portal Azure:

1. O diretor de serviço deve ter permissões do proprietário na conta de Armazenamento. Consulte [as permissões de configuração para que o Diretor de Serviço seja proprietário na conta de armazenamento.](#configure-serviceprincipal-permissions)
1. Selecione **data lake store acesso**.
1. Na lâmina de **acesso Gen1 de armazenamento de data lake,** selecione **Utilize a lâmina existente**.
1. Selecione **o diretor de serviço**e, em seguida, selecione um principal de serviço.
1. Faça o upload do certificado (.pfx file) que está associado ao seu principal de serviço selecionado e, em seguida, introduza a senha de certificado.

[Adicione o principal do serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png)

1. Selecione **Acesso** à configuração do acesso à pasta.  Consulte [permissões de ficheiros configurantes](#configure-file-permissions).

### <a name="set-up-permissions-for-the-service-principal-to-be-owner-on-the-storage-account"></a><a name="configure-serviceprincipal-permissions"></a>Configurar permissões para que o Diretor de Serviço seja proprietário na conta de armazenamento
1. Na folha de armazenamento do Controlo de Acesso (IAM) clique em Adicionar uma atribuição de função. 
2. No Add a role assignment blade select Role as 'owner', e selecione o SPN e clique em guardar.

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Configure permissões de ficheiros

A configuração é diferente dependendo se a conta é usada como armazenamento padrão ou como uma conta de armazenamento adicional:

* Usado como armazenamento predefinido

  * permissão ao nível da raiz da conta Despossundo Desendo de Armazenamento Gen1
  * permissão ao nível da raiz do armazenamento do cluster HDInsight. Por exemplo, a pasta __/clusters__ usada anteriormente no tutorial.

* Uso como armazenamento adicional

  * Permissão nas pastas onde precisa de acesso ao ficheiro.

Para atribuir permissão na conta de armazenamento com data lake storage gen1 ao nível da raiz:

1. Na lâmina de **acesso Gen1 de armazenamento de data lake,** selecione **Access**. A lâmina **de permissões de ficheiro Select** é aberta. Ele lista todas as contas de armazenamento na sua assinatura.
1. Hover (não clique) o rato sobre o nome da conta com data lake storage gen1 para tornar a caixa de verificação visível e, em seguida, selecione a caixa de verificação.

    ![Selecione permissões de ficheiros](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png)

   Por predefinição, __LEIA,__ __ESCREVa__e __EXECUTE__ todos.

1. Clique em **Selecionar** na parte inferior da página.
1. Selecione **Executar** para atribuir permissão.
1. Selecione **Concluído**.

Para atribuir permissão ao nível da raiz do cluster HDInsight:

1. Na lâmina de **acesso Gen1 de armazenamento de data lake,** selecione **Access**. A lâmina **de permissões de ficheiro Select** é aberta. Ele lista todas as contas de armazenamento com Data Lake Storage Gen1 na sua subscrição.
1. A partir da lâmina **de permissões de ficheiros Select,** selecione a conta de armazenamento com o nome Data Lake Storage Gen1 para mostrar o seu conteúdo.
1. Selecione a raiz de armazenamento do cluster HDInsight selecionando a caixa de verificação à esquerda da pasta. De acordo com a imagem anterior, a raiz de armazenamento de cluster é a pasta __/clusters__ que especificou ao selecionar a Data Lake Storage Gen1 como armazenamento predefinido.
1. Desa estale as permissões na pasta.  Por predefinição, leiam, escrevam e executem todos.
1. Clique em **Selecionar** na parte inferior da página.
1. Selecione **Executar**.
1. Selecione **Concluído**.

Se estiver a utilizar a Data Lake Storage Gen1 como armazenamento adicional, tem de atribuir permissão apenas às pastas a que pretende aceder a partir do cluster HDInsight. Por exemplo, na imagem abaixo, fornece acesso apenas à pasta **mynewfolder** numa conta de armazenamento com data lake storage gen1.

![Atribuir permissões principais de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png)

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Verificar configuração do cluster

Depois de concluída a configuração do cluster, na lâmina do cluster, verifique os seus resultados fazendo ambos ou ambos os seguintes passos:

* Para verificar se o armazenamento associado ao cluster é a conta com a Data Lake Storage Gen1 que especificou, selecione **contas de Armazenamento** no painel esquerdo.

    ![Verificar armazenamento associado](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png)

* Para verificar se o principal de serviço está corretamente associado ao cluster HDInsight, selecione o **acesso da Data Lake Storage Gen1** no painel esquerdo.

    ![Verificar o principal do serviço](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png)

## <a name="examples"></a>Exemplos

Depois de configurar o cluster com data lake storage Gen1 como seu armazenamento, veja estes exemplos de como usar o cluster HDInsight para analisar os dados armazenados na Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-primary-storage"></a>Executar uma consulta de Colmeia contra dados numa Gen1 de Armazenamento de Data Lake (como armazenamento primário)

Para executar uma consulta de Colmeia, utilize a interface de vistas da Colmeia no portal Ambari. Para obter instruções sobre como utilizar as vistas da Colmeia Ambari, consulte [use a Vista de Colmeia com Hadoop em HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Quando se trabalha com dados numa Gen1 de Armazenamento de Data Lake, há algumas cordas para mudar.

Se utilizar, por exemplo, o cluster que criou com a Data Lake Storage Gen1 como armazenamento primário, o caminho para os dados é: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/file*. Uma consulta da Colmeia para criar uma tabela a partir de dados de amostra que é armazenado na Data Lake Storage Gen1 parece a seguinte declaração:

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

Descrições:

* `adl://hdiadlsg1storage.azuredatalakestore.net/` é a raiz da conta com data lake storage gen1.
* `/clusters/myhdiadlcluster` é a raiz dos dados do cluster que especificou ao criar o cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` é a localização do ficheiro de amostra que usou na consulta.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-additional-storage"></a>Executar uma consulta de Colmeia contra dados numa Gen1 de armazenamento de data lake (como armazenamento adicional)

Se o cluster que criou utilizar o armazenamento Blob como armazenamento predefinido, os dados da amostra não estão contidos na conta de armazenamento com a Data Lake Storage Gen1 que é usada como armazenamento adicional. Nesse caso, primeiro transfira os dados do armazenamento blob para a conta de armazenamento com a Data Lake Storage Gen1 e, em seguida, executar as consultas como mostrado no exemplo anterior.

Para obter informações sobre como copiar dados do armazenamento blob para uma conta de armazenamento com data lake storage gen1, consulte os seguintes artigos:

* [Utilize o Distcp para copiar dados entre o armazenamento do Azure Blob e a Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Utilize o AdlCopy para copiar dados do armazenamento do Azure Blob para a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Use data lake storage Gen1 com um cluster spark

Você pode usar um cluster Spark para executar trabalhos spark em dados que são armazenados em um Data Lake Storage Gen1. Para obter mais informações, consulte [o cluster HDInsight Spark para analisar dados na Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Use data lake storage gen1 em uma topologia tempestade

Você pode usar a conta de armazenamento com Data Lake Storage Gen1 para escrever dados de uma topologia storm. Para obter instruções sobre como alcançar este cenário, consulte [Use Azure Data Lake Storage Gen1 com Apache Storm com HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Ver também

* [Use data lake storage Gen1 com clusters Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [PowerShell: Criar um cluster HDInsight para utilizar a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
