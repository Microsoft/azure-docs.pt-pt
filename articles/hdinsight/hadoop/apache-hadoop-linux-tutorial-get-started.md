---
title: 'Quickstart: Criar o cluster Hadoop Apache no Azure HDInsight usando o modelo de Gestor de Recursos'
description: Neste arranque rápido, você cria o cluster Apache Hadoop em Azure HDInsight usando o modelo de Gestor de Recursos
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: ae0f29b8085bd9637f527f2a58229dd89ce6933b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064679"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Quickstart: Criar o cluster Hadoop Apache no Azure HDInsight usando o modelo de Gestor de Recursos

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure para criar um cluster [Apache Hadoop](./apache-hadoop-introduction.md) em Azure HDInsight. Hadoop foi o quadro original de código aberto para o processamento e análise distribuídos de grandes conjuntos de dados em clusters. O ecossistema Hadoop inclui software e utilitários relacionados, incluindo Apache Hive, Apache HBase, Spark, Kafka, entre muitos outros.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]
  
Atualmente, o HDInsight inclui [sete tipos diferentes de cluster](../hdinsight-overview.md#cluster-types-in-hdinsight). Cada tipo de cluster suporta um conjunto diferente de componentes. Todos os tipos de cluster suportam o Hive. Para obter uma lista dos componentes suportados no HDInsight, consulte [Quais são as novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight?](../hdinsight-component-versioning.md)  

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-apache-hadoop-cluster"></a>Criar um aglomerado de Hadoop Apache

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password).

:::code language="json" source="~/quickstart-templates/101-hdinsight-linux-ssh-password/azuredeploy.json" range="1-148":::


Dois recursos Azure são definidos no modelo:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): crie uma conta de armazenamento Azure.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): crie um cluster HDInsight.

### <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione o botão **Deploy para Azure** abaixo para iniciar sessão no Azure e abra o modelo de Gestor de Recursos.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Introduza ou selecione os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Subscrição|A partir da lista de lançamentos, selecione a subscrição Azure que é usada para o cluster.|
    |Grupo de recursos|A partir da lista de drop-down, selecione o seu grupo de recursos existente, ou selecione **Criar novo**.|
    |Localização|O valor irá autopovoar-se com a localização utilizada para o grupo de recursos.|
    |Nome do Cluster|Introduza um nome globalmente exclusivo. Para este modelo, utilize apenas letras minúsculas e números.|
    |Tipo de Cluster | Selecione **hadoop**. |
    |Nome de Utilizador de Início de Sessão do Cluster|Forneça o nome de utilizador, o predefinido é **o administrador**.|
    |Palavra-passe de Início de Sessão do Cluster|Forneça uma senha. A palavra-passe deve ter pelo menos 10 caracteres de comprimento e deve conter pelo menos um dígito, uma maiúscula e uma letra minúscula, um caracteres não alfanuméricos (exceto os caracteres ' ' ). |
    |Nome do utilizador SSH|Forneça o nome de utilizador, o padrão é **sshuser**|
    |Palavra-passe ssh|Forneça a senha.|

    Algumas propriedades foram codificadas no modelo.  Pode configurar estes valores a partir do modelo. Para obter mais explicações sobre estas propriedades, consulte [Create Apache Hadoop clusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Os valores que forem fornecidos têm de ser exclusivos e devem seguir as diretrizes de nomenclatura. O modelo não realiza verificações de validação. Caso os valores fornecidos já estejam a ser utilizados ou não sigam as diretrizes, receberá um erro depois de ter submetido o modelo.  

    ![HDInsight Linux inicia modelo de Gestor de Recursos no portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Implementar cluster Hadoop no HDInsight usando o portal Azure e um modelo de gestor de grupo de recursos")

1. Reveja os **TERMOS E CONDIÇÕES.** Em seguida, selecione **Concordo com os termos e condições acima indicados,** em **seguida, comprar**. Receberá uma notificação de que a sua implantação está em andamento. A criação de um cluster demora cerca de 20 minutos.

## <a name="review-deployed-resources"></a>Rever os recursos implantados

Assim que o cluster for criado, receberá uma notificação **de Implementação bem sucedida** com uma ligação de recurso **Go.** A página do grupo Recursos listará o seu novo cluster HDInsight e o armazenamento predefinido associado ao cluster. Cada cluster tem uma conta [de Armazenamento Azure](../hdinsight-hadoop-use-blob-storage.md) ou uma dependência da conta de armazenamento de [lagos de dados Azure.](../hdinsight-hadoop-use-data-lake-store.md) É referida como a conta de armazenamento padrão. O cluster HDInsight e a sua conta de armazenamento predefinido devem ser colocalizados na mesma região de Azure. Eliminar aglomerados não apaga a conta de armazenamento.

> [!NOTE]  
> Para outros métodos de criação de clusters e compreensão das propriedades utilizadas neste arranque rápido, consulte [Create HDInsight clusters](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="clean-up-resources"></a>Limpar recursos

Depois de completar o arranque rápido, poderá querer eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Armazenamento Azure, para que possa eliminar com segurança um cluster quando não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar clusters quando não estão em uso.

> [!NOTE]  
> Se avançar *imediatamente* para o tutorial seguinte para saber como executar operações de ETL com o Hadoop no HDInsight, é recomendado que mantenha o cluster em execução. Isto porque no tutorial tens de criar um aglomerado hadoop outra vez. No entanto, se não avançar já para o tutorial seguinte, tem de eliminar o cluster agora.

A partir do portal Azure, navegue até ao seu cluster e selecione **Eliminar**.

![HDInsight elimina cluster do portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "HDInsight elimina cluster do portal")

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, elimina tanto o cluster HDInsight como a conta de armazenamento predefinida.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um cluster Apache Hadoop no HDInsight usando um modelo de Gestor de Recursos. No artigo seguinte, irá saber como executar uma operação de ETL (extração, transformação e carregamento) com o Hadoop no HDInsight.

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar dados usando consulta interativa no HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
