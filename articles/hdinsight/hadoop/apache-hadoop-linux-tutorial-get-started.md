---
title: 'Início rápido: Apache Hadoop & Gerenciador de recursos-Azure HDInsight'
description: Neste guia de início rápido, você cria Apache Hadoop cluster no Azure HDInsight usando o modelo do Resource Manager
keywords: guia de introdução do hadoop, hadoop linux, início rápido do hadoop, introdução do hive, início rápido do hive
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: b554fcd4d4175aeac88df14eebbbaf4a8ba49f4c
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044807"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Início rápido: criar Apache Hadoop cluster no Azure HDInsight usando o modelo do Resource Manager

Neste guia de início rápido, você aprende a criar um cluster Apache Hadoop no Azure HDInsight usando um modelo do Resource Manager.

Modelos semelhantes podem ser exibidos em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular). A referência de modelo pode ser encontrada [aqui](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).  Você também pode criar um cluster usando o [portal do Azure](apache-hadoop-linux-create-cluster-get-started-portal.md).  

Atualmente, o HDInsight inclui [sete tipos diferentes de cluster](../hdinsight-overview.md#cluster-types-in-hdinsight). Cada tipo de cluster suporta um conjunto diferente de componentes. Todos os tipos de cluster suportam o Hive. Para obter uma lista dos componentes suportados no HDInsight, consulte [Quais são as novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight?](../hdinsight-component-versioning.md)  

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

<a name="create-cluster"></a>
## <a name="create-a-hadoop-cluster"></a>Criar um cluster do Hadoop

1. Selecione o botão **implantar no Azure** abaixo para entrar no Azure e abrir o modelo do Resource Manager na portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Introduza ou selecione os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |**Subscrição**     |  Selecione a sua subscrição do Azure. |
    |**Grupo de recursos**     | Crie um grupo de recursos ou selecione crie um existente.  Um grupo de recursos é um contentor de componentes do Azure.  Neste caso, o grupo de recursos contém o cluster do HDInsight e a conta de armazenamento do Azure dependente. |
    |**Localização**     | Selecione uma localização do Azure onde quer criar o cluster.  Selecione uma localização mais próxima de si para obter um melhor desempenho. |
    |**Nome do Cluster**     | Introduza um nome para o cluster do Hadoop. Uma vez que todos os clusters no HDInsight partilham o mesmo espaço de nomes DNS, este nome tem de ser único. O nome pode conter apenas letras minúsculas, números e hifens e deve começar com uma letra.  Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen.  O nome também deve ter entre 3 e 59 caracteres de comprimento. |
    |**Tipo de Cluster**     | Selecione **hadoop**. |
    |**Nome e palavra-passe de início de sessão no cluster**     | O nome de logon padrão é **admin**. A senha deve ter pelo menos 10 caracteres de comprimento e deve conter pelo menos um dígito, uma letra maiúscula e uma letras minúsculas, um caractere não alfanumérico (exceto os caracteres ' "' \). Certifique-se de que **não escolhe** uma palavra-passe comum, tal como "Pass@word1".|
    |**Nome de utilizador e palavra-passe de SSH**     | O nome de utilizador predefinido é **sshuser**.  Pode mudar o nome de utilizador do SSH.  A palavra-passe de utilizador do SSH tem os mesmos requisitos que a palavra-passe de início de sessão do cluster.|

    Algumas propriedades foram codificadas no modelo.  Pode configurar estes valores a partir do modelo. Para obter mais explicações sobre essas propriedades, consulte [criar Apache Hadoop clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Os valores que forem fornecidos têm de ser exclusivos e devem seguir as diretrizes de nomenclatura. O modelo não realiza verificações de validação. Caso os valores fornecidos já estejam a ser utilizados ou não sigam as diretrizes, receberá um erro depois de ter submetido o modelo.  

    ![O HDInsight Linux inicia o modelo do Resource Manager no portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Implantar o cluster Hadoop no HDInsight usando o portal do Azure e um modelo do Gerenciador de grupo de recursos")

3. Selecione **concordo com os termos e condições declarados acima**e, em seguida, selecione **comprar**. Você receberá uma notificação informando que sua implantação está em andamento.  A criação de um cluster demora cerca de 20 minutos.

4. Depois que o cluster for criado, você receberá uma notificação de **implantação bem-sucedida** com um link **ir para o grupo de recursos** .  A página do **grupo de recursos** listará o novo cluster HDInsight e o armazenamento padrão associado ao cluster. Cada cluster tem uma [conta de armazenamento do Azure](../hdinsight-hadoop-use-blob-storage.md) ou uma Azure data Lake Storage dependência de [conta](../hdinsight-hadoop-use-data-lake-store.md) . É designada de conta de armazenamento predefinida. O cluster HDInsight e sua conta de armazenamento padrão devem estar colocalizados na mesma região do Azure. A eliminação dos clusters não elimina a conta de armazenamento.

> [!NOTE]  
> Para outros métodos de criação de cluster e noções básicas sobre as propriedades usadas neste guia de início rápido, consulte [Criar clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, talvez você queira excluir o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

> [!NOTE]  
> Se tenciona avançar *imediatamente* para o tutorial seguinte para saber como executar operações de ETL com o Hadoop no HDInsight, talvez seja conveniente manter o cluster em execução. Isso ocorre porque, no tutorial, você precisa criar um cluster Hadoop novamente. No entanto, se não avançar já para o tutorial seguinte, tem de eliminar o cluster agora.

**Para eliminar o cluster e/ou a conta de armazenamento predefinida**

1. Volte ao separador do browser onde tem o portal do Azure. Deverá estar na página de descrição geral do cluster. Se apenas quiser eliminar o cluster, mas quiser manter a conta de armazenamento predefinida, selecione **Eliminar**.

    ![Excluir cluster do HDInsight do portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Excluir cluster do HDInsight do portal")

2. Se quiser eliminar o cluster e a conta de armazenamento predefinida, selecione o nome do grupo de recursos (realçado na captura de ecrã anterior) para abrir a página do grupo de recursos.

3. Selecione **Eliminar grupo de recursos** para eliminar o grupo de recursos, o qual contém o cluster e a conta de armazenamento predefinida. Tenha em atenção que a eliminação do grupo de recursos elimina a conta de armazenamento. Se pretender manter a conta do Storage, opte por eliminar apenas o cluster.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar um cluster Apache Hadoop no HDInsight usando um modelo do Resource Manager. No artigo seguinte, vai aprender a realizar uma operação de ETL (extração, transformação e carregamento) com o Hadoop no HDInsight.

> [!div class="nextstepaction"]
>[Extrair, transformar e carregar dados usando a consulta interativa no HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)