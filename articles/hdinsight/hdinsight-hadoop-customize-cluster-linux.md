---
title: Personalizar clusters do HDInsight ao utilizar as ações de script, Azure
description: Adicione componentes personalizados para clusters do HDInsight baseado em Linux utilizando as ações de script. Ações de script são scripts de Bash que podem ser utilizados para personalizar a configuração de cluster ou adicionar serviços adicionais e utilitários, como Hue, Solr ou R.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 58fca4cfc4217c67be1a4e679d1a95f34ff5726f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108176"
---
# <a name="customize-linux-based-hdinsight-clusters-by-using-script-actions"></a>Personalizar clusters do HDInsight baseado em Linux utilizando as ações de script

O Azure HDInsight fornece um método de configuração chamado **ações de script** que invoca scripts personalizados para personalizar o cluster. Estes scripts são utilizados para instalar componentes adicionais e alterar as definições de configuração. Ações de script podem ser utilizadas durante ou após a criação do cluster.

> [!IMPORTANT]  
> A capacidade de utilizar as ações de script num cluster já esteja sendo executado só está disponível para clusters do HDInsight baseado em Linux.
>
> Linux é o único sistema operativo utilizado no HDInsight versão 3.4 ou posterior. Para obter mais informações, consulte [reforma do Windows HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Ações de script também podem ser publicadas no Azure Marketplace como uma aplicação do HDInsight. Para obter mais informações sobre aplicações do HDInsight, consulte [publicar uma aplicação de HDInsight no Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Permissões

Para um cluster de HDInsight associado a um domínio, existem dois Apache Ambari permissões necessárias ao utilizar as ações de script com o cluster:

* **AMBARI. EXECUTE\_PERSONALIZADA\_COMANDO**. A função de administrador do Ambari tenha esta permissão, por predefinição.
* **CLUSTER.RUN\_CUSTOM\_COMMAND**. O administrador de Cluster do HDInsight e o administrador de Ambari têm esta permissão por predefinição.

Para obter mais informações sobre como trabalhar com permissões com o HDInsight associado a um domínio, consulte [clusters do HDInsight gerir com o Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Controlo de acesso

Se não administrador ou proprietário da sua subscrição do Azure, a conta tem de ter, pelo menos, acesso de Contribuidor ao grupo de recursos que contém o cluster do HDInsight.

Se criar um cluster do HDInsight, alguém com, pelo menos, acesso de contribuinte à subscrição do Azure tem de ter registado previamente o fornecedor para HDInsight. O registo do fornecedor acontece quando um utilizador com o acesso Contribuidor para a subscrição cria um recurso na mesma pela primeira vez. Também pode ser feito sem a criação de um recurso se [registar um fornecedor com REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Obter mais informações sobre como trabalhar com a gestão de acesso:

* [Get started with access management in the Azure portal (Introdução à gestão do acesso no portal do Azure)](../role-based-access-control/overview.md)
* [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Compreender as ações de script

Uma ação de script é o script de Bash que é executado em nós num cluster do HDInsight. Características e funcionalidades de ações de script são os seguintes:

* Devem ser armazenados num URI que é acessível a partir do cluster do HDInsight. Seguem-se as localizações de armazenamento possíveis:

    * Uma conta de armazenamento do Azure Data Lake que seja acessível pelo cluster do HDInsight. Para obter informações sobre como utilizar o armazenamento do Azure Data Lake com o HDInsight, consulte [início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

        O formato URI para scripts armazenados no Data Lake Storage Gen1 é `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]  
        > O principal de serviço que HDInsight utiliza para aceder ao armazenamento do Data Lake tem de ter acesso de leitura para o script.

    * Um blob numa conta de armazenamento do Azure que é a primário ou adicional conta de armazenamento para o cluster do HDInsight. HDInsight é concedido acesso a ambos os tipos de contas de armazenamento durante a criação do cluster.

    * Um serviço de partilha de ficheiros público. Os exemplos são BLOBs do Azure, o GitHub, OneDrive e Dropbox.

        Por exemplo URIs, consulte [scripts de ações de script de exemplo](#example-script-action-scripts).

        > [!WARNING]  
        > HDInsight suporta apenas BLOBs em contas de armazenamento do Azure com um escalão de desempenho padrão. 

* Pode ser restringida para serem executadas em apenas determinados tipos de nó. Os exemplos são nós principais ou nós de trabalho.

* Pode ser persistente ou ad-hoc.

    Os scripts persistentes são utilizados para personalizar novos nós de trabalho adicionados ao cluster através de operações de dimensionamento. Um script persistente também pode aplicar as alterações a outro tipo de nó quando ocorrem operações de dimensionamento. Um exemplo é um nó principal.

  > [!IMPORTANT]  
  > Ações de script persistente tem de ter um nome exclusivo.

    Não são mantidos scripts ad hoc. Eles não são aplicados a nós de trabalho adicionados ao cluster, após a execução do script. Em seguida, pode promover um script ad hoc para um script persistente ou despromover um script persistente para um script ad hoc.

  > [!IMPORTANT]  
  > Ações de script utilizadas durante a criação do cluster automaticamente são mantidas.
  >
  > Scripts que não cumpram não são mantidos, mesmo que indica especificamente que deve ser.

* Pode aceitar parâmetros que são utilizados pelo script durante a execução.

* Execute com privilégios de nível de raiz em nós do cluster.

* Pode ser utilizada através do portal do Azure, Azure PowerShell, a CLI clássica do Azure ou o SDK de .NET do HDInsight.

O cluster mantém um histórico de todos os scripts que tenham sido executados. O histórico de ajuda quando precisar de localizar o ID de um script para operações de promoção ou despromoção.

> [!IMPORTANT]  
> Não existe nenhuma forma automática para anular as alterações feitas por uma ação de script. Manual reverter as alterações ou fornecer um script que inverta-los.

### <a name="script-action-in-the-cluster-creation-process"></a>Ação de script do processo de criação do cluster

Ações de script utilizadas durante a criação do cluster são ligeiramente diferentes de ações de script a executar num cluster existente:

* O script é mantido automaticamente.

* Uma falha no script pode fazer com que o processo de criação de cluster efetuar a ativação.

O diagrama seguinte ilustra quando a ação de script é executado durante o processo de criação:

![Personalização de cluster do HDInsight e fases durante a criação do cluster][img-hdi-cluster-states]

O script é executado enquanto o HDInsight está a ser configurado. O script é executado em paralelo em todos os nós do cluster especificados. Ele é executado com privilégios de raiz em nós.

> [!NOTE]  
> Pode efetuar operações como parar e iniciar serviços, incluindo serviços relacionados com o Apache Hadoop. Se parar os serviços, certifique-se de que o serviço do Ambari e outros serviços relacionados com o Hadoop estão a executar antes de concluir o script. Estes serviços são necessários para determinar com êxito o estado de funcionamento e o estado do cluster enquanto ele está a ser criado.


Durante a criação do cluster, pode usar muitas ações de script ao mesmo tempo. Estes scripts são invocados na ordem em que foram especificados.

> [!IMPORTANT]  
> Ações de script deverá ser concluída em 60 minutos, ou tempo limite. Durante o aprovisionamento do cluster, o script é executado simultaneamente com outros processos de instalação e configuração. Concorrência de recursos, tais como a largura de banda de CPU, rede ou de tempo pode fazer com que o script a demorar mais tempo a concluir do que o no seu ambiente de desenvolvimento.
>
> Para minimizar o tempo que demora a executar o script, evite tarefas, como baixar e compilar aplicativos da origem. Pré-compilar aplicativos e armazenar o binário no armazenamento do Azure.


### <a name="script-action-on-a-running-cluster"></a>Ação de script num cluster em execução

Uma falha num script a executar num cluster já esteja sendo executado automaticamente não causa o cluster alterar para um Estado com falhas. Após a conclusão de um script, deverá devolver o cluster para um Estado de execução.

> [!IMPORTANT]  
> Mesmo que o cluster tem um Estado de execução, o script com falha pode ter dividido coisas. Por exemplo, um script pode eliminar ficheiros necessários para o cluster.
>
> Executam as ações de scripts com privilégios de raiz. Certifique-se de que compreende o que faz um script antes de aplicá-la ao seu cluster.

Quando aplica um script para um cluster, o estado do cluster é alterado de **em execução** ao **aceites**. Em seguida, ele é alterado para **configuração do HDInsight** e, finalmente, voltar ao **em execução** para scripts com êxito. O estado do script é registado no histórico de ação de script. Estas informações indica se o script com êxito ou falhadas. Por exemplo, o `Get-AzureRmHDInsightScriptActionHistory` cmdlet do PowerShell mostra o estado de um script. Devolve informações semelhantes ao seguinte texto:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Se alterar o utilizador do cluster, o administrador, a palavra-passe depois do cluster for criado, as ações de script ser executadas em relação a este cluster poderão falhar. Se tiver quaisquer ações de script persistentes que nós de trabalho de destino, esses scripts podem falhar quando dimensionar o cluster.

## <a name="example-script-action-scripts"></a>Scripts de ações de script de exemplo

Scripts de ações de script podem ser usados por meio dos utilitários a seguir:

* O portal do Azure
* Azure PowerShell
* A CLI clássica do Azure
* Um SDK de .NET do HDInsight

HDInsight fornece scripts para instalar os seguintes componentes em clusters do HDInsight:

| Name | Script |
| --- | --- |
| Adicionar uma conta de Armazenamento do Azure |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Ver [adicionar mais contas de armazenamento ao HDInsight](hdinsight-hadoop-add-storage.md). |
| Instalar o Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Ver [instalar e utilizar Hue no Hadoop do HDInsight clusters](hdinsight-hadoop-hue-linux.md). |
| Instalar prontamente |`https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`. Ver [instalar e utilizar o Presto no HDInsight baseado no Hadoop clusters](hdinsight-hadoop-install-presto.md). |
| Instalar Solr |`https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh`. Ver [instalar e utilizar clusters do Apache Solr no Hadoop do HDInsight](hdinsight-hadoop-solr-install-linux.md). |
| Instalar Giraph |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`. Ver [instalar o Apache Giraph no Hadoop do HDInsight clusters](hdinsight-hadoop-giraph-install-linux.md). |
| Pré-carregar bibliotecas do Hive |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Ver [adicionar bibliotecas de Apache Hive personalizadas, quando criar o cluster do HDInsight](hdinsight-hadoop-add-hive-libraries.md). |
| Instalar ou atualizar o Mono | `https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash`. Ver [instalar ou atualizar o Mono no HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Utilize uma ação de script durante a criação do cluster

Esta secção explica as diferentes formas que pode utilizar ações de script quando criar um cluster do HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Utilize uma ação de script durante a criação do cluster no portal do Azure

1. Começar a criar um cluster, conforme descrito em [configurar clusters no HDInsight com o Apache Hadoop, Apache Spark, Apache Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md). Durante a criação do cluster, vai deparar-se numa __resumo do Cluster__ página. Do __resumo do Cluster__ página, selecione a __editar__ ligar para __definições avançadas__.

    ![Ligação de definições avançadas](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Partir do __definições avançadas__ secção, selecione __ações de Script__. Partir do __ações de Script__ secção, selecione __+ submeter novo__.

    ![Submeter uma ação de script nova](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Utilize o __selecionar um script__ entrada para selecionar um script pré-criados. Para utilizar um script personalizado, selecione __personalizado__. Em seguida, forneça o __Name__ e __URI do script de Bash__ para o seu script.

    ![Adicionar um script sob a forma de script selecione](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A tabela seguinte descreve os elementos no formulário:

    | Propriedade | Value |
    | --- | --- |
    | Selecionar um script | Para utilizar o seu próprio script, selecione __personalizado__. Caso contrário, selecione um dos scripts fornecidos. |
    | Name |Especifique um nome para a ação de script. |
    | URI do script de bash |Especifique o URI do script. |
    | Head/Worker/Zookeeper |Especifique os nós em que o script é executado: **HEAD**, **Worker**, ou **ZooKeeper**. |
    | Parâmetros |Especifique os parâmetros, se necessário, o script. |

    Utilize o __manter esta ação de script__ entrada para se certificar de que o script é aplicado durante operações de dimensionamento.

5. Selecione __criar__ para guardar o script. Pode utilizar __+ submeter novo__ para adicionar outro script.

    ![Várias ações de script](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Quando tiver terminado a adição de scripts, selecione o __selecionar__ botão e, em seguida, o __seguinte__ botão para retornar para o __resumo do Cluster__ secção.

3. Para criar o cluster, selecione __Create__ partir do __resumo do Cluster__ seleção.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Utilize uma ação de script a partir de modelos do Azure Resource Manager

Ações de script podem ser utilizadas com modelos Azure Resource Manager. Por exemplo, veja [criar clusters do HDInsight Linux e execute uma ação de script](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

Neste exemplo, a ação de script é adicionada ao utilizar o seguinte código:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Obter mais informações sobre como implementar um modelo:

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Utilize uma ação de script durante a criação do cluster do Azure PowerShell

Nesta secção, vai utilizar o [Add-AzureRmHDInsightScriptAction](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightscriptaction) cmdlet para invocar scripts para personalizar um cluster. Antes de começar, certifique-se de instalar e configurar o Azure PowerShell. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [descrição geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0#run-or-install).

O script seguinte mostra como aplicar uma ação de script, quando criar um cluster com o PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Pode demorar vários minutos antes do cluster ser criado.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Utilize uma ação de script durante a criação do cluster do HDInsight .NET SDK

O SDK de .NET do HDInsight fornece bibliotecas de cliente que o tornam mais fácil trabalhar com o HDInsight a partir de uma aplicação .NET. Para obter um exemplo de código, consulte [baseado em Linux criar clusters no HDInsight com o SDK de .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Aplicar uma ação de script para um cluster em execução

Esta secção explica como aplicar ações de script para um cluster em execução.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Aplicar uma ação de script para um cluster em execução a partir do portal do Azure

Vá para o [portal do Azure](https://portal.azure.com):

1. No menu à esquerda, selecione **todos os serviços**.

1. Sob **ANALYTICS**, selecione **clusters do HDInsight**.

1. Selecione o cluster na lista, que abre a vista predefinida.

1. A vista predefinida, em **configurações**, selecione **ações de Script**.

1. Na parte superior do **ações de Script** página, selecione **+ submeter novo**.

    ![Adicionar um script para um cluster em execução](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Utilize o __selecionar um script__ entrada para selecionar um script pré-criados. Para utilizar um script personalizado, selecione __personalizado__. Em seguida, forneça o __Name__ e __URI do script de Bash__ para o seu script.

    ![Adicionar um script sob a forma de script selecione](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A tabela seguinte descreve os elementos no formulário:

    | Propriedade | Value |
    | --- | --- |
    | Selecionar um script | Para utilizar o seu próprio script, selecione __personalizado__. Caso contrário, selecione um script fornecido. |
    | Name |Especifique um nome para a ação de script. |
    | URI do script de bash |Especifique o URI do script. |
    | Head/Worker/Zookeeper |Especifique os nós em que o script é executado: **HEAD**, **Worker**, ou **ZooKeeper**. |
    | Parâmetros |Especifique os parâmetros, se necessário, o script. |

    Utilize o __manter esta ação de script__ entrada para se certificar de que o script é aplicado durante operações de dimensionamento.

5. Por fim, selecione o **criar** botão para aplicar o script para o cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Aplicar uma ação de script para um cluster em execução a partir do Azure PowerShell

Antes de começar, certifique-se de instalar e configurar o Azure PowerShell. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [descrição geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0#run-or-install).

O exemplo seguinte mostra como aplicar uma ação de script para um cluster em execução:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Depois de concluída a operação, receberá informações semelhantes ao seguinte texto:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Aplicar uma ação de script para um cluster em execução a partir da CLI do Azure

Antes de começar, certifique-se de instalar e configurar a CLI do Azure. Para obter mais informações, consulte [instalar a CLI do Azure clássica](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Mude para o modo Azure Resource Manager:

    ```bash
    azure config mode arm
    ```

2. Autenticar a sua subscrição do Azure:

    ```bash
    azure login
    ```

3. Aplica uma ação de script a um cluster em execução:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Se omitir os parâmetros para este comando, lhe for pedido para os mesmos. Se o script especificar com `-u` aceita parâmetros, pode especificá-las usando o `-p` parâmetro.

    São tipos de nó válido `headnode`, `workernode`, e `zookeeper`. Se o script deve ser aplicado a vários tipos de nó, especificar os tipos separados por ponto e vírgula `;`. Por exemplo, `-n headnode;workernode`.

    Para manter o script, adicione `--persistOnSuccess`. Pode também manter o script mais tarde, utilizando `azure hdinsight script-action persisted set`.

    Depois da tarefa é concluída, obtém o resultado como o seguinte texto:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Aplicar uma ação de script para um cluster em execução com REST API

Ver [Cluster REST API no Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Aplicar uma ação de script para um cluster em execução a partir do SDK de .NET do HDInsight

Para obter um exemplo com o SDK .NET para aplicar os scripts a um cluster, consulte [aplicam-se uma ação de Script num cluster do HDInsight está em execução baseado em Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Ver o histórico e promover e despromover ações de script

### <a name="the-azure-portal"></a>O portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu à esquerda, selecione **todos os serviços**.

1. Sob **ANALYTICS**, selecione **clusters do HDInsight**.

1. Selecione o cluster na lista, que abre a vista predefinida.

1. A vista predefinida, em **configurações**, selecione **ações de Script**.

4. Apresenta um histórico de scripts para este cluster na secção de ações de script. Estas informações incluem uma lista de scripts persistentes. Captura de ecrã seguinte mostra que o script de Solr foi executado neste cluster. A captura de ecrã não mostra todos os scripts persistentes.

    ![Ações do script](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Selecione um script do histórico para apresentar os **propriedades** secção deste script. Na parte superior do ecrã, pode voltar a executar o script ou promovê-lo.

    ![Ações de script, propriedades](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Também pode selecionar as reticências, **...** , à direita de entradas na seção de ações de script para efetuar ações.

    ![Ações de script, o botão de reticências](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Função |
| --- | --- |
| `Get-AzureRmHDInsightPersistedScriptAction` |Obter informações sobre ações de script persistentes. |
| `Get-AzureRmHDInsightScriptActionHistory` |Obter um histórico das ações de script aplicados ao cluster ou de detalhes de um script específico. |
| `Set-AzureRmHDInsightPersistedScriptAction` |Promova uma ação de script ad hoc para uma ação de script persistentes. |
| `Remove-AzureRmHDInsightPersistedScriptAction` |Despromova uma ação de script persistentes a uma ação ad hoc. |

> [!IMPORTANT]  
> `Remove-AzureRmHDInsightPersistedScriptAction` não anular as ações executadas por um script. Este cmdlet Remove apenas o sinalizador persistente.

O script de exemplo seguinte demonstra como utilizar os cmdlets para promover e, em seguida, despromover um script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>A CLI clássica do Azure

| cmdlet | Função |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Obter uma lista de ações de script persistentes. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Obter informações sobre uma ação de script persistentes específico. |
| `azure hdinsight script-action history list <clustername>` |Obter um histórico das ações de script aplicadas ao cluster. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Obter informações sobre uma ação de script específico. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Promova uma ação de script ad hoc para uma ação de script persistentes. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Despromova uma ação de script persistentes a uma ação ad hoc. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` não anular as ações executadas por um script. Este cmdlet Remove apenas o sinalizador persistente.

### <a name="the-hdinsight-net-sdk"></a>O HDInsight .NET SDK

Para obter um exemplo com o SDK .NET para recuperar o histórico de script a partir de um cluster, promover ou despromover scripts, consulte [ aplicam-se uma ação de Script num cluster do HDInsight está em execução baseado em Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Este exemplo também demonstra como instalar uma aplicação do HDInsight com o SDK de .NET.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para o software de código aberto utilizada nos clusters do HDInsight

O serviço do Microsoft Azure HDInsight utiliza um ecossistema de tecnologias de open source formado em torno do Apache Hadoop. Microsoft Azure fornece um nível geral de suporte para tecnologias de código aberto. Para obter mais informações, consulte a **âmbito de suporte** secção [FAQ do suporte do Azure](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte para componentes internos.

Dois tipos de componentes de código aberto estão disponíveis no serviço HDInsight:

* **Componentes internos**. Esses componentes são pré-instalado em clusters do HDInsight e fornecem a funcionalidade principal do cluster. Os seguintes componentes pertencem a essa categoria:

  * [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager.
  * A linguagem de consulta do Hive [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout](https://mahout.apache.org/). 
    
    Uma lista completa dos componentes de cluster está disponível no [quais são os componentes do Apache Hadoop e versões disponíveis com o HDInsight?](hdinsight-component-versioning.md)

* **Componentes personalizados**. Como um utilizador do cluster, pode instalar ou utilizar na sua carga de trabalho a qualquer componente disponível na Comunidade ou criado por si.

> [!WARNING]  
> Componentes fornecidos com o cluster do HDInsight são totalmente suportadas. Support da Microsoft ajuda a isolar e resolver problemas relacionados com esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para o ajudar a resolver o problema. Microsoft Support poderá conseguir resolver o problema. Ou eles podem pedir-lhe para interagir com os canais disponíveis para as tecnologias de código-fonte aberto, onde os conhecimentos aprofundados para essa tecnologia é encontrado. Muitos sites de Comunidades podem ser utilizados. Os exemplos são [fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) e [Stack Overflow](https://stackoverflow.com). 
>
> Projetos Apache também tem sites de projeto no [Web site do Apache](https://apache.org). Um exemplo é [Hadoop](https://hadoop.apache.org/).

O serviço HDInsight fornece várias formas de utilizar componentes personalizados. O mesmo nível de suporte aplica-se, não importa como um componente utilizado ou instalado no cluster. A lista seguinte descreve a mais comum formas que componentes personalizados sejam usados em clusters do HDInsight:

1. **Submissão da tarefa**. Hadoop ou outros tipos de tarefas que executarem ou usam componentes personalizados podem ser submetidos para o cluster.

2. **Personalização do cluster**. Durante a criação do cluster, pode especificar definições adicionais e componentes personalizados que são instalados em nós do cluster.

3. **Exemplos de**. Para populares componentes personalizados, Microsoft e outros fornecer exemplos de como esses componentes podem ser usados em clusters do HDInsight. Estes exemplos são fornecidos sem suporte.

## <a name="troubleshooting"></a>Resolução de problemas

Pode utilizar a IU web do Ambari para ver informações registadas pelo ações de script. Se o script falhar durante a criação do cluster, os registos também estão disponíveis na conta de armazenamento predefinida associada ao cluster. Esta seção fornece informações sobre como obter os registos ao utilizar ambas estas opções.

### <a name="the-apache-ambari-web-ui"></a>A IU web do Apache Ambari

1. No seu browser, aceda a https://CLUSTERNAME.azurehdinsight.net. Substitua **CLUSTERNAME** pelo nome do cluster do HDInsight.

    Quando lhe for pedido, introduza o nome de conta de administrador **administrador**e a palavra-passe para o cluster. Poderá ter de reintroduzir as credenciais de administrador num formulário da web.

2. Na barra de na parte superior da página, selecione o **ops** entrada. Uma lista apresenta operações atuais e anteriores, feitas no cluster através do Ambari.

    ![Barra de interface do Usuário de web de Ambari com ops selecionado](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Encontre as entradas que tenham **execute\_customscriptaction** no **operações** coluna. Estas entradas são criadas quando executam as ações de script.

    ![Captura de ecrã das operações](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Para ver os **STDOUT** e **STDERR** saída, selecione o **run\customscriptaction** e desagregação usando os links de entrada. Esta saída é gerada quando o script é executado e pode conter informações úteis.

### <a name="access-logs-from-the-default-storage-account"></a>Registos de acesso da conta de armazenamento predefinida

Se a criação do cluster falhar devido a um erro de script, os registos são mantidos na conta de armazenamento de cluster.

* Os registos de armazenamento estão disponíveis em `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Captura de ecrã das operações](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    Neste diretório, os registos são organizados em separado para **nó principal**, **nó de trabalho**, e **nó do zookeeper**. Veja os exemplos seguintes:

    * **Nó principal**: `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Nó de trabalho**: `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Nó do zookeeper**: `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Todos os **stdout** e **stderr** do anfitrião correspondente é carregado para a conta de armazenamento. Há um **saída -\*. txt** e **erros -\*. txt** para cada ação de script. O **saída-*. txt** arquivo contém informações sobre o URI do script que foi executado no anfitrião. O texto seguinte é um exemplo dessas informações:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* É possível que repetidamente criar um cluster de ação de script com o mesmo nome. Nesse caso, pode distinguir os registos relevantes com base na **data** nome da pasta. Por exemplo, a estrutura de pastas para um cluster, **mycluster**criados em datas diferentes parece ser semelhante para as seguintes entradas de registo:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se criar um cluster de ação de script com o mesmo nome no mesmo dia, pode utilizar o prefixo exclusivo para identificar os ficheiros de registo relevantes.

* Se criar um cluster perto 12:00 AM, meia-noite, é possível que os ficheiros de registo que abrangem dois dias. Nesse caso, verá duas pastas de data diferentes para o mesmo cluster.

* Carregar ficheiros de registo para o contentor predefinido, pode demorar até cinco minutos, especialmente para grandes clusters. Portanto, se pretender aceder aos registos, não deve eliminar imediatamente o cluster se uma ação de script falhar.

### <a name="ambari-watchdog"></a>Watchdog de Ambari

> [!WARNING]  
> Não altere a palavra-passe para o watchdog de Ambari, hdinsightwatchdog, no seu cluster do HDInsight baseado em Linux. Alterar a palavra-passe para esta conta quebra a capacidade de executar novas ações de script no cluster do HDInsight.

### <a name="cant-import-name-blobservice"></a>Não é possível importar o nome do BlobService

__Os sintomas__. A ação de script falhar. Texto semelhante ao seguinte erro apresentada quando vê a operação num Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Causa__. Este erro ocorre se atualizar o cliente de armazenamento do Azure de Python que está incluído com o cluster do HDInsight. HDInsight espera que o cliente de armazenamento do Azure 0.20.0.

__Resolução__. Para resolver este erro, se conectar manualmente para cada nó de cluster utilizando `ssh`. Execute o seguinte comando para reinstalar a versão de cliente de armazenamento correto:

```bash
sudo pip install azure-storage==0.20.0
```

Para obter informações sobre como ligar ao cluster com SSH, veja [ligar ao HDInsight (Apache Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Histórico não mostra os scripts utilizados durante a criação do cluster

Se o seu cluster tiver sido criado antes de 15 de Março de 2016, não poderá ver uma entrada no histórico de ações de script. Redimensionar o cluster faz com que os scripts sejam apresentados no histórico de ações de script.

Existem duas exceções:

* O cluster tiver sido criado antes de 1 de Setembro de 2015. Esta data é quando as ações de script foram introduzidas. Qualquer cluster criado antes desta data não foi possível ter usado ações de script para a criação do cluster.

* Várias ações de script que utilizou durante a criação do cluster. Ou tiver utilizado o mesmo nome para vários scripts ou o mesmo nome, mesmo URI, mas diferentes parâmetros para vários scripts. Nestes casos, obtém o erro seguinte:

    Não existem novas ações de script podem ser executadas neste cluster devido a nomes de script em conflito em scripts existentes. Os nomes de script fornecidos durante a criação do cluster têm de ser exclusivos. Scripts existentes são executados no redimensionamento.

## <a name="next-steps"></a>Passos Seguintes

* [Desenvolver scripts de ações de script para o HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalar e utilizar o Apache Solr nos clusters do HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Instalar e usar o Apache Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Adicionar armazenamento adicional a um cluster do HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fases durante a criação do cluster"
