---
title: Criar Azure HDInsight - Azure Data Lake Storage Gen2 - portal
description: Aprenda a usar o Azure Data Lake Storage Gen2 com clusters Azure HDInsight usando o portal.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: 5eb6cfc7f0dbc0bc886550e1d601dc52dff66267
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866374"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Criar um cluster com data lake storage gen2 usando o portal Azure

O portal Azure é uma ferramenta de gestão baseada na web para serviços e recursos hospedados na nuvem Microsoft Azure. Neste artigo, aprende-se a criar clusters Azure HDInsight baseados em Linux utilizando o portal. Detalhes adicionais estão disponíveis a partir de [clusters Create HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para criar um cluster HDInsight que usa data lake storage Gen2 para armazenamento, siga estes passos para configurar uma conta de armazenamento que tem um espaço hierárquico de nomes.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Crie uma identidade gerida atribuída ao utilizador, se ainda não tiver uma.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No clique superior esquerdo **Criar um recurso**.
1. Na caixa de pesquisa, **digite** o utilizador atribuído e clique em **Identidade Gerida Atribuída pelo Utilizador.**
1. Clique em **Criar**.
1. Insira um nome para a sua identidade gerida, selecione a subscrição correta, grupo de recursos e localização.
1. Clique em **Criar**.

Para obter mais informações sobre como as identidades geridas funcionam em Azure HDInsight, consulte [identidades geridas em Azure HDInsight](hdinsight-managed-identities.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png" alt-text="Criar uma identidade gerida atribuída ao utilizador":::

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Criar uma conta de armazenamento para usar com data lake storage gen2

Crie uma conta de armazenamento para usar com a Azure Data Lake Storage Gen2.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No clique superior esquerdo **Criar um recurso**.
1. Na caixa de pesquisa, escreva **o armazenamento** e clique na conta **de armazenamento.**
1. Clique em **Criar**.
1. No ecrã da **conta de armazenamento Criar:**
    1. Selecione o grupo de subscrição e recursos corretos.
    1. Insira um nome para a sua conta de armazenamento com data lake storage gen2.
    1. Clique no separador **Avançado.**
    1. Clique **em Enabled** ao lado **do espaço hierárquico** de nomes em Data Lake Storage **Gen2**.
    1. Clique em **Rever + criar**.
    1. Clique em **Criar**

Para obter mais informações sobre outras opções durante a criação de conta de armazenamento, consulte [Quickstart: Criar uma conta de armazenamento para Azure Data Lake Storage Gen2](../storage/blobs/create-data-lake-storage-account.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png" alt-text="Screenshot mostrando criação de conta de armazenamento no portal Azure":::

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>Configurar permissões para a identidade gerida na Data Lake Storage Gen2

Atribua a identidade gerida à função **de Proprietário de Dados de Armazenamento blob** na conta de armazenamento.

1. No [portal Azure,](https://portal.azure.com)aceda à sua conta de armazenamento.
1. Selecione a sua conta de armazenamento e, em seguida, selecione **Access control (IAM)** para exibir as definições de controlo de acesso para a conta. Selecione o **separador funções** para ver a lista de atribuições de funções.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png" alt-text="Screenshot mostrando as definições de controlo de acesso ao armazenamento":::

1. Selecione o botão **de atribuição de função + adicionar** uma nova função.
1. Na janela **de atribuição de funções Adicionar,** selecione a função **Proprietário de dados de armazenamento Blob.** Em seguida, selecione a subscrição que tem a conta de identidade e armazenamento gerida. Em seguida, procure localizar a identidade gerida atribuída pelo utilizador que criou anteriormente. Por fim, selecione a identidade gerida e será listada em **membros Selecionados**.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png" alt-text="Screenshot mostrando como atribuir um papel de Azure":::

1. Selecione **Guardar**. A identidade atribuída ao utilizador que selecionou está agora listada sob a função selecionada.
1. Depois desta configuração inicial estar concluída, pode criar um cluster através do portal. O cluster deve estar na mesma região de Azure que a conta de armazenamento. No **separador armazenamento** do menu de criação de cluster, selecione as seguintes opções:

    * Para **o tipo de armazenamento primário**, selecione **Azure Data Lake Storage Gen2**.
    * Na **conta de Armazenamento Primário,** procure e selecione a conta de armazenamento recém-criada com armazenamento Gen2 de armazenamento de data lake.

    * Em **Identidade**, selecione a identidade gerida recentemente criada pelo utilizador.

        :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png" alt-text="Definições de armazenamento para utilização de Data Lake Storage Gen2 com Azure HDInsight":::

    > [!NOTE]
    > * Para adicionar uma conta de armazenamento secundária com data lake storage gen2, ao nível da conta de armazenamento, basta atribuir a identidade gerida criada anteriormente para o novo Data Lake Storage Gen2 que pretende adicionar. Informamos que a adição de uma conta de armazenamento secundária com data lake storage gen2 através da lâmina "Contas de armazenamento adicionais" no HDInsight não é suportada.
    > * Pode ativar RA-GRS ou RA-ZRS na conta de armazenamento Azure Blob que o HDInsight utiliza. No entanto, a criação de um cluster contra o ra-GRS ou ra-ZRS ponto final secundário não é suportado.

## <a name="delete-the-cluster"></a>Eliminar o cluster

Consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passos seguintes

Criou com sucesso um cluster HDInsight. Agora aprende a trabalhar com o teu grupo.

### <a name="apache-spark-clusters"></a>Aglomerados de faíscas apache

* [Personalize os clusters HDInsight baseados em Linux utilizando ações de script](hdinsight-hadoop-customize-cluster-linux.md)
* [Criar uma aplicação autónoma com o Scala](spark/apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um aglomerado Apache Spark usando Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: Realizar análise de dados interativas usando Spark in HDInsight com ferramentas BI](spark/apache-spark-use-bi-tools.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Aglomerados apache Hadoop

* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Use MapReduce com HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Aglomerados Apache HBase

* [Começar com Apache HBase em HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações Java para Apache HBase em HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
