---
title: Gerir clusters do Apache Hadoop no HDInsight com o portal do Azure
description: Saiba como criar e gerir clusters do HDInsight com o portal do Azure.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: hrasheed
ms.openlocfilehash: 8fccf82371225acd5839537f7629ffe7df138d43
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888156"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gerir clusters do Apache Hadoop no HDInsight com o portal do Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Utilizar o [portal do Azure][azure-portal], pode gerir [Apache Hadoop](https://hadoop.apache.org/) clusters no Azure HDInsight. Utilize o Seletor de separador acima para obter informações sobre como gerir clusters do Hadoop no HDInsight com outras ferramentas.

**Pré-requisitos**
- Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster do Apache Hadoop existente no HDInsight.  Ver [baseado em Linux criar clusters no HDInsight com o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Introdução
Inicie sessão em [https://portal.azure.com](https://portal.azure.com).


## <a name="showClusters"></a> Listar e Mostrar clusters
O **clusters do HDInsight** página listará os clusters existentes.  No portal:
1. Selecione **todos os serviços** no menu à esquerda.
2. Selecione **clusters do HDInsight** sob **ANALYTICS**.

## <a name="homePage"></a> Home page do cluster 
Selecione o nome de cluster a partir da [ **clusters do HDInsight** página](#showClusters).  Esta ação abre o **descrição geral** vista, o que é semelhante à seguinte imagem:

![O Azure essentials de cluster do HDInsight portal](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Menu superior:**  
- **Mover**: Move o cluster para outro grupo de recursos ou para outra subscrição.  
- **Delete**: Elimina o cluster.  
- **Atualizar**:  Atualiza o modo de exibição.

**Menu à esquerda:**  
 - **Menu do canto superior esquerdo**  
    - **Descrição geral**:  Fornece informações gerais para o seu cluster.
    -  **Registo de atividades**: Mostrar e consultar os registos de atividade.
    - **Controlo de acesso (IAM)**: Utilize atribuições de funções.  Ver [utilize atribuições de funções para gerir o acesso aos recursos da sua subscrição do Azure](../role-based-access-control/role-assignments-portal.md).
    - **Etiquetas**: Permite-lhe definir os pares de chave/valor para definir uma taxonomia personalizada dos seus serviços cloud. Por exemplo, pode criar uma chave denominada **projeto**e, em seguida, utilizar um valor para os serviços associados a um projeto específico.
    - **Diagnosticar e resolver problemas**: Apresenta informações de resolução de problemas.
    - **Início Rápido**:  Apresenta informações que o ajuda a começar a utilizar o HDInsight.
    - **Ferramentas**: Informações do HDInsight ferramentas relacionadas.

- **Menu de definições**  
  - **Tamanho do cluster**: Verifique, aumentar e diminuir o número de nós de trabalho do cluster. Ver [dimensionar clusters](hdinsight-administer-use-management-portal.md#scale-clusters).
  - **Limites de quota**: Apresente os núcleos utilizados e disponíveis para a sua subscrição.
  - **SSH + início de sessão do Cluster**: Mostra as instruções para ligar ao cluster através de ligação de Secure Shell (SSH). Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
  - **Geração 1 do Data Lake Storage**: Configure o acesso de geração 1 de armazenamento do Data Lake.  Consulte [início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
  - **Contas de armazenamento**: Ver as contas de armazenamento e as chaves. As contas de armazenamento são configuradas durante o processo de criação do cluster.
  - **Aplicativos**: Adicionar/remover aplicações do HDInsight.  Ver [instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).
  - **Ações de script**: Execute scripts de Bash no cluster. Ver [HDInsight baseado em Linux personalizar clusters com ação de Script](hdinsight-hadoop-customize-cluster-linux.md).
  - **Metastores externas**: Ver os [Apache Hive](https://hive.apache.org/) e [Apache Oozie](https://oozie.apache.org/) metastores. As metastores só pode ser configurada durante o processo de criação do cluster.
  - **Parceiro do HDInsight**: Adicionar/remover o atual parceiro do HDInsight.
  - **Propriedades**: Ver os [propriedades de cluster](#properties).
  - **Bloqueia**: Adicione um bloqueio para impedir que o cluster que está a ser modificada ou eliminada.
  -  **Script de automação**: Apresentar e exportar o modelo Azure Resource Manager para o cluster. Atualmente, só pode exportar a conta de armazenamento do Azure dependente. Ver [Apache Hadoop baseado em Linux criar clusters no HDInsight utilizando modelos Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

- **Menu de monitorização**
  - **Alertas**: Gerir alertas e ações.
  - **Métricas**: Monitorize as métricas de cluster nos registos do Azure Monitor.
  - **Definições de diagnóstico**: Definições no onde pretende armazenar as métricas de diagnóstico.
  - **Operations Management Suite**:  Monitorize o seu cluster nos registos do Azure Operations Management Suite (OMS) e o Azure Monitor.

- **Suporte + resolução de problemas de menu**
  - **Estado de funcionamento do recurso**: Ver [descrição geral do Estado de funcionamento de recursos do Azure](../service-health/resource-health-overview.md).
  - **Novo pedido de suporte**: Pode criar um pedido de suporte com o suporte da Microsoft.
    
## <a name="properties"></a> Propriedades do cluster
Partir do [home page do cluster](#homePage), em **definições** selecione **propriedades**.
* **Nome de anfitrião**: Nome do cluster.
* **URL do cluster**: O URL para a interface de web do Ambari.
* **Secure shell (SSH)**: O nome de utilizador e nome do anfitrião para utilizar em aceder ao cluster através de SSH.
* **Estado**: Um dos: Abortada, aceites, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operacional, executar, erro, eliminar, eliminado, excedido, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ou ClusterCustomization.
* **Região**: Localização do Azure. Para obter uma lista das localizações do Azure suportadas, consulte a **região** caixa de lista pendente [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
* **Data de criação**: A data em que o cluster foi implementado.
* **Sistema operativo**: Qualquer um dos **Windows** ou **Linux**.
* **Tipo de**: Hadoop, HBase, Storm, Spark.
* **Versão**. Ver [versões do HDInsight](hdinsight-component-versioning.md).
* **Subscrição**: Nome da subscrição.
* **Origem de dados predefinida**: O sistema de ficheiros de cluster predefinido.
* **Tamanho de nós de trabalho**: O tamanho da VM selecionado de nós de trabalho.
* **Siga o tamanho do nó**: O tamanho da VM selecionado dos nós principais.
* **Rede virtual**: O nome da rede Virtual que o cluster é implementado, se um tiver sido selecionado no momento da implementação.

## <a name="move-clusters"></a>Clusters de movimentação

Pode mover um cluster do HDInsight para outro grupo de recursos do Azure ou noutra subscrição. 

Partir do [home page do cluster](#homePage):

1. Selecione **mover** no menu superior.
2. Selecione **mover para outro grupo de recursos** ou **mover para outra subscrição**.
3. Siga as instruções da página nova.

## <a name="delete-clusters"></a>Eliminar clusters
Eliminar um cluster não elimina a conta de armazenamento predefinida nem todas as contas de armazenamento ligada. Pode voltar a criar o cluster ao utilizar as mesmas contas de armazenamento e o mesmo metastores. Recomendamos que utilize um novo contentor de BLOBs predefinido quando voltar a criar o cluster.

Partir do [home page do cluster](#homePage):

1. Selecione **eliminar** no menu superior.
2. Siga as instruções da página nova.

Consulte também [colocar em pausa/encerramento clusters](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Adicionar mais contas de armazenamento

Pode adicionar mais contas de armazenamento do Azure e contas de armazenamento do Azure Data Lake depois de um cluster é criado. Para obter mais informações, veja [Adicionar mais contas de armazenamento ao HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Dimensionar clusters
O funcionalidade de dimensionamento do cluster permite-lhe alterar o número de nós de trabalho utilizado por um cluster de HDInsight do Azure, sem ter de voltar a criar o cluster.

> [!NOTE]  
> Apenas clusters com o HDInsight versão 3.1.3 ou superior são suportadas. Se tiver a certeza de que a versão do seu cluster, consulte a página de propriedades.  Ver a lista e Mostrar clusters.

Partir do [home page do cluster](#homePage):

1. Sob **configurações**, selecione **tamanho do Cluster**.
2. Introduza **nós de número de trabalho** na caixa de texto numérica. O limite no número de nós de cluster varia entre subscrições do Azure. Pode contactar o suporte de faturação para aumentar o limite.  As informações de custo refletem as alterações efetuadas para o número de nós.
3. Selecione **Guardar**.

    ![Escala de spark do storm de hbase do HDInsight hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster2.png)

O impacto de alterar o número de nós de dados varia para cada tipo de cluster suportada pelo HDInsight:

* Apache Hadoop

    Pode facilmente aumentar o número de nós de trabalho num cluster do Hadoop que está a ser executado sem afetar todas as tarefas em execução ou pendentes. Também podem ser submetidas a novas tarefas enquanto a operação estiver em curso. Falhas numa operação de dimensionamento são processadas corretamente para que o cluster está sempre deixado no estado funcional.

    Quando um cluster do Hadoop é reduzido, reduzindo o número de nós de dados, alguns dos serviços do cluster são reiniciados. Esse comportamento faz com que tudo em execução e tarefas pendentes a falhar após a conclusão da operação de dimensionamento. Pode, no entanto, volte a submeter as tarefas depois de concluída a operação.
* Apache HBase

    Pode facilmente adicionar ou remover nós ao cluster do HBase durante a execução. Servidores regionais são balanceadas automaticamente dentro de alguns minutos a concluir a operação de dimensionamento. No entanto, também manualmente pode balancear servidores regionais iniciando sessão nó principal do cluster e executar os seguintes comandos a partir de uma janela de linha de comandos:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Para obter mais informações sobre como utilizar a shell de HBase, consulte [começar com um exemplo de Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Pode facilmente adicionar ou remover nós de dados ao seu cluster do Storm, enquanto estiver em execução. No entanto, após a conclusão bem-sucedida da operação de dimensionamento, terá de reequilibrar a topologia.

    Reequilíbrio pode ser feito de duas formas:

  * IU da web do Storm
  * Ferramenta de interface de linha de comandos (CLI)

    Consulte a [documentação do Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    A IU da web de Storm está disponível no cluster do HDInsight:

    ![Reequilíbrio de dimensionamento do HDInsight Storm](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Eis um exemplo de comando da CLI para reequilibrar a topologia do Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```


## <a name="pauseshut-down-clusters"></a>Colocar em pausa/encerramento clusters

A maioria das tarefas do Hadoop é tarefas de lote que só são executadas ocasionalmente. Para a maioria dos clusters do Hadoop, há grandes períodos de tempo que o cluster não está a ser utilizado para processamento. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado.
Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Existem várias formas, pode programar o processo:

* Utilizador do Azure Data Factory. Ver [criar sob demanda baseados em Linux Apache Hadoop clusters no HDInsight com o Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) para a criação de HDInsight a pedido dos serviços ligados.
* Utilize o Azure PowerShell.  Ver [analisar dados de atraso de voo](hdinsight-analyze-flight-delay-data.md).
* Utilize a CLI clássica do Azure. Ver [clusters do HDInsight gerir com o CLI clássica do Azure](hdinsight-administer-use-command-line.md).
* Utilize o HDInsight .NET SDK. Ver [Apache Hadoop submeter tarefas](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Para obter informações sobre preços, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para eliminar um cluster a partir do Portal, veja [eliminar clusters](#delete-clusters)



## <a name="upgrade-clusters"></a>Atualizar clusters

Ver [cluster de HDInsight atualizar para uma versão mais recente](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Abra a IU web do Apache Ambari

Ambari proporciona uma gestão web intuitivo e fácil de usar Hadoop da interface do Usuário apoiada por suas APIs RESTful. Ambari permite que os administradores de sistema gerir e monitorizar clusters do Hadoop.

Partir do [home page do cluster](#homePage):

1. Selecione **dashboards de clusters**.

    ![Menu de cluster de Hadoop do HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Selecione **Ambari doméstica** da página nova.
2. Introduza o nome de utilizador do cluster e a palavra-passe.  O nome de utilizador de cluster predefinido é _administrador_. A web do Ambari interface do Usuário é semelhante a:

Para obter mais informações, consulte [clusters do HDInsight gerir com a IU do Apache Ambari Web](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Alterar palavras-passe
Um cluster do HDInsight pode ter duas contas de usuário. O HDInsight (também conhecido como conta de utilizador do cluster Conta de utilizador HTTP) e a conta de utilizador SSH são criados durante o processo de criação. Pode utilizar o portal para alterar a senha de conta de utilizador do cluster e as ações de script para alterar a conta de utilizador SSH.

### <a name="change-the-cluster-user-password"></a>Alterar a palavra-passe de utilizador do cluster

> [!NOTE]  
> Alterar a palavra-passe de utilizador (administrador) do cluster pode fazer com que as ações de script ser executadas em relação a este cluster para efetuar a ativação. Se tiver quaisquer ações de script persistentes que nós de trabalho de destino, esses scripts podem falhar ao adicionar nós ao cluster através de redimensionar operações. Para obter mais informações sobre as ações de script, consulte [HDInsight personalizar clusters com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

Partir do [home page do cluster](#homePage):
1. Selecione **SSH + Cluster início de sessão** sob **definições**.
2. Selecione **repor credencial**.
3. Introduza e confirme a nova palavra-passe nas caixas de texto.
4. Selecione **OK**.

A palavra-passe é alterada em todos os nós do cluster.

### <a name="change-the-ssh-user-password"></a>Alterar a palavra-passe de utilizador do SSH
1. Com um editor de texto, guarde o seguinte texto como um ficheiro denominado **changepassword.sh**.

    > [!IMPORTANT]  
    > Tem de utilizar um editor que utiliza LF como o fim da linha. Se o editor de utilizar CRLF, em seguida, o script não funciona.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Carregue o ficheiro para uma localização de armazenamento que pode ser acessada a partir do HDInsight com um endereço de HTTP ou HTTPS. Por exemplo, um ficheiro pública armazenar como o armazenamento do OneDrive ou de Blobs do Azure. Guarde o URI (endereço HTTP ou HTTPS) para o ficheiro, conforme este URI é necessário no próximo passo.
3. Do [home page do cluster](#homePage) selecionar **ações de Script** sob **definições**.
4. Partir do **ações de Script** painel, selecione **submeter novo**. 
5. Partir do **submeter ação de script** painel, introduza as seguintes informações:

   | Campo | Value |
   | --- | --- |
   | Tipo de script | Selecione **- personalizado** na lista pendente.|
   | Name |"Alterar ssh palavra-passe" |
   | URI do script de bash |O URI para o ficheiro de changepassword.sh |
   | Tipo ou tipos de nós: (Head, trabalho, Nimbus, Supervisor, Zookeeper, etc.) |✓ para todos os tipos de nó listados |
   | Parâmetros |Introduza o nome de utilizador SSH e, em seguida, a nova palavra-passe. Deve haver um espaço entre o nome de utilizador e a palavra-passe. |
   | Manter esta ação de script... |Deixe este campo desmarcada. |
5. Selecione **criar** para aplicar o script. Quando concluir o script, é possível ligar ao cluster através de SSH com a nova palavra-passe.

## <a name="grantrevoke-access"></a>Conceder/revogar acesso
Clusters do HDInsight tem os seguintes serviços da web HTTP (todos estes serviços tem pontos de extremidade RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Por predefinição, estes serviços são concedidos para o acesso. Pode revogar/conceder o acesso usando [CLI clássica do Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) e [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Localizar o ID de subscrição
Cada cluster é associada a uma subscrição do Azure.  A subscrição do Azure ID é visível a partir da [home page do cluster](#homePage).

## <a name="find-the-resource-group"></a>Encontrar o grupo de recursos
No modo Azure Resource Manager, cada cluster do HDInsight é criado com um grupo do Azure Resource Manager. O grupo do Gestor de recursos está visível a partir da [home page do cluster](#homePage).

## <a name="find-the-storage-accounts"></a>Localizar as contas de armazenamento
Clusters de HDInsight utilizam uma conta de armazenamento do Azure ou o armazenamento do Azure Data Lake para armazenar dados. Cada cluster do HDInsight pode ter uma conta do storage predefinida e um número de contas de armazenamento ligadas. Para listar as contas de armazenamento, a partir do [home page do cluster](#homePage) sob **definições**, selecione **contas de armazenamento**.


## <a name="monitor-jobs"></a>Monitorizar trabalhos
Ver [clusters do HDInsight gerir com a IU do Apache Ambari Web](hdinsight-hadoop-manage-ambari.md#monitoring).


## <a name="monitor-cluster-usage"></a>Monitorizar a utilização do cluster
O **utilização** seção do painel do cluster de HDInsight apresenta informações sobre o número de núcleos disponíveis para a sua subscrição para utilização com o HDInsight, bem como o número de núcleos alocados para este cluster e como eles são alocados para os nós dentro deste cluster. Ver a lista e Mostrar clusters.

> [!IMPORTANT]  
> Para monitorizar os serviços fornecidos pelo cluster do HDInsight, tem de utilizar a Web do Ambari ou a API de REST do Ambari. Para obter mais informações sobre a utilização do Ambari, consulte [clusters do HDInsight gerir com o Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Ligar a um cluster

* [Utilizar o Apache Hive com o HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu algumas funções administrativas básicas. Para obter mais informações, consulte os artigos seguintes:

* [Administrar o HDInsight com o Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administrar o HDInsight com a CLI clássica do Azure](hdinsight-administer-use-command-line.md)
* [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Leia mais sobre como utilizar a interface do Usuário do Apache Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [Detalhes sobre como utilizar a API de REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Utilizar o Apache Hive no HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Apache Pig no HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar o Apache Sqoop no HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Qual a versão do Apache Hadoop é no Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Linha de comandos do Hadoop"
