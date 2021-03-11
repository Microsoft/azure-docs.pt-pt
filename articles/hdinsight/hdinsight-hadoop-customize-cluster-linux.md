---
title: Personalize os clusters Azure HDInsight utilizando ações de script
description: Adicione componentes personalizados aos clusters HDInsight utilizando ações de script. As ações do script são scripts Bash que podem ser usados para personalizar a configuração do cluster. Ou adicione serviços e utilidades adicionais como Hue, Solr ou R.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, contperf-fy21q2
ms.date: 03/09/2021
ms.openlocfilehash: 00ed8c26bbafeb94b1481e6157a242dad7ed84c6
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610268"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Personalize os clusters Azure HDInsight utilizando ações de script

Azure HDInsight fornece um método de configuração chamado **ações de script** que invocam scripts personalizados para personalizar o cluster. Estes scripts são utilizados para instalar componentes adicionais e alterar definições de configuração. As ações do script podem ser usadas durante ou após a criação do cluster.

As ações de script também podem ser publicadas no Azure Marketplace como uma aplicação HDInsight. Para obter mais informações sobre aplicações HDInsight, consulte [publicar uma aplicação HDInsight no Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="understand-script-actions"></a>Compreender as ações do script

Uma ação de script é o script Bash que corre nos nós num cluster HDInsight. As características e características das ações do script são as seguintes:

- Deve ser armazenado num URI acessível a partir do cluster HDInsight. Seguem-se possíveis locais de armazenamento:

    - Para agrupamentos regulares (não-ESP):
      - Data Lake Storage Gen1/Gen2: O principal serviço hdInsight usa para aceder ao Armazenamento do Lago de Dados deve ter lido o acesso ao script. O formato URI para scripts armazenados na Data Lake Storage Gen1 é `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` . 
      - Uma bolha numa conta de Armazenamento Azure que é a conta de armazenamento primário ou adicional para o cluster HDInsight. O HDInsight tem acesso a ambos estes tipos de contas de armazenamento durante a criação de clusters.

        > [!IMPORTANT]  
        > Não rode a chave de armazenamento nesta conta de Armazenamento Azure, pois irá causar a falha das ações de scripts subsequentes com scripts aí armazenados.

      - Um serviço público de partilha de ficheiros acessível através de `http://` caminhos. Exemplos são Azure Blob, GitHub ou OneDrive. Por exemplo, URIs, consulte [scripts de ação de scripts de exemplo](#example-script-action-scripts).
    - Para agrupamentos com ESP, os `wasb://` `wasbs://` ou `http[s]://` URIs ou URIs são suportados.

- Pode ser restringido a funcionar apenas em certos tipos de nós. Exemplos são nós de cabeça ou nós de trabalhadores.
- Pode ser persistir ou *ad hoc*.

    - As ações de scripts persistires devem ter um nome único. Scripts persistidos são usados para personalizar novos nós de trabalhador adicionados ao cluster através de operações de escala. Um script persistido também pode aplicar alterações a outro tipo de nó quando ocorrem operações de escala. Um exemplo é um nó na cabeça.
    - Os guiões *ad hoc* não persistem. As ações de script utilizadas durante a criação do cluster são automaticamente persistidas. Não são aplicados aos nós dos trabalhadores adicionados ao cluster depois do guião ter sido executado. Em seguida, você pode promover um script *ad hoc* para um script persistido ou despromu-lo um script persistido para um script *ad hoc.* Os scripts que falham não persistem, mesmo que indiques especificamente que deveriam ser.

- Pode aceitar parâmetros que são usados pelo script durante a execução.
- Corra com privilégios de nível de raiz nos nós do cluster.
- Pode ser utilizado através do portal Azure PowerShell, Azure CLI ou HDInsight .NET SDK.
- As ações de script que removem ou modificam ficheiros de serviço no VM podem ter impacto na saúde e disponibilidade do serviço.

O cluster mantém uma história de todos os scripts que foram executados. A história ajuda quando precisa de encontrar a identificação de um script para operações de promoção ou despromoção.

> [!IMPORTANT]  
> Não há forma automática de desfazer as alterações feitas por uma ação de guião. Ou inverte manualmente as alterações ou fornece um script que as reverte.

## <a name="permissions"></a>Permissões

Para um cluster HDInsight ligado a domínio, existem duas permissões Apache Ambari que são necessárias quando utiliza ações de script com o cluster:

* **O AMBARI. EXECUTAR \_ \_ COMANDO PERSONALIZADO**. A função administradora de Ambari tem esta permissão por defeito.
* **CLUSTER. EXECUTAR \_ \_ COMANDO PERSONALIZADO**. Tanto o Administrador de Cluster HDInsight como o Administrador Ambari têm esta permissão por padrão.

Para obter mais informações sobre o trabalho com permissões com HDInsight ligado ao domínio, consulte [Gerir clusters HDInsight com Pacote de Segurança Empresarial.](./domain-joined/apache-domain-joined-manage.md)

## <a name="access-control"></a>Controlo de acesso

Se não for o administrador ou proprietário da sua subscrição Azure, a sua conta deve ter pelo menos `Contributor` acesso ao grupo de recursos que contém o cluster HDInsight.

Alguém com pelo menos acesso ao colaborador à assinatura Azure deve ter registado previamente o fornecedor. O registo do fornecedor acontece quando um utilizador com o Acesso do Contribuinte à subscrição cria um recurso. Para, sem criar um recurso, consulte [registar um fornecedor utilizando REST](/rest/api/resources/providers#Providers_Register).

Obtenha mais informações sobre o trabalho com a gestão de acessos:

- [Get started with access management in the Azure portal (Introdução à gestão do acesso no portal do Azure)](../role-based-access-control/overview.md)
- [Atribua funções Azure para gerir o acesso aos seus recursos de subscrição Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="methods-for-using-script-actions"></a>Métodos para usar ações de script

Você tem a opção de configurar uma ação de script para executar quando o cluster é criado pela primeira vez, ou executá-lo em um cluster existente.

### <a name="script-action-in-the-cluster-creation-process"></a>Ação de script no processo de criação de cluster

As ações de script utilizadas durante a criação de clusters são ligeiramente diferentes das ações de script executadas num cluster existente:

- O script é automaticamente persistido.
- Uma falha no script pode fazer com que o processo de criação do cluster falhe.

O diagrama que se segue ilustra quando a ação do guião decorre durante o processo de criação:

![HdInsight personalização do cluster e etapas durante a criação de cluster][img-hdi-cluster-states]

O script é executado enquanto o HDInsight está a ser configurado. O script funciona em paralelo em todos os nós especificados no cluster. Funciona com privilégios de raiz nos nós.

Você pode fazer operações como parar e iniciar serviços, incluindo serviços relacionados com Apache Hadoop. Se parar os serviços, certifique-se de que Ambari e outros serviços relacionados com Hadoop estão a ser prestados antes do final do script. Estes serviços necessários determinam a saúde e o estado do aglomerado enquanto está a ser criado.

Durante a criação de clusters, você pode usar muitas ações de script de uma só vez. Estes scripts são invocados na ordem em que foram especificados.

> [!IMPORTANT]  
> As ações do script devem terminar dentro de 60 minutos, ou estão fora. Durante o fornecimento de clusters, o script é executado simultaneamente com outros processos de configuração e configuração. A competição por recursos como o tempo de CPU ou largura de banda de rede pode fazer com que o script despuça mais tempo do que no seu ambiente de desenvolvimento.
>
> Para minimizar o tempo que leva para executar o script, evite tarefas como descarregar e compilar aplicações a partir da fonte. Aplicações pré-comprelha e armazenar o binário no Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Ação do script em um cluster de execução

Uma falha de script num cluster já em execução não faz com que o cluster mude automaticamente para um estado falhado. Depois de um guião terminar, o cluster deve voltar a um estado de execução. Mesmo que o aglomerado tenha um estado de funcionamento, o guião falhado pode ter partido coisas. Por exemplo, um script pode eliminar ficheiros necessários pelo cluster.

As ações dos scripts são executadas com privilégios de raiz. Certifique-se de que entende o que um script faz antes de aplicá-lo ao seu cluster.

Quando se aplica um script a um cluster, o estado de cluster muda de **Running** to **Accepted**. Em seguida, muda para a **configuração HDInsight** e, finalmente, de volta a **Running** para scripts bem sucedidos. O estado do script está registado no histórico de ação do script. Esta informação diz-lhe se o script foi bem sucedido ou falhou. Por exemplo, o `Get-AzHDInsightScriptActionHistory` cmdlet PowerShell mostra o estado de um script. Devolve informações semelhantes ao seguinte texto:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> Se alterar o utilizador do cluster, administração, palavra-passe após a criação do cluster, as ações de script executadas contra este cluster podem falhar. Se tiver alguma ação de scripts que se aponte para os nós dos trabalhadores, estes scripts podem falhar quando escalar o cluster.

## <a name="example-script-action-scripts"></a>Scripts de ação de script de exemplo

Scripts de ação de scripts podem ser usados através dos seguintes utilitários:

* Portal do Azure
* Azure PowerShell
* CLI do Azure
* HDInsight .NET SDK

O HDInsight fornece scripts para instalar os seguintes componentes em clusters HDInsight:

| Name | Script |
| --- | --- |
| Adicione uma conta de Armazenamento Azure |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Consulte [adicionar contas de armazenamento adicionais ao HDInsight](hdinsight-hadoop-add-storage.md). |
| Instalar Matiz |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Consulte [instalar e utilizar o Hue nos clusters hdInsight Hadoop](hdinsight-hadoop-hue-linux.md). |
| Bibliotecas pré-carga de Colmeia |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Consulte [as bibliotecas personalizadas da Crisição Apache ao criar o seu cluster HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Ação de script durante a criação de cluster

Esta secção explica as diferentes formas de utilizar as ações de script quando cria um cluster HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Use uma ação de script durante a criação do cluster a partir do portal Azure

1. Comece a criar um cluster como descrito em [criar clusters baseados em Linux em HDInsight utilizando o portal Azure](hdinsight-hadoop-create-linux-clusters-portal.md). A partir do **separador Configuração + preços,** selecione **+ Adicione ação de script**.

    ![Ação de script de cluster de portal Azure](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Utilize __a__ entrada do script Select para selecionar um script pré-feito. Para utilizar um script personalizado, selecione __Custom__. Em __seguida,__ forneça o script __URI do nome__ e da bash para o seu script.

    ![Adicione um script no formulário de script selecionado](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    A tabela a seguir descreve os elementos do formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecione um script | Para utilizar o seu próprio script, selecione __Custom__. Caso contrário, selecione um dos scripts fornecidos. |
    | Name |Especifique um nome para a ação do script. |
    | URI de guião de bash |Especifique o URI do script. |
    | Cabeça/Trabalhador/ZooKeeper |Especificar os nós em que o script é executado: **Head,** **Worker**, ou **ZooKeeper**. |
    | Parâmetros |Especifique os parâmetros, se necessário pelo script. |

    Utilize a entrada __de ação do script Persist para__ se certificar de que o script é aplicado durante as operações de escala.

1. Selecione __Criar__ para guardar o script. Em seguida, pode usar __+ Submeter novo__ para adicionar outro script.

    ![HDInsight várias ações de script](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Quando terminar de adicionar scripts, volte ao **separador Configuração + preços.**

1. Complete os passos restantes de criação de cluster como de costume.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Use uma ação de script a partir de modelos de Gestor de Recursos Azure

As ações do Script podem ser usadas com modelos de Gestor de Recursos Azure. Por exemplo, consulte [Create HDInsight Linux Cluster e executar uma ação de script](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

Neste exemplo, a ação do script é adicionada utilizando o seguinte código:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Obtenha mais informações sobre como implementar um modelo:

- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Implementar recursos com modelos de Gestor de Recursos e o CLI Azure](../azure-resource-manager/templates/deploy-cli.md)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Use uma ação de script durante a criação de cluster a partir de Azure PowerShell

Nesta secção, utiliza o [cmdlet Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) para invocar scripts para personalizar um cluster. Antes de começar, certifique-se de instalar e configurar a Azure PowerShell. Para utilizar estes comandos PowerShell, precisa do [Módulo AZ](/powershell/azure/).

O seguinte script mostra como aplicar uma ação de script quando cria um cluster usando PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Pode levar vários minutos até que o cluster seja criado.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Utilize uma ação de script durante a criação do cluster a partir do HDInsight .NET SDK

O HDInsight .NET SDK fornece bibliotecas de clientes que facilitam o trabalho com o HDInsight a partir de uma aplicação .NET. Para obter uma amostra de código, consulte [As ações do script](/dotnet/api/overview/azure/hdinsight#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Ação do script para um cluster de execução

Esta secção explica como aplicar ações de script num cluster de execução.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Aplique uma ação de script a um cluster de execução a partir do portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) e localize o seu cluster.

1. A partir da vista padrão, em **Definições**, selecione **ações de Script**.

1. A partir do topo da página de ações do **Script,** selecione **+ Submeta novo**.

    ![Adicione um script a um cluster de execução](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Utilize __a__ entrada do script Select para selecionar um script pré-feito. Para utilizar um script personalizado, selecione __Custom__. Em __seguida,__ forneça o script __URI do nome__ e da bash para o seu script.

    ![Adicione um script no formulário de script selecionado](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    A tabela a seguir descreve os elementos do formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecione um script | Para utilizar o seu próprio script, selecione __personalizado.__ Caso contrário, selecione um script fornecido. |
    | Name |Especifique um nome para a ação do script. |
    | URI de guião de bash |Especifique o URI do script. |
    | Cabeça/Trabalhador/Zookeeper |Especificar os nós em que o script é executado: **Head,** **Worker**, ou **ZooKeeper**. |
    | Parâmetros |Especifique os parâmetros, se necessário pelo script. |

    Utilize a entrada __de ação do script Persist para__ se certificar de que o script é aplicado durante as operações de escala.

1. Por fim, selecione o botão **Criar** para aplicar o script no cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Aplique uma ação de script a um cluster de execução da Azure PowerShell

Para utilizar estes comandos PowerShell, precisa do [Módulo AZ](/powershell/azure/). O exemplo a seguir mostra como aplicar uma ação de script a um cluster de execução:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Após o fim da operação, recebe informações semelhantes ao seguinte texto:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Aplique uma ação de script a um cluster de execução do CLI Azure

Antes de começar, certifique-se de instalar e configurar o Azure CLI. Certifique-se de que tem a versão mais recente. Para mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

1. Autenticar para a sua subscrição Azure:

    ```azurecli
    az login
    ```

1. Aplique uma ação de script num cluster de execução:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Papéis válidos `headnode` `workernode` são, `zookeepernode` , . . `edgenode` . Se o script for aplicado a vários tipos de nós, separe as funções por um espaço. Por exemplo, `--roles headnode workernode`.

    Para persistir o script, adicione `--persist-on-success` . Também pode persistir o script mais tarde utilizando `az hdinsight script-action promote` .

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Aplicar uma ação de script a um cluster de execução usando a API REST

Consulte [cluster REST API em Azure HDInsight](/rest/api/hdinsight/hdinsight-cluster).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Aplique uma ação de script a um cluster de execução a partir do HDInsight .NET SDK

Para um exemplo de utilização do .NET SDK para aplicar scripts num cluster, consulte [Aplicar uma Ação de Script contra um cluster HDInsight baseado em Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Ver história e promover e despromu-la

### <a name="the-azure-portal"></a>O portal do Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) e localize o seu cluster.

1. A partir da vista padrão, em **Definições**, selecione **ações de Script**.

1. Uma história de scripts para este cluster apresenta na secção de ações do script. Esta informação inclui uma lista de scripts persistidos. A imagem que se segue mostra que o script Solr foi executado neste cluster. A imagem não mostra nenhum script persistido.

    ![As ações de script do portal submetem a história](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Selecione um script do histórico para exibir a secção **Propriedades** para este script. A partir do topo do ecrã, pode repetir o script ou promovê-lo.

    ![Propriedades de ações de script promovem](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Também pode selecionar a elipse, **... ,** à direita das entradas na secção de ações do script para fazer ações.

    ![As ações de scripts persistidas eliminam](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Função |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Recupere informações sobre as ações de scripts persistentes. Este cmdlet não desfaz as ações feitas por um guião, apenas remove a bandeira persistiu.|
| `Get-AzHDInsightScriptActionHistory` |Recupere um histórico de ações de script aplicadas ao cluster ou detalhes para um script específico. |
| `Set-AzHDInsightPersistedScriptAction` |Promover uma `ad hoc` ação de script para uma ação de scripts persistente. |
| `Remove-AzHDInsightPersistedScriptAction` |Desmote uma ação de guião insistida para uma `ad hoc` ação. |

O seguinte exemplo de script demonstra usar os cmdlets para promover e, em seguida, despromu-lo um script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>CLI do Azure

| Comando | Descrição |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-delete) |Elimina uma ação de script persistiu especificada do cluster. Este comando não desfaz as ações feitas por um guião, apenas remove a bandeira persistiu.|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-execute)|Execute as ações de script no cluster HDInsight especificado.|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list) |Lista todas as ações de scripts persistidos para o cluster especificado. |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list-execution-history)|Lista o histórico de execução de todos os scripts para o cluster especificado.|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-promote)|Promove a execução de scripts ad hoc especificados para um script persistido.|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-show-execution-details)|Obtém o detalhe da execução do guião para a identificação de execução do guião.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Para um exemplo de utilização do .NET SDK para recuperar o histórico de scripts de um cluster, promover ou despromutar scripts, consulte [Aplicar uma Ação de Script contra um cluster HDInsight baseado em Linux.](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)

> [!NOTE]  
> Este exemplo também demonstra como instalar uma aplicação HDInsight utilizando o .NET SDK.

## <a name="next-steps"></a>Passos seguintes

* [Desenvolver scripts de ação de script para HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Adicione armazenamento adicional a um cluster HDInsight](hdinsight-hadoop-add-storage.md)
* [Resolução de problemas de ações de script](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Estágios durante a criação de clusters"
