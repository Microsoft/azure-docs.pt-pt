---
title: 'Quickstart: Criar cluster Apache HBase usando o modelo - Azure HDInsight'
description: Este quickstart mostra como usar o modelo de Gestor de Recursos para criar um cluster Apache HBase em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/12/2020
ms.openlocfilehash: 45c9625f0a762a0879473cc64c38d791c804d087
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91530685"
---
# <a name="quickstart-create-apache-hbase-cluster-in-azure-hdinsight-using-arm-template"></a>Quickstart: Criar cluster Apache HBase em Azure HDInsight usando o modelo ARM

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure (modelo ARM) para criar um cluster [Apache HBase](./apache-hbase-overview.md) em Azure HDInsight. HBase é uma base de dados NoSQL de código aberto que é construída em Apache Hadoop e modelada após [o Google BigTable](https://cloud.google.com/bigtable/).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-hbase-linux/azuredeploy.json":::

Dois recursos Azure são definidos no modelo:

* [Microsoft.Storage/storageAstas:](/azure/templates/microsoft.storage/storageaccounts)criar uma conta de armazenamento Azure.
* [Microsoft.HDInsight/cluster:](/azure/templates/microsoft.hdinsight/clusters)crie um cluster HDInsight.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione o botão **'Implementar para azul'** abaixo para iniciar súb9 no Azure e abrir o modelo ARM.

    [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json)

1. Introduza ou selecione os seguintes valores:

    |Propriedade |Descrição |
    |---|---|
    |Subscrição|A partir da lista de drop-down, selecione a subscrição Azure que é usada para o cluster.|
    |Grupo de recursos|A partir da lista de drop-down, selecione o seu grupo de recursos existente ou selecione **Criar novo**.|
    |Localização|O valor irá autopopular com a localização utilizada para o grupo de recursos.|
    |Nome do Cluster|Introduza um nome globalmente exclusivo. Para este modelo, utilize apenas letras minúsculas e números.|
    |Nome de Utilizador de Início de Sessão do Cluster|Forneça o nome de utilizador, o padrão é **administrador**.|
    |Palavra-passe de Início de Sessão do Cluster|Forneça uma senha. A palavra-passe deve ter pelo menos 10 caracteres de comprimento e deve conter pelo menos um dígito, uma maiúscula e uma letra minúscula, um carácter não alfanumérico (exceto os caracteres ' ). |
    |Nome do utilizador Ssh|Forneça o nome de utilizador, o padrão é sshuser|
    |Ssh Password|Forneça a senha.|

    ![Modelo de gestor de recursos HBase](./media/quickstart-resource-manager-template/resource-manager-template-hbase.png)

1. Para mais **uma análise dos termos e condições.** Em seguida, **selecione Eu concordo com os termos e condições acima indicados,** em seguida, **Comprar**. Receberá uma notificação de que a sua implantação está em andamento. A criação de um cluster demora cerca de 20 minutos.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Assim que o cluster for criado, receberá uma notificação **conseguida da Implementação** com uma ligação **de recursos Para Go.** A sua página de grupo de recursos irá listar o seu novo cluster HDInsight e o armazenamento predefinido associado ao cluster. Cada cluster tem uma conta [de Armazenamento Azure](../hdinsight-hadoop-use-blob-storage.md) ou uma dependência [de conta Azure Data Lake Storage.](../hdinsight-hadoop-use-data-lake-store.md) É referida como a conta de armazenamento predefinida. O cluster HDInsight e a sua conta de armazenamento predefinida devem ser indicados na mesma região de Azure. Excluir agrupamentos não apaga a conta de armazenamento.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de completar o arranque rápido, é possível que queira eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Azure Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados.

A partir do portal Azure, navegue até ao seu cluster e selecione **Delete**.

![A screenshot mostra um cluster H D Insight com o grupo de recursos para eliminar.](./media/quickstart-resource-manager-template/azure-portal-delete-hbase.png)

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, elimina tanto o cluster HDInsight como a conta de armazenamento predefinido.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um cluster Apache HBase em HDInsight usando um modelo ARM. No artigo seguinte, aprende-se a consultar a HBase em HDInsight com a HBase Shell.

> [!div class="nextstepaction"]
> [Consulta Apache HBase em Azure HDInsight com HBase Shell](./query-hbase-with-hbase-shell.md)
