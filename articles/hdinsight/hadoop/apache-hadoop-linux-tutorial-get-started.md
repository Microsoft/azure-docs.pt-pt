---
title: 'Início rápido: Criar clusters do Apache Hadoop com o Resource Manager - Azure HDInsight'
description: Saiba como criar clusters do HDInsight.
keywords: guia de introdução do hadoop, hadoop linux, início rápido do hadoop, introdução do hive, início rápido do hive
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 9084e8e0fe7707b5f115df80792cea102772b8e9
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861652"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Início rápido: Criar cluster do Apache Hadoop no HDInsight do Azure com o modelo do Resource Manager

Neste guia de introdução, saiba como criar um cluster do Apache Hadoop no HDInsight do Azure através de um modelo do Resource Manager.

Modelos semelhantes podem ser visualizados no [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular). A referência de modelo pode ser encontrada [aqui](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).  Também pode criar um cluster com o [portal do Azure](apache-hadoop-linux-create-cluster-get-started-portal.md).  

Atualmente, o HDInsight inclui [sete tipos diferentes de cluster](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Cada tipo de cluster suporta um conjunto diferente de componentes. Todos os tipos de cluster suportam o Hive. Para obter uma lista dos componentes suportados no HDInsight, consulte [Quais são as novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight?](../hdinsight-component-versioning.md)  

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

<a name="create-cluster"></a>
## <a name="create-a-hadoop-cluster"></a>Criar um cluster do Hadoop

1. Selecione o **implementar no Azure** botão abaixo para iniciar sessão no Azure e abrir o modelo do Resource Manager no portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Introduza ou selecione os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |**Subscrição**     |  Selecione a sua subscrição do Azure. |
    |**Grupo de recursos**     | Crie um grupo de recursos ou selecione um existente.  Um grupo de recursos é um contentor de componentes do Azure.  Neste caso, o grupo de recursos contém o cluster do HDInsight e a conta de armazenamento do Azure dependente. |
    |**Localização**     | Selecione uma localização do Azure onde quer criar o cluster.  Selecione uma localização mais próxima de si para obter um melhor desempenho. |
    |**Nome do Cluster**     | Introduza um nome para o cluster do Hadoop. Uma vez que todos os clusters no HDInsight partilham o mesmo espaço de nomes DNS, este nome tem de ser único. O nome pode conter apenas letras minúsculas, números e hífenes e tem de começar com uma letra.  Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen.  O nome também tem de ter entre 3 e 59 carateres de comprimento. |
    |**Tipo de Cluster**     | Selecione **hadoop**. |
    |**Nome e palavra-passe de início de sessão no cluster**     | O nome de início de sessão predefinido é **admin**. A palavra-passe tem de ter no mínimo 10 carateres e tem de conter, pelo menos, um número, uma letra maiúscula e uma letra minúscula, e um caráter não alfanumérico (exceto os carateres ' " `\). Certifique-se de que **não escolhe** uma palavra-passe comum, tal como "Pass@word1".|
    |**Nome de utilizador e palavra-passe de SSH**     | O nome de utilizador predefinido é **sshuser**.  Pode mudar o nome de utilizador do SSH.  A palavra-passe de utilizador do SSH tem os mesmos requisitos que a palavra-passe de início de sessão do cluster.|

    Algumas propriedades foram codificadas no modelo.  Pode configurar estes valores a partir do modelo. Para obter mais explicações sobre estas propriedades, consulte [Apache Hadoop criar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Os valores que forem fornecidos têm de ser exclusivos e devem seguir as diretrizes de nomenclatura. O modelo não realiza verificações de validação. Caso os valores fornecidos já estejam a ser utilizados ou não sigam as diretrizes, receberá um erro depois de ter submetido o modelo.  

    ![Linux de HDInsight obtém o modelo de Gestor de recursos de introdução no portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "cluster de implementar o Hadoop no HDInsight com o portal do Azure e um modelo de Gestor do grupo de recursos")

3. Selecione **concordo com os termos e condições indicados acima**e, em seguida, selecione **Compra**. Receberá uma notificação que sua implementação está em curso.  A criação de um cluster demora cerca de 20 minutos.

4. Assim que o cluster é criado, receberá um **implementação concluída com êxito** notificação com uma **vá para o grupo de recursos** ligação.  Sua **grupo de recursos** página irá listar o novo cluster do HDInsight e o armazenamento de predefinido associada ao cluster. Cada cluster tem um [conta de armazenamento do Azure](../hdinsight-hadoop-use-blob-storage.md) ou uma [conta de armazenamento do Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md) dependência. É designada de conta de armazenamento predefinida. O cluster do HDInsight e a sua conta do storage predefinida tem de ser colocalizados na mesma região do Azure. A eliminação dos clusters não elimina a conta de armazenamento.

> [!NOTE]  
> Para outros métodos de criação de clusters e compreender as propriedades utilizadas neste guia de introdução, consulte [clusters do HDInsight criar](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="clean-up-resources"></a>Limpar recursos
Depois de concluir o início rápido, pode querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

> [!NOTE]  
> Se avançar *imediatamente* para o tutorial seguinte para saber como executar operações de ETL com o Hadoop no HDInsight, é recomendado que mantenha o cluster em execução. Isto acontece porque o tutorial tem de criar um cluster do Hadoop novamente. No entanto, se não avançar já para o tutorial seguinte, tem de eliminar o cluster agora.

**Para eliminar o cluster e/ou a conta de armazenamento predefinida**

1. Volte ao separador do browser onde tem o portal do Azure. Deverá estar na página de descrição geral do cluster. Se apenas quiser eliminar o cluster, mas quiser manter a conta de armazenamento predefinida, selecione **Eliminar**.

    ![Eliminação de cluster do HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Eliminar cluster do HDInsight")

2. Se quiser eliminar o cluster e a conta de armazenamento predefinida, selecione o nome do grupo de recursos (realçado na captura de ecrã anterior) para abrir a página do grupo de recursos.

3. Selecione **Eliminar grupo de recursos** para eliminar o grupo de recursos, o qual contém o cluster e a conta de armazenamento predefinida. Tenha em atenção que a eliminação do grupo de recursos elimina a conta de armazenamento. Se pretender manter a conta do Storage, opte por eliminar apenas o cluster.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a criar um cluster do Apache Hadoop no HDInsight com um modelo do Resource Manager. No artigo seguinte, irá saber como executar uma operação de ETL (extração, transformação e carregamento) com o Hadoop no HDInsight.

> [!div class="nextstepaction"]
>[Extrair, transformar e carregar dados com o Apache Hive no HDInsight](../hdinsight-analyze-flight-delay-data-linux.md)

Se estiver pronto para começar a trabalhar com os seus próprios dados e precisar de saber mais sobre a forma como o HDInsight armazena os dados ou como carregar dados para o HDInsight, consulte os seguintes artigos:

* Para obter informações sobre como o HDInsight utiliza o Armazenamento do Azure, consulte [Utilizar o Armazenamento do Azure com o HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como criar um cluster do HDInsight com o armazenamento do Data Lake, veja [início rápido: Configurar clusters no HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* Para obter informações sobre como carregar dados para o HDInsight, veja [Upload data to HDInsight](../hdinsight-upload-data.md) (Carregar dados para o HDInsight).
* [Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

Saiba mais sobre como analisar os dados com o HDInsight, veja os seguintes artigos:

* Para saber mais sobre como utilizar o Hive com HDInsight, incluindo como executar consultas do Hive a partir do Visual Studio, veja [utilizar o Apache Hive com HDInsight](hdinsight-use-hive.md).
* Para saber mais sobre o Pig, uma linguagem utilizada para transformar dados, veja [utilizar o Apache Pig com o HDInsight](hdinsight-use-pig.md).
* Para saber mais sobre o MapReduce, uma forma de escrever programas que processam dados no Hadoop, veja [Use MapReduce with HDInsight](hdinsight-use-mapreduce.md) (Utilizar o MapReduce com o HDInsight).
* Para saber mais sobre como utilizar as Ferramentas do HDInsight para o Visual Studio para analisar dados no HDInsight, consulte [Começar a utilizar as ferramentas Hadoop do Visual Studio para o HDInsight](apache-hadoop-visual-studio-tools-get-started.md).
* Para saber mais sobre como utilizar as Ferramentas do HDInsight para o VSCode para analisar dados no HDInsight, consulte [Utilizar as Ferramentas do HDInsight para o Visual Studio Code](../hdinsight-for-vscode.md).


Se gostaria de saber mais sobre como criar ou gerir um cluster do HDInsight, consulte os seguintes artigos:

* Para saber mais sobre a gestão do seu cluster do HDInsight baseado em Linux, veja [clusters do HDInsight gerir com o Apache Ambari](../hdinsight-hadoop-manage-ambari.md).
* Para saber mais sobre as opções que pode selecionar ao criar um cluster do HDInsight, consulte [Criar HDInsight no Linux utilizando opções personalizadas](../hdinsight-hadoop-provision-linux-clusters.md).

Para saber mais sobre a criação de cluster do HDInsight com modelos Azure Resource Manager, consulte:

* [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular).
* [Referência de modelo do Azure](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).

[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md