---
title: Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight
description: Aprenda a usar a Azure Data Lake Storage Gen2 com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: ab1cba455f93abe4d603f417f8d0a00dda0e315e
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497652"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight

Azure Data Lake Storage Gen2 é um serviço de armazenamento em nuvem dedicado à análise de big data, construído no armazenamento Azure Blob. Data Lake Storage Gen2 combina as capacidades de armazenamento de Azure Blob e Azure Data Lake Storage Gen1. O serviço resultante oferece funcionalidades da Azure Data Lake Storage Gen1. Estas funcionalidades incluem: semântica do sistema de ficheiros, segurança ao nível do diretório e nível de ficheiros e adaptabilidade. Juntamente com o armazenamento de baixo custo, tiered, alta disponibilidade e capacidades de recuperação de desastres a partir do armazenamento Azure Blob.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilidade de Gen2 de armazenamento de data lake

Data Lake Storage Gen2 está disponível como uma opção de armazenamento para quase todos os tipos de cluster Azure HDInsight como uma conta de armazenamento padrão e uma conta de armazenamento adicional. HBase, no entanto, pode ter apenas uma conta Desarrumação gen2 de armazenamento de dados.

Para uma comparação completa das opções de criação de cluster usando data lake storage gen2, consulte [opções de armazenamento compare para uso com clusters Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Depois de selecionar a Data Lake Storage Gen2 como o seu **tipo de armazenamento primário,** não é possível selecionar uma conta Gen1 de armazenamento de data lake como armazenamento adicional.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Criar um cluster com data lake storage gen2 através do portal Azure

Para criar um cluster HDInsight que usa data lake storage gen2 para armazenamento, siga estes passos para configurar uma conta Descongesmente de Armazenamento de Dados.

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Crie uma identidade gerida atribuída ao utilizador, se ainda não tiver uma.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No clique superior esquerdo **Criar um recurso**.
1. Na caixa de pesquisa, **digite** o utilizador atribuído e clique em **Identidade Gerida Atribuída pelo Utilizador.**
1. Clique em **Create** (Criar).
1. Insira um nome para a sua identidade gerida, selecione a subscrição correta, grupo de recursos e localização.
1. Clique em **Create** (Criar).

Para obter mais informações sobre como as identidades geridas funcionam em Azure HDInsight, consulte [identidades geridas em Azure HDInsight](hdinsight-managed-identities.md).

![Criar uma identidade gerida atribuída pelo utilizador](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Criar uma conta do Data Lake Storage Gen2

Crie uma conta de armazenamento Azure Data Lake De armazenamento Gen2.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No clique superior esquerdo **Criar um recurso**.
1. Na caixa de pesquisa, escreva **o armazenamento** e clique na conta **de Armazenamento.**
1. Clique em **Create** (Criar).
1. No ecrã da **conta de armazenamento Criar:**
    1. Selecione o grupo de subscrição e recursos corretos.
    1. Insira um nome para a sua conta Desemarramento de Armazenamento de Dados Gen2.
    1. Clique no separador **Avançado.**
    1. Clique **em Enabled** ao lado **do espaço hierárquico** de nomes em Data Lake Storage **Gen2**.
    1. Clique em **Rever + criar**.
    1. Clique em **Criar**

Para obter mais informações sobre outras opções durante a criação de conta de armazenamento, consulte [Quickstart: Criar uma conta de armazenamento Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Screenshot mostrando criação de conta de armazenamento no portal Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Configurar permissões para a identidade gerida na conta da Data Lake Storage Gen2

Atribua a identidade gerida à função **de Proprietário de Dados de Armazenamento blob** na conta de armazenamento.

1. No [portal Azure,](https://portal.azure.com)aceda à sua conta de armazenamento.
1. Selecione a sua conta de armazenamento e, em seguida, selecione **Access control (IAM)** para exibir as definições de controlo de acesso para a conta. Selecione o **separador funções** para ver a lista de atribuições de funções.

    ![Screenshot mostrando as definições de controlo de acesso ao armazenamento](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Selecione o botão **de atribuição de função + adicionar** uma nova função.
1. Na janela **de atribuição de funções Adicionar,** selecione a função **Proprietário de dados de armazenamento Blob.** Em seguida, selecione a subscrição que tem a conta de identidade e armazenamento gerida. Em seguida, procure localizar a identidade gerida atribuída pelo utilizador que criou anteriormente. Por fim, selecione a identidade gerida e será listada em **membros Selecionados**.

    ![Screenshot mostrando como atribuir um papel RBAC](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Selecione **Guardar**. A identidade atribuída ao utilizador que selecionou está agora listada sob a função selecionada.
1. Depois desta configuração inicial estar concluída, pode criar um cluster através do portal. O cluster deve estar na mesma região de Azure que a conta de armazenamento. No **separador armazenamento** do menu de criação de cluster, selecione as seguintes opções:

    * Para **o tipo de armazenamento primário**, selecione **Azure Data Lake Storage Gen2**.
    * Na **conta de Armazenamento Primário,** procure e selecione a conta de armazenamento gen2 de armazenamento de data lake recém-criada.

    * Em **Identidade**, selecione a identidade gerida recentemente criada pelo utilizador.

        ![Definições de armazenamento para utilização de Data Lake Storage Gen2 com Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Para adicionar uma conta secundária de Data Lake Storage Gen2, ao nível da conta de armazenamento, basta atribuir a identidade gerida criada anteriormente à nova conta de armazenamento de Data Lake Gen2 que pretende adicionar. Informamos que a adição de uma conta gen2 de armazenamento de dados secundário através da lâmina "Contas de armazenamento adicionais" no HDInsight não é suportada.
    > * Pode ativar RA-GRS ou RA-ZRS na conta de armazenamento Azure que o HDInsight utiliza. No entanto, a criação de um cluster contra o ra-GRS ou ra-ZRS ponto final secundário não é suportado.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Criar um cluster com data lake storage gen2 através do Azure CLI

Você pode [baixar um ficheiro de modelo de amostra](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) e descarregar um ficheiro de [parâmetros de amostra](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Antes de utilizar o modelo e o corte de código Azure CLI abaixo, substitua os seguintes espaços reservados pelos seus valores corretos:

| Marcador de posição | Descrição |
|---|---|
| `<SUBSCRIPTION_ID>` | O ID da sua assinatura Azure |
| `<RESOURCEGROUPNAME>` | O grupo de recursos onde pretende a criação da nova conta de cluster e armazenamento. |
| `<MANAGEDIDENTITYNAME>` | O nome da identidade gerida que será dada permissões na sua conta Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | A nova conta Azure Data Lake Storage Gen2 que será criada. |
| `<CLUSTERNAME>` | O nome do seu cluster HDInsight. |
| `<PASSWORD>` | A sua senha escolhida para iniciar sessão no cluster utilizando o SSH e o dashboard Ambari. |

O código abaixo faz os seguintes passos iniciais:

1. Entra na sua conta Azure.
1. Define a subscrição ativa onde serão feitas as operações de criação.
1. Cria um novo grupo de recursos para as novas atividades de implantação.
1. Cria uma identidade gerida atribuída pelo utilizador.
1. Adiciona uma extensão ao CLI Azure para usar funcionalidades para data lake storage gen2.
1. Cria uma nova conta de Data Lake Storage Gen2 usando a `--hierarchical-namespace true` bandeira.

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

Em seguida, inscreva-se no portal. Adicione a nova identidade gerida atribuída ao utilizador na função **de Contribuinte de Dados de Armazenamento** na conta de armazenamento. Este passo é descrito no passo 3 sob [a utilização do portal Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Depois de atribuir a função para a identidade gerida atribuída pelo utilizador, desloque o modelo utilizando o seguinte corte de código.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Criar um cluster com data lake storage gen2 através do Azure PowerShell

A utilização do PowerShell para criar um cluster HDInsight com a Azure Data Lake Storage Gen2 não é suportada atualmente.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controlo de acesso para Data Lake Storage Gen2 em HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Que tipo de permissões suportam o Data Lake Storage Gen2?

Data Lake Storage Gen2 utiliza um modelo de controlo de acesso que suporta tanto o controlo de acesso baseado em funções (RBAC) como as listas de controlo de acesso semelhantes a POSIX (ACLs). Data Lake Storage Gen1 suporta listas de controlo de acesso apenas para controlar o acesso aos dados.

O RBAC utiliza atribuições de funções para aplicar eficazmente conjuntos de permissões aos utilizadores, grupos e princípios de serviço para recursos Azure. Tipicamente, esses recursos Azure estão limitados a recursos de alto nível (por exemplo, contas de Armazenamento Azure). Para o Azure Storage, e também para o Data Lake Storage Gen2, este mecanismo foi estendido ao recurso do sistema de ficheiros.

 Para obter mais informações sobre permissões de ficheiros com o RBAC, consulte [o controlo de acesso baseado em funções Azure (Azure RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Para obter mais informações sobre permissões de ficheiros com ACLs, consulte [as listas de controlo do Access em ficheiros e diretórios.](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Como controlo o acesso aos meus dados na Data Lake Storage Gen2?

A capacidade do seu cluster HDInsight de aceder a ficheiros na Data Lake Storage Gen2 é controlada através de identidades geridas. Uma identidade gerida é uma identidade registada no Azure Ative Directory (Azure AD) cujas credenciais são geridas pela Azure. Com identidades geridas, não precisa de registar os principais serviços em Azure AD. Ou manter credenciais como certificados.

Os serviços Azure têm dois tipos de identidades geridas: atribuídas pelo sistema e atribuídas pelo utilizador. O HDInsight utiliza identidades geridas atribuídas pelo utilizador para aceder ao Data Lake Storage Gen2. A `user-assigned managed identity` é criado como um recurso autónomo Azure. Através de um processo de criação, o Azure cria uma identidade no inquilino do Azure AD no qual a subscrição que está a ser utilizada confia. Depois de criada, a identidade pode ser atribuída a uma ou mais instâncias do serviço do Azure.

O ciclo de vida das identidades atribuídas pelo utilizador é gerido separadamente do ciclo de vida das instâncias do serviço do Azure ao qual estão atribuídas. Para obter mais informações sobre identidades geridas, veja [quais são as identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Como posso definir permissões para os utilizadores de Azure AD consultarem dados na Data Lake Storage Gen2 utilizando a Hive ou outros serviços?

Para definir permissões para os utilizadores consultarem os dados, utilize os grupos de segurança Azure AD como o principal designado em ACLs. Não atribua diretamente permissões de acesso a ficheiros a utilizadores individuais ou a princípios de serviço. Com grupos de segurança AZure AD para controlar o fluxo de permissões, pode adicionar e remover utilizadores ou principais de serviço sem reaplicar ACLs a toda uma estrutura de diretório. Basta adicionar ou remover os utilizadores do grupo de segurança Azure AD apropriado. Os ACLs não são herdados, por isso a recandidúsci dos ACLs requer a atualização do ACL em todos os ficheiros e subdiretivos.

## <a name="access-files-from-the-cluster"></a>Aceder a ficheiros a partir do cluster

Existem várias formas de aceder aos ficheiros em Data Lake Storage Gen2 a partir de um cluster HDInsight.

* **Utilizar o nome completamente qualificado**. Com esta abordagem, fornece o caminho completo para o ficheiro ao qual pretende aceder.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Utilizando o formato de caminho abreviado**. Com esta abordagem, você substitui o caminho até a raiz do cluster com:

    ```
    abfs:///<file.path>/
    ```

* **Utilizar o caminho relativo**. Com esta abordagem, fornece apenas o caminho relativo para o ficheiro ao qual pretende aceder.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exemplos de acesso a dados

Os exemplos baseiam-se numa [ligação ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) ao nó da cabeça do cluster. Os exemplos utilizam os três esquemas URI. Substituir `CONTAINERNAME` e `STORAGEACCOUNT` pelos valores relevantes

#### <a name="a-few-hdfs-commands"></a>Alguns comandos hdfs

1. Crie um arquivo no armazenamento local.

    ```bash
    touch testFile.txt
    ```

1. Crie diretórios no armazenamento de clusters.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copie os dados do armazenamento local para o armazenamento de clusters.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Listar conteúdos de diretório no armazenamento de clusters.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Criação de uma mesa de colmeia

Três localizações de ficheiros são mostradas para fins ilustrativos. Para execução real, utilize apenas uma das `LOCATION` entradas.

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

* [Integração Azure HDInsight com pré-visualização da Data Lake Storage Gen2 - ACL e atualização de segurança](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Tutorial: Extrair, transformar e carregar dados usando Consulta Interativa em Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
