---
title: Usar o Grafana no Azure HDInsight
description: Saiba como acessar o painel do Grafana com clusters de Apache Hadoop no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: cea0e9709afb65caa23d28be093c28498f2b82d0
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122987"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Acessar o Grafana no Azure HDInsight

O [Grafana](https://grafana.com/) é um popular Construtor de gráficos e painéis de software livre. O Grafana é rico em recursos; Ele não apenas permite que os usuários criem painéis personalizáveis e compartilháveis, também oferece painéis com modelo/script, integração LDAP, várias fontes de dados e muito mais.

Atualmente, no Azure HDInsight, o Grafana tem suporte com os tipos de cluster HBase e consulta interativa.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-apache-hadoop-cluster"></a>Criar um cluster Apache Hadoop

Nesta seção, você cria um cluster de consulta interativa no HDInsight usando um modelo de Azure Resource Manager. Não precisa de ter experiência no modelo do Resource Manager para seguir este artigo. 

1. Clique no botão **Implementar no Azure** abaixo para iniciar sessão no Azure abrir o modelo do Resource Manager no portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Introduza ou selecione os valores conforme sugerido na seguinte captura de ecrã:

    > [!NOTE]  
    > Os valores que forem fornecidos têm de ser exclusivos e devem seguir as diretrizes de nomenclatura. O modelo não realiza verificações de validação. Caso os valores fornecidos já estejam a ser utilizados ou não sigam as diretrizes, receberá um erro depois de ter submetido o modelo.       
    > 
    >
    
    ![Introdução ao modelo do Resource Manager no portal no HDInsight com Linux](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "Implementar o cluster do Hadoop no HDInsight com o portal do Azure e um modelo do gestor do grupo de recursos")

    Introduza ou selecione os seguintes valores:
    
    |Propriedade  |Descrição  |
    |---------|---------|
    |**Subscrição**     |  Selecione a sua subscrição do Azure. |
    |**Grupo de recursos**     | Crie um grupo de recursos ou selecione um existente.  Um grupo de recursos é um contentor de componentes do Azure.  Neste caso, o grupo de recursos contém o cluster do HDInsight e a conta de armazenamento do Azure dependente. |
    |**Location**     | Selecione uma localização do Azure onde quer criar o cluster.  Selecione uma localização mais próxima de si para obter um melhor desempenho. |
    |**Tipo de Cluster**     | Selecione **hadoop**. |
    |**Nome do Cluster**     | Insira um nome para o cluster Apache Hadoop. Uma vez que todos os clusters no HDInsight partilham o mesmo espaço de nomes DNS, este nome tem de ser único. O nome pode ser composto por um máximo de 59 carateres, incluindo letras, números e hífenes. O primeiro e último carateres do nome não podem ser hífenes. |
    |**Nome e palavra-passe de início de sessão no cluster**     | O nome de início de sessão predefinido é **admin**. A palavra-passe tem de ter no mínimo 10 carateres e tem de conter, pelo menos, um número, uma letra maiúscula e uma letra minúscula, e um caráter não alfanumérico (exceto os carateres ' " `\). Certifique-se de que **não escolhe** uma palavra-passe comum, tal como "Pass@word1".|
    |**Nome de utilizador e palavra-passe de SSH**     | O nome de utilizador predefinido é **sshuser**.  Pode mudar o nome de utilizador do SSH.  A palavra-passe de utilizador do SSH tem os mesmos requisitos que a palavra-passe de início de sessão do cluster.|

    Algumas propriedades foram codificadas no modelo.  Pode configurar estes valores a partir do modelo. Para obter mais explicações sobre essas propriedades, consulte [criar Apache Hadoop clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Selecione **Concordo com os termos e condições indicados acima** e **Afixar ao dashboard**. Em seguida, clique em **Comprar**. Irá ver um novo mosaico intitulado **A submeter a implementação** no dashboard do portal. A criação de um cluster demora cerca de 20 minutos.

    ![Progresso do implantação de modelo do Azure](./media/hdinsight-grafana/deployment-progress-tile.png "Progresso do implantação de modelo do Azure")

4. Assim que o cluster for criado, a legenda do mosaico é alterada para o nome do grupo de recursos que especificou. O mosaico também indica o cluster do HDInsight que é criado no grupo de recursos.

    ![Introdução ao grupo de recursos no HDInsight com Linux](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Grupo de recursos de cluster no Azure HDInsight")

5. O mosaico indica igualmente o armazenamento predefinido associado ao cluster. Cada cluster tem uma dependência de [conta do Armazenamento do Azure](../hdinsight-hadoop-use-blob-storage.md) ou de [conta do Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). É designada de conta de armazenamento predefinida. O cluster HDInsight e sua conta de armazenamento padrão devem estar colocalizados na mesma região do Azure. A eliminação dos clusters não elimina a conta de armazenamento.
    

> [!NOTE]  
> Para outros métodos de criação de cluster e noções básicas sobre as propriedades usadas neste artigo, consulte [Criar clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="access-the-grafana-dashboard"></a>Acessar o painel do Grafana

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **clusters HDInsight**e, em seguida, selecione o nome do cluster que você criou na última seção.

3. Em **links rápidos**, clique em **painel do cluster**.

    ![Portal do painel do cluster HDInsight](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "Painel do cluster HDInsight no portal")

4. No painel, clique no bloco **Grafana** . Como alternativa, navegue até o `/grafana/` caminho da URL do cluster. Por exemplo, `https://<clustername>.azurehdinsight.net/grafana/`.

5. Insira as credenciais de usuário do cluster Hadoop.

6. O painel do Grafana é exibido e é semelhante a este exemplo:

    ![Painel da Web do HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Painel do HDInsight Grafana")

## <a name="clean-up-resources"></a>Limpar recursos
Depois de concluir o artigo, pode achar conveniente eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. 

> [!NOTE]  
> Se avançar *imediatamente* para o tutorial seguinte para saber como executar operações de ETL com o Hadoop no HDInsight, é recomendado que mantenha o cluster em execução. Isto porque tem de voltar a criar um cluster do Hadoop no tutorial. No entanto, se não avançar já para o tutorial seguinte, tem de eliminar o cluster agora.

**Para eliminar o cluster e/ou a conta de armazenamento predefinida**

1. Volte ao separador do browser onde tem o portal do Azure. Deverá estar na página de descrição geral do cluster. Se apenas quiser eliminar o cluster, mas quiser manter a conta de armazenamento predefinida, selecione **Eliminar**.

    ![Ícone Excluir cluster portal do Azure](./media/hdinsight-grafana/hdinsight-delete-cluster.png "Excluir cluster HDInsight")

2. Se quiser eliminar o cluster e a conta de armazenamento predefinida, selecione o nome do grupo de recursos (realçado na captura de ecrã anterior) para abrir a página do grupo de recursos.

3. Selecione **Eliminar grupo de recursos** para eliminar o grupo de recursos, o qual contém o cluster e a conta de armazenamento predefinida. Tenha em atenção que a eliminação do grupo de recursos elimina a conta de armazenamento. Se pretender manter a conta do Storage, opte por eliminar apenas o cluster.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu como criar um cluster HDInsight baseado em Linux usando um modelo do Resource Manager e como executar consultas de Apache Hive básicas. No artigo seguinte, irá saber como executar uma operação de ETL (extração, transformação e carregamento) com o Hadoop no HDInsight.

> [!div class="nextstepaction"]
>[Extrair, transformar e carregar dados usando a consulta interativa no HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)

Se estiver pronto para começar a trabalhar com os seus próprios dados e precisar de saber mais sobre a forma como o HDInsight armazena os dados ou como carregar dados para o HDInsight, consulte os seguintes artigos:

* Para obter informações sobre como o HDInsight utiliza o Armazenamento do Azure, consulte [Utilizar o Armazenamento do Azure com o HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar dados para o HDInsight, veja [Upload data to HDInsight](../hdinsight-upload-data.md) (Carregar dados para o HDInsight).

Saiba mais sobre como analisar os dados com o HDInsight, veja os seguintes artigos:

* Para saber mais sobre como usar o hive com o HDInsight, incluindo como executar consultas de hive do Visual Studio, confira [usar o Apache Hive com o hdinsight](../hdinsight-use-hive.md).
* Para saber mais sobre o Pig, uma linguagem usada para transformar dados, consulte [usar o Apache Pig com o HDInsight](../hdinsight-use-pig.md).
* Para saber mais sobre o MapReduce, uma forma de escrever programas que processam dados no Hadoop, veja [Use MapReduce with HDInsight](../hdinsight-use-mapreduce.md) (Utilizar o MapReduce com o HDInsight).
* Para saber mais sobre como utilizar as Ferramentas do HDInsight para o Visual Studio para analisar dados no HDInsight, consulte [Começar a utilizar as ferramentas Hadoop do Visual Studio para o HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).



Se gostaria de saber mais sobre como criar ou gerir um cluster do HDInsight, consulte os seguintes artigos:

* Para saber mais sobre a gestão do seu cluster do HDInsight baseado em Linux, consulte [Gerir clusters do HDInsight com o Ambari](../hdinsight-hadoop-manage-ambari.md).
* Para saber mais sobre as opções que pode selecionar ao criar um cluster do HDInsight, consulte [Criar HDInsight no Linux utilizando opções personalizadas](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
