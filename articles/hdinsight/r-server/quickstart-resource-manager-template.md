---
title: 'Quickstart: Criar cluster de serviços ML usando o modelo - Azure HDInsight'
description: Este quickstart mostra como usar o modelo de Gestor de Recursos para criar um cluster ML Services em Azure HDInsight.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: c15e06d959510d74078a738db2606778d313ad65
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943985"
---
# <a name="quickstart-create-ml-services-cluster-in-azure-hdinsight-using-arm-template"></a>Quickstart: Criar cluster ML Services em Azure HDInsight usando o modelo ARM

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure (modelo ARM) para criar um cluster [ML Services](./r-server-overview.md) em Azure HDInsight. O Microsoft Machine Learning Server está disponível como opção de implementação quando cria clusters HDInsight em Azure. O tipo de cluster que fornece esta opção chama-se ML Services. Esta capacidade fornece aos cientistas de dados, estatísticos e programadores R o acesso a pedido a métodos de análise escaláveis e distribuídos em HDInsight.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-rserver%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-rserver/azuredeploy.json":::

Dois recursos Azure são definidos no modelo:

* [Microsoft.Storage/storageAstas:](/azure/templates/microsoft.storage/storageaccounts)criar uma conta de armazenamento Azure.
* [Microsoft.HDInsight/cluster:](/azure/templates/microsoft.hdinsight/clusters)crie um cluster HDInsight.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione o botão **'Implementar para azul'** abaixo para iniciar súb9 no Azure e abrir o modelo ARM.

    [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-rserver%2Fazuredeploy.json)

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

    ![Implementar o modelo do gestor de recursos HBase](./media/quickstart-resource-manager-template/resource-manager-template-rserver.png)

1. Para mais **uma análise dos termos e condições.** Em seguida, **selecione Eu concordo com os termos e condições acima indicados,** em seguida, **Comprar**. Receberá uma notificação de que a sua implantação está em andamento. A criação de um cluster demora cerca de 20 minutos.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Assim que o cluster for criado, receberá uma notificação **conseguida da Implementação** com uma ligação **de recursos Para Go.** A sua página de grupo de recursos irá listar o seu novo cluster HDInsight e o armazenamento predefinido associado ao cluster. Cada cluster tem uma conta [de armazenamento Azure Blob,](../hdinsight-hadoop-use-blob-storage.md) uma [Azure Data Lake Storage Gen1,](../hdinsight-hadoop-use-data-lake-storage-gen1.md)ou uma  [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) dependência. É referida como a conta de armazenamento predefinida. O cluster HDInsight e a sua conta de armazenamento predefinida devem ser indicados na mesma região de Azure. Excluir agrupamentos não apaga a conta de armazenamento.

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de completar o arranque rápido, é possível que queira eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Azure Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados.

A partir do portal Azure, navegue até ao seu cluster e selecione **Delete**.

[Eliminar o modelo do Gestor de Recursos HBase](./media/quickstart-resource-manager-template/azure-portal-delete-rserver.png)

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, elimina tanto o cluster HDInsight como a conta de armazenamento predefinido.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu a criar um cluster ML Services em HDInsight usando um modelo ARM. No artigo seguinte, aprende-se a executar um script R com O Servidor RStudio que demonstra a utilização do Spark para computações R distribuídas..

> [!div class="nextstepaction"]
> [Execute um script R num cluster de Serviços ML em Azure HDInsight usando o Servidor RStudio](./machine-learning-services-quickstart-job-rstudio.md)
