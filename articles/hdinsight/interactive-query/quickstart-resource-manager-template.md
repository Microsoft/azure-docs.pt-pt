---
title: 'Quickstart: Criar cluster de consulta interativa usando o modelo - Azure HDInsight'
description: Este quickstart mostra como usar o modelo de Gestor de Recursos para criar um cluster de consulta interativa em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 02dbb129fa32ce72a94a098c56b0a81186783ffd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79485819"
---
# <a name="quickstart-create-interactive-query-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Quickstart: Criar cluster de consulta interativa no Azure HDInsight usando o modelo de Gestor de Recursos

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure para criar um cluster [de consulta interativa](./apache-interactive-query-get-started.md) em Azure HDInsight. Consulta interativa (também chamada Apache Hive LLAP, ou [Processamento Analítico de Baixa Latência)](https://cwiki.apache.org/confluence/display/Hive/LLAP)é um tipo de [cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)Azure HDInsight .

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-interactive-query-cluster"></a>Criar um cluster de consulta interativa

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-interactive-hive).

:::code language="json" source="~/quickstart-templates/101-hdinsight-interactive-hive/azuredeploy.json" range="1-158":::


Dois recursos Azure são definidos no modelo:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): crie uma conta de armazenamento Azure.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): crie um cluster HDInsight.

### <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione o botão **Deploy para Azure** abaixo para iniciar sessão no Azure e abra o modelo de Gestor de Recursos.

    [![Desdobre para Azure](./media/quickstart-resource-manager-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json)

1. Introduza ou selecione os seguintes valores:

    |Propriedade |Descrição |
    |---|---|
    |Subscrição|A partir da lista de lançamentos, selecione a subscrição Azure que é usada para o cluster.|
    |Grupo de recursos|A partir da lista de drop-down, selecione o seu grupo de recursos existente, ou selecione **Criar novo**.|
    |Localização|O valor irá autopovoar-se com a localização utilizada para o grupo de recursos.|
    |Nome do Cluster|Introduza um nome globalmente exclusivo. Para este modelo, utilize apenas letras minúsculas e números.|
    |Nome de Utilizador de Início de Sessão do Cluster|Forneça o nome de utilizador, o predefinido é **o administrador**.|
    |Palavra-passe de Início de Sessão do Cluster|Forneça uma senha. A palavra-passe deve ter pelo menos 10 caracteres de comprimento e deve conter pelo menos um dígito, uma maiúscula e uma letra minúscula, um caracteres não alfanuméricos (exceto os caracteres ' ' ). |
    |Nome do utilizador SSH|Forneça o nome de utilizador, o padrão é sshuser|
    |Palavra-passe ssh|Forneça a senha.|

    ![Modelo de gestor de recursos HBase](./media/quickstart-resource-manager-template/resource-manager-template-hive.png)

1. Reveja os **TERMOS E CONDIÇÕES.** Em seguida, selecione **Concordo com os termos e condições acima indicados,** em **seguida, comprar**. Receberá uma notificação de que a sua implantação está em andamento. A criação de um cluster demora cerca de 20 minutos.

## <a name="review-deployed-resources"></a>Rever os recursos implantados

Assim que o cluster for criado, receberá uma notificação **de Implementação bem sucedida** com uma ligação de recurso **Go.** A página do grupo Recursos listará o seu novo cluster HDInsight e o armazenamento predefinido associado ao cluster. Cada cluster tem uma conta [de Armazenamento Azure](../hdinsight-hadoop-use-blob-storage.md) ou uma dependência da conta de armazenamento de [lagos de dados Azure.](../hdinsight-hadoop-use-data-lake-store.md) É referida como a conta de armazenamento padrão. O cluster HDInsight e a sua conta de armazenamento predefinido devem ser colocalizados na mesma região de Azure. Eliminar aglomerados não apaga a conta de armazenamento.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de completar o arranque rápido, poderá querer eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Armazenamento Azure, para que possa eliminar com segurança um cluster quando não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar clusters quando não estão em uso.

A partir do portal Azure, navegue até ao seu cluster e selecione **Eliminar**.

![Modelo de gestor de recursos HBase](./media/quickstart-resource-manager-template/azure-portal-delete-hive.png)

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, elimina tanto o cluster HDInsight como a conta de armazenamento predefinida.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um cluster de consulta interativa no HDInsight usando um modelo de Gestor de Recursos. No próximo artigo, aprende-se a usar o Apache Zeppelin para fazer consultas à Apache Hive.

> [!div class="nextstepaction"]
> [Execute consultas de Hive Apache em Azure HDInsight com Apache Zeppelin](./hdinsight-connect-hive-zeppelin.md)
