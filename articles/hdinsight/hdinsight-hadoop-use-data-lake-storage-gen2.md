---
title: Utilize o Azure Data Lake Storage Gen2 com clusters Azure HDInsight
description: Saiba como utilizar o Azure Data Lake Storage Gen2 com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389028"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Utilize o Azure Data Lake Storage Gen2 com clusters Azure HDInsight

Azure Data Lake Storage Gen2 é um serviço de armazenamento em nuvem dedicado à análise de big data, construído no armazenamento azure Blob. Data Lake Storage Gen2 combina as capacidades do armazenamento Azure Blob e do Armazenamento do Lago Azure Gen1. O serviço resultante oferece funcionalidades do Azure Data Lake Storage Gen1, tais como semântica do sistema de ficheiros, segurança de nível de diretório e nível de ficheiro, e escalabilidade, juntamente com as capacidades de armazenamento de baixo custo, hierárquicos, alta disponibilidade e capacidades de recuperação de desastres do armazenamento Azure Blob.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilidade de Gen2 de armazenamento de lago de dados

Data Lake Storage Gen2 está disponível como uma opção de armazenamento para quase todos os tipos de cluster Azure HDInsight como padrão e uma conta de armazenamento adicional. A HBase, no entanto, pode ter apenas uma conta data Lake Storage Gen2.

Para uma comparação completa das opções de criação de cluster utilizando data Lake Storage Gen2, consulte Compare opções de [armazenamento para utilização com clusters Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Depois de selecionar data lake storage Gen2 como o seu **tipo de armazenamento principal,** você não pode selecionar uma conta Data Lake Storage Gen1 como armazenamento adicional.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Crie um cluster com Data Lake Storage Gen2 através do portal Azure

Para criar um cluster HDInsight que utiliza data lake storage Gen2 para armazenamento, siga estes passos para configurar uma conta Data Lake Storage Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Crie uma identidade gerida atribuída ao utilizador, se ainda não tiver uma.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No clique superior esquerdo **Criar um recurso**.
1. Na caixa de pesquisa, digite **o utilizador atribuído** e clique em Identidade Gerida Atribuída pelo **Utilizador**.
1. Clique em **Criar**.
1. Introduza um nome para a sua identidade gerida, selecione a subscrição correta, grupo de recursos e localização.
1. Clique em **Criar**.

Para obter mais informações sobre como as identidades geridas funcionam no Azure HDInsight, consulte [identidades geridas no Azure HDInsight](hdinsight-managed-identities.md).

![Criar uma identidade gerida atribuída pelo utilizador](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Criar uma conta Gen2 de Armazenamento de Data Lake

Crie uma conta de armazenamento de Gen2 de armazenamento de lago de dados Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No clique superior esquerdo **Criar um recurso**.
1. Na caixa de pesquisa, digite **o armazenamento** e clique na conta **de armazenamento**.
1. Clique em **Criar**.
1. No ecrã da **conta de armazenamento Criar:**
    1. Selecione o grupo de subscrição e recursos correto.
    1. Insira um nome para a sua conta Data Lake Storage Gen2. Para obter mais informações sobre as convenções de nomeação de contas de armazenamento, consulte [as convenções de nomeação para os recursos do Azure.](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage)
    1. Clique no separador **Avançado.**
    1. Clique **ativado** ao lado do espaço de **nome hierárquico** sob data **lake storage Gen2**.
    1. Clique em **Rever + criar**.
    1. Clique em **Criar**.

Para obter mais informações sobre outras opções durante a criação de conta de armazenamento, consulte [Quickstart: Create a Azure Data Lake Storage Gen2 storage account](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Screenshot mostrando criação de conta de armazenamento no portal Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Configurar permissões para a identidade gerida na conta Data Lake Storage Gen2

Atribuir a identidade gerida à função de Proprietário de **Dados blob** de armazenamento na conta de armazenamento.

1. No [portal Azure,](https://portal.azure.com)vá à sua conta de armazenamento.
1. Selecione a sua conta de armazenamento e, em seguida, selecione o controlo de **acesso (IAM)** para visualizar as definições de controlo de acesso para a conta. Selecione o separador de **atribuições de funções** para ver a lista de atribuições de papéis.

    ![Screenshot mostrando definições de controlo de acesso de armazenamento](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Selecione o botão de atribuição de **funções + Adicionar** para adicionar uma nova função.
1. Na janela de atribuição de **funções Add,** selecione a função De proprietário de dados de **armazenamento Blob.** Em seguida, selecione a subscrição que tem a conta de identidade e armazenamento gerida. Em seguida, procure localizar a identidade gerida atribuída pelo utilizador que criou anteriormente. Por fim, selecione a identidade gerida e será listada em **membros Selecionados**.

    ![Screenshot mostrando como atribuir uma função RBAC](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Selecione **Guardar**. A identidade atribuída ao utilizador que selecionou está agora listada no âmbito da função selecionada.
1. Depois de concluída esta configuração inicial, pode criar um cluster através do portal. O cluster deve estar na mesma região de Azure que a conta de armazenamento. No separador **de armazenamento** do menu de criação de cluster, selecione as seguintes opções:

    * Para **o tipo de armazenamento primário,** selecione **Azure Data Lake Storage Gen2**.
    * Sob **a conta de Armazenamento Primário,** procure e selecione a recém-criada conta de armazenamento de Data Lake Gen2.

    * Em **Identidade**, selecione a identidade gerida recém-criada pelo utilizador.

        ![Definições de armazenamento para utilização de Data Lake Storage Gen2 com Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Para adicionar uma conta secundária de Data Lake Storage Gen2, ao nível da conta de armazenamento, simplesmente atribuir a identidade gerida criada anteriormente à nova conta de armazenamento de Data Lake Storage Gen2 que pretende adicionar. Por favor, informamos que a adição de uma conta secundária de Data Lake Storage Gen2 através da lâmina "Contas de armazenamento adicionais" no HDInsight não é suportada.
    > * Pode ativar RA-GRS ou RA-ZRS na conta de armazenamento Azure que o HDInsight utiliza. No entanto, a criação de um cluster contra o ponto final secundário RA-GRS ou RA-ZRS não é suportada.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Crie um cluster com Data Lake Storage Gen2 através do Azure CLI

Pode [descarregar um ficheiro](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) de modelo de amostra e descarregar um ficheiro de [parâmetros de amostra](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Antes de utilizar o modelo e o código Azure CLI abaixo, substitua os seguintes espaços reservados com os seus valores corretos:

| Marcador de posição | Descrição |
|---|---|
| `<SUBSCRIPTION_ID>` | A identificação da sua assinatura Azure |
| `<RESOURCEGROUPNAME>` | O grupo de recursos onde quer a nova conta de cluster e armazenamento criada. |
| `<MANAGEDIDENTITYNAME>` | O nome da identidade gerida que será dada permissões na sua conta Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | A nova conta azure Data Lake Storage Gen2 que será criada. |
| `<CLUSTERNAME>` | O nome do seu cluster HDInsight. |
| `<PASSWORD>` | A sua senha escolhida para iniciar sessão no cluster utilizando o SSH, bem como o painel ambari. |

O código abaixo faz os seguintes passos iniciais:

1. Inicia sessão na sua conta Azure.
1. Define a subscrição ativa onde as operações de criação serão feitas.
1. Cria um novo grupo de recursos para as novas atividades de implantação.
1. Cria uma identidade gerida atribuída ao utilizador.
1. Adiciona uma extensão ao Azure CLI para utilizar funcionalidades para data lake storage Gen2.
1. Cria uma nova conta data Lake Storage Gen2 usando a bandeira `--hierarchical-namespace true`.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Em seguida, inscreva-se no portal. Adicione a nova identidade gerida atribuída ao utilizador atribuído à função de Contribuinte de Dados do **Depósito Blob** na conta de armazenamento, conforme descrito no passo 3 no âmbito [do portal Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Depois de ter atribuído a função para a identidade gerida atribuída pelo utilizador, implemente o modelo utilizando o seguinte código.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Crie um cluster com Data Lake Storage Gen2 através do Azure PowerShell

O uso do PowerShell para criar um cluster HDInsight com o Azure Data Lake Storage Gen2 não é suportado atualmente.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controlo de acesso para Data Lake Storage Gen2 em HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Que tipo de permissões o Data Lake Storage Gen2 suporta?

Data Lake Storage Gen2 usa um modelo de controlo de acesso que suporta tanto o controlo de acesso baseado em papéis (RBAC) como as listas de controlo de acesso semelhantes a POSIX (ACLs). Data Lake Storage Gen1 suporta listas de controlo de acesso apenas para controlar o acesso aos dados.

O RBAC utiliza atribuições de funções para aplicar efetivamente conjuntos de permissões a utilizadores, grupos e diretores de serviço para recursos Azure. Tipicamente, esses recursos Azure estão limitados a recursos de alto nível (por exemplo, contas de Armazenamento Azure). Para o Armazenamento Azure, e também data Lake Storage Gen2, este mecanismo foi estendido ao recurso do sistema de ficheiros.

 Para obter mais informações sobre permissões de ficheiros com RBAC, consulte o [controlo de acesso baseado em funções azure (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Para obter mais informações sobre permissões de ficheiros com ACLs, consulte listas de controlo de [acesso em ficheiros e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Como controlo o acesso aos meus dados no Data Lake Storage Gen2?

A capacidade do seu cluster HDInsight de aceder a ficheiros em Data Lake Storage Gen2 é controlada através de identidades geridas. Uma identidade gerida é uma identidade registada no Azure Ative Directory (Azure AD) cujas credenciais são geridas pelo Azure. Com identidades geridas, não precisa de registar os principais de serviço sintetários em Azure AD ou manter credenciais como certificados.

Os serviços Azure têm dois tipos de identidades geridas: atribuídos pelo sistema e atribuídos ao utilizador. O HDInsight utiliza identidades geridas atribuídas ao utilizador para aceder ao Data Lake Storage Gen2. Uma identidade gerida atribuída ao utilizador é criada como um recurso Azure autónomo. Através de um processo de criação, o Azure cria uma identidade no inquilino do Azure AD no qual a subscrição que está a ser utilizada confia. Depois de criada, a identidade pode ser atribuída a uma ou mais instâncias do serviço do Azure.

O ciclo de vida das identidades atribuídas pelo utilizador é gerido separadamente do ciclo de vida das instâncias do serviço do Azure ao qual estão atribuídas. Para obter mais informações sobre identidades geridas, veja [como funcionam as identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Como posso definir permissões para os utilizadores de Anúncios Azure consultarem dados no Data Lake Storage Gen2 utilizando a Hive ou outros serviços?

Para definir permissões para os utilizadores consultarem dados, utilize os grupos de segurança Azure AD como o principal designado em ACLs. Não atribua diretamente permissões de acesso a ficheiros a utilizadores individuais ou diretores de serviço. Quando utiliza grupos de segurança Azure AD para controlar o fluxo de permissões, pode adicionar e remover utilizadores ou diretores de serviço sem reaplicar OSAc a toda uma estrutura de diretório. Só tem de adicionar ou remover os utilizadores do grupo de segurança Azure AD apropriado. Os ACLs não são herdados, por isso a reaplicação dos ACLs requer a atualização da ACL em todos os ficheiros e subdiretórios.

## <a name="access-files-from-the-cluster"></a>Aceder a ficheiros a partir do cluster

Existem várias formas de aceder aos ficheiros em Data Lake Storage Gen2 a partir de um cluster HDInsight.

* **Utilizar o nome completamente qualificado**. Com esta abordagem, fornece o caminho completo para o ficheiro ao qual pretende aceder.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Utilizando o formato de caminho abreviado**. Com esta abordagem, você substitui o caminho até a raiz do cluster por:

    ```
    abfs:///<file.path>/
    ```

* **Utilizar o caminho relativo**. Com esta abordagem, fornece apenas o caminho relativo para o ficheiro ao qual pretende aceder.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exemplos de acesso a dados

Exemplos são baseados numa [ligação ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) ao nó da cabeça do cluster. Os exemplos utilizam os três esquemas URI. Substitua `CONTAINERNAME` e `STORAGEACCOUNT` com os valores relevantes

#### <a name="a-few-hdfs-commands"></a>Alguns comandos hdfs

1. Crie um ficheiro sobre o armazenamento local.

    ```bash
    touch testFile.txt
    ```

1. Crie diretórios no armazenamento de clusters.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copie dados do armazenamento local para o armazenamento de clusters.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lista de conteúdos de diretório no armazenamento de clusters.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Criando uma mesa de colmeia

Três localizações de ficheiros são mostradas para fins ilustrativos. Para a execução real, utilize apenas uma das entradas `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Passos seguintes

* [Integração Azure HDInsight com visualização de Data Lake Storage Gen2 - ACL e atualização de segurança](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introdução ao Armazenamento de Lagos De Dados Azure Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Tutorial: Extrair, transformar e carregar dados usando consulta interativa no Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
