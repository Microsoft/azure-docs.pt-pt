---
title: Personalize os clusters Azure HDInsight utilizando ações de script
description: Adicione componentes personalizados aos clusters HDInsight utilizando ações de script. As ações do script são scripts Bash que podem ser usados para personalizar a configuração do cluster ou adicionar serviços e serviços adicionais como Hue, Solr ou R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 12e6892930afe8ba9c7bad9b05fd39eeaf8835fc
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206729"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Personalize os clusters Azure HDInsight utilizando ações de script

O Azure HDInsight fornece um método de configuração chamado ações de **script** que invocam scripts personalizados para personalizar o cluster. Estes scripts são utilizados para instalar componentes adicionais e alterar as definições de configuração. As ações do script podem ser usadas durante ou após a criação do cluster.

As ações do script também podem ser publicadas no Azure Marketplace como uma aplicação HDInsight. Para obter mais informações sobre as aplicações HDInsight, consulte [Publicar uma aplicação HDInsight no Mercado Azure](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Permissões

Para um cluster HDInsight associado ao domínio, existem duas permissões Apache Ambari que são necessárias quando você usa ações de script com o cluster:

* **AMBARI. EXECUTAR\_COMANDO DE\_PERSONALIZADO**. A função de Administrador Ambari tem esta permissão por defeito.
* **AGLOMERADO. EXECUTAR\_COMANDO DE\_PERSONALIZADO**. Tanto o Administrador de Cluster HDInsight como o Administrador Ambari têm esta permissão por defeito.

Para obter mais informações sobre o trabalho com permissões com hDInsight unida por domínios, consulte [Gerir os clusters HDInsight com](./domain-joined/apache-domain-joined-manage.md)o Pacote de Segurança Empresarial .

## <a name="access-control"></a>Controlo de acesso

Se não for o administrador ou proprietário da sua subscrição Azure, a sua conta deve ter pelo menos acesso ao grupo de recursos que contém o cluster HDInsight.

Se criar um cluster HDInsight, alguém com pelo menos acesso colaborador à subscrição do Azure deve ter registado previamente o fornecedor para o HDInsight. O registo do fornecedor acontece quando um utilizador com o acesso Contribuidor para a subscrição cria um recurso na mesma pela primeira vez. Também pode ser feito sem criar um recurso se [registar um fornecedor utilizando o REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Obtenha mais informações sobre o trabalho com a gestão de acesso:

* [Get started with access management in the Azure portal (Introdução à gestão do acesso no portal do Azure)](../role-based-access-control/overview.md)
* [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Compreender as ações do guião

Uma ação de script é o script Bash que corre nos nós em um cluster HDInsight. Características e características das ações do guião são as seguintes:

* Deve ser armazenado num URI acessível a partir do cluster HDInsight. Seguem-se possíveis locais de armazenamento:

    * Para agrupamentos regulares:

      * ADLS Gen1: O diretor de serviço HDInsight usa para aceder ao Data Lake Storage deve ter lido o acesso ao script. O formato URI para scripts armazenados em Data Lake Storage Gen1 é `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

      * Uma bolha numa conta de Armazenamento Azure que é a conta de armazenamento primária ou adicional para o cluster HDInsight. O HDInsight tem acesso a ambos estes tipos de contas de armazenamento durante a criação do cluster.

        > [!IMPORTANT]  
        > Não rode a chave de armazenamento nesta conta de Armazenamento Azure, uma vez que irá fazer com que as ações subsequentes de script com scripts aí armazenados falhem.

      * Um serviço público de partilha de ficheiros acessível através http:// caminhos. Exemplos são Azure Blob, GitHub, OneDrive. Por exemplo, URIs, consulte [scripts](#example-script-action-scripts)de ação de script exemplo .

     * Para agrupamentos com ESP, são apoiados os wasb:// ou wasbs:// ou http[s]://URIs.

* Pode ser restringido a funcionar apenas em certos tipos de nó. Exemplos são nós os cabeçados ou nódosos operários.

* Pode ser persistido ou ad hoc.

    As ações de guião persistidas devem ter um nome único. Os scripts persistidos são usados para personalizar novos nós de trabalhador adicionados ao cluster através de operações de escala. Um script persistente também pode aplicar alterações a outro tipo de nó quando ocorrem operações de escala. Um exemplo é um nó de cabeça.

    Os guiões ad hoc não são persistentes. As ações de script utilizadas durante a criação do cluster são automaticamente persistidas. Não são aplicados aos nós dos trabalhadores adicionados ao cluster depois do guião ter sido executado. Depois pode promover um guião ad hoc para um roteiro persistente ou despromover um roteiro persistente para um script ad hoc. Os scripts que falham não são persistentes, mesmo que indiqueespecificamente que deveriam ser.

* Pode aceitar parâmetros que são usados pelo script durante a execução.

* Corra com privilégios de nível de raiz nos nós do agrupamento.

* Pode ser usado através do portal Azure, Azure PowerShell, Azure CLI ou HDInsight .NET SDK.

O aglomerado mantém um historial de todos os scripts que foram executados. A história ajuda quando precisa encontrar a identificação de um script para operações de promoção ou despromoção.

> [!IMPORTANT]  
> Não há forma automática de desfazer as mudanças feitas por uma ação de guião. Inverta manualmente as alterações ou fornece um script que as reverte.

### <a name="script-action-in-the-cluster-creation-process"></a>Ação de script no processo de criação de cluster

As ações de script usadas durante a criação de cluster são ligeiramente diferentes das ações de script executadas num cluster existente:

* O guião é automaticamente persperdoado.

* Uma falha no script pode fazer com que o processo de criação do cluster falhe.

O diagrama seguinte ilustra quando a ação do guião corre durante o processo de criação:

![Personalização e estágios de cluster HDInsight durante a criação de cluster][img-hdi-cluster-states]

O script funciona enquanto o HDInsight está a ser configurado. O script corre em paralelo em todos os nós especificados no cluster. Funciona com privilégios de raiz nos nódosos.

Pode realizar operações como serviços de paragem e arranque, incluindo serviços relacionados com a Apache Hadoop. Se parar os serviços, certifique-se de que o serviço Ambari e outros serviços relacionados com Hadoop estão a funcionar antes do script terminar. Estes serviços são necessários para determinar com sucesso a saúde e o estado do cluster enquanto está a ser criado.

Durante a criação de clusters, você pode usar muitas ações de script ao mesmo tempo. Estes scripts são invocados na ordem em que foram especificados.

> [!IMPORTANT]  
> As ações do guião devem terminar dentro de 60 minutos, ou têm tempo livre. Durante o fornecimento de cluster, o script funciona simultaneamente com outros processos de configuração e configuração. A concorrência por recursos como o tempo de CPU ou a largura de banda da rede pode fazer com que o script demore mais tempo a terminar do que no seu ambiente de desenvolvimento.
>
> Para minimizar o tempo que demora a executar o script, evite tarefas como descarregar e compilar aplicações a partir da fonte. Pré-compile as aplicações e guarde o binário no Armazenamento Azure.

### <a name="script-action-on-a-running-cluster"></a>Ação de script em um cluster de execução

Uma falha num script executado num cluster já em execução não faz com que o cluster mude automaticamente para um estado falhado. Depois de um guião terminar, o cluster deve voltar a um estado de corrida. Mesmo que o aglomerado tenha um estado de execução, o guião falhado pode ter quebrado as coisas. Por exemplo, um script pode eliminar ficheiros necessários pelo cluster.

As ações dos scripts são executadas com privilégios de raiz. Certifique-se de que compreende o que um guião faz antes de o aplicar ao seu cluster.

Quando aplica um guião a um cluster, o estado de cluster muda de **Execução** para **Aceito**. Em seguida, muda para a **configuração HDInsight** e, finalmente, de volta a **Running** para scripts de sucesso. O estado do guião está registado no histórico de ação do guião. Esta informação diz-lhe se o guião foi bem sucedido ou falhado. Por exemplo, o cmdlet `Get-AzHDInsightScriptActionHistory` PowerShell mostra o estado de um script. Devolve informações semelhantes ao seguinte texto:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Se alterar o utilizador do cluster, administrador, palavra-passe após a criação do cluster, as ações de script contra este cluster podem falhar. Se tiver alguma ação de script persistente que marque os nós dos trabalhadores, estes scripts podem falhar quando escala o cluster.

## <a name="example-script-action-scripts"></a>Scripts de ação de script exemplo

Scripts de ação script podem ser usados através dos seguintes utilitários:

* Portal do Azure
* Azure PowerShell
* CLI do Azure
* HDInsight .NET SDK

O HDInsight fornece scripts para instalar os seguintes componentes nos clusters HDInsight:

| Nome | Script |
| --- | --- |
| Adicione uma conta de Armazenamento Azure |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Ver Adicionar contas de [armazenamento adicionais ao HDInsight](hdinsight-hadoop-add-storage.md). |
| Instalar Matiz |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Consulte [instalar e utilizar o Hue nos clusters Hadoop HDInsight](hdinsight-hadoop-hue-linux.md). |
| Pré-carregar bibliotecas da Colmeia |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Consulte [adicionar bibliotecas personalizadas da Hive Apache ao criar o seu cluster HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Ação de script durante a criação de cluster

Esta secção explica as diferentes formas de utilizar as ações do script quando cria um cluster HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Use uma ação de script durante a criação de cluster a partir do portal Azure

1. Comece a criar um cluster como descrito em [Clusters baseados em Linux em HDInsight utilizando o portal Azure](hdinsight-hadoop-create-linux-clusters-portal.md). A partir do separador **de preços Configuração +,** selecione **+ Adicione a ação do script**.

    ![Ação de script de cluster do portal Azure](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Utilize a entrada __de script Seletiva__ para selecionar um script pré-fabricado. Para utilizar um script personalizado, selecione __Custom__. Em seguida, forneça o __nome__ e o __guião de Bash URI__ para o seu script.

    ![Adicione um script no formulário de script selecionado](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    A tabela seguinte descreve os elementos no formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecione um script | Para utilizar o seu próprio script, selecione __Custom__. Caso contrário, selecione um dos scripts fornecidos. |
    | Nome |Especifique um nome para a ação do guião. |
    | Roteiro de bash URI |Especifique o URI do guião. |
    | Cabeça/Trabalhador/ZooKeeper |Especifique os nós em que o guião é executado: **Cabeça,** **Trabalhador**ou **ZooKeeper**. |
    | Parâmetros |Especifique os parâmetros, se necessário pelo script. |

    Utilize a entrada __de ação do Script Persist__ para se certificar de que o script é aplicado durante as operações de escala.

1. Selecione __Criar__ para salvar o script. Depois pode usar __+ Submeter novo__ para adicionar outro script.

    ![Ações de script seletivas HDInsight](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Quando terminar de adicionar scripts, volte ao separador de **preços Configuração +.**

1. Complete os passos restantes de criação do cluster, como de costume.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Use uma ação de script a partir de modelos de Gestor de Recursos Azure

As ações do script podem ser usadas com modelos de Gestor de Recursos Azure. Por exemplo, consulte [create HDInsight Linux Cluster e execute uma ação](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/)de script .

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

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Implementar recursos com modelos de Gestor de Recursos e o Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Use uma ação de script durante a criação de cluster a partir de Azure PowerShell

Nesta secção, utiliza o cmdlet [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) para invocar scripts para personalizar um cluster. Antes de começar, certifique-se de que instala e configura o Azure PowerShell. Para utilizar estes comandos PowerShell, precisa do [Módulo AZ](https://docs.microsoft.com/powershell/azure/overview).

O seguinte script mostra como aplicar uma ação de script quando cria um cluster usando powerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Pode levar alguns minutos até que o cluster seja criado.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Utilize uma ação de script durante a criação de cluster a partir do HDInsight .NET SDK

O HDInsight .NET SDK fornece bibliotecas de clientes que facilitam o trabalho com o HDInsight a partir de uma aplicação .NET. Para obter uma amostra de código, consulte [Script Actions](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Ação de script para um cluster de execução

Esta secção explica como aplicar as ações do script a um cluster de execução.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Aplique uma ação de script a um cluster de execução do portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) e localize o seu cluster.

1. A partir da vista predefinida, em **Definições,** selecione **as ações do Script**.

1. A partir do topo da página de ações do **Script,** selecione **+ Submeta novo**.

    ![Adicione um script a um cluster de execução](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Utilize a entrada __de script Seletiva__ para selecionar um script pré-fabricado. Para utilizar um script personalizado, selecione __Custom__. Em seguida, forneça o __nome__ e o __guião de Bash URI__ para o seu script.

    ![Adicione um script no formulário de script selecionado](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    A tabela seguinte descreve os elementos no formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecione um script | Para utilizar o seu próprio script, selecione __personalizado__. Caso contrário, selecione um script fornecido. |
    | Nome |Especifique um nome para a ação do guião. |
    | Roteiro de bash URI |Especifique o URI do guião. |
    | Head/Worker/ZooKeeper |Especifique os nós em que o guião é executado: **Cabeça,** **Trabalhador**ou **ZooKeeper**. |
    | Parâmetros |Especifique os parâmetros, se necessário pelo script. |

    Utilize a entrada __de ação do script Persist__ para se certificar de que o script é aplicado durante as operações de escala.

1. Por fim, selecione o botão **Criar** para aplicar o script ao cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Aplique uma ação de script a um cluster de execução da Azure PowerShell

Para utilizar estes comandos PowerShell, precisa do [Módulo AZ](https://docs.microsoft.com/powershell/azure/overview). O exemplo que se segue mostra como aplicar uma ação de script a um cluster em execução:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Após o acabamento da operação, recebe informações semelhantes ao seguinte texto:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Aplique uma ação de script a um cluster de execução do Azure CLI

Antes de começar, certifique-se de que instala e configura o ClI Azure. Certifique-se de que tem a versão mais recente. Para mais informações, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Autenticar a sua subscrição Azure:

    ```azurecli
    az login
    ```

1. Aplique uma ação de script num cluster em execução:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    As funções válidas são `headnode`, `workernode`, `zookeepernode`, `edgenode`. Se o script deve ser aplicado a vários tipos de nó, separe as funções por um espaço. Por exemplo, `--roles headnode workernode`.

    Para persistir o guião, adicione `--persist-on-success`. Também pode persistir o guião mais tarde, utilizando `az hdinsight script-action promote`.

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Aplique uma ação de script a um cluster de execução usando a API REST

Consulte [cluster REST API em Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Aplique uma ação de script a um cluster de execução do HDInsight .NET SDK

Para um exemplo de utilização do .NET SDK para aplicar scripts num cluster, consulte Aplicar uma Ação de [Script contra um cluster HDInsight baseado em Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)em execução .

## <a name="view-history-and-promote-and-demote-script-actions"></a>Ver história e promover e despromover ações de script

### <a name="the-azure-portal"></a>O portal do Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) e localize o seu cluster.

1. A partir da vista predefinida, em **Definições,** selecione **as ações do Script**.

1. Uma história de scripts para este cluster exibe na secção de ações de script. Esta informação inclui uma lista de scripts persistentes. A imagem que se segue mostra que o script Solr foi executado neste cluster. A imagem não mostra nenhum guião persistente.

    ![As ações do script do portal submetem história](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Selecione um script do histórico para exibir a secção **Propriedades** para este script. A partir do topo do ecrã, pode reproduzir o script ou promovê-lo.

    ![Ações de script propriedades promovem](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Também pode selecionar a elipse, **...** ao direito de entradas na secção de ações de script para realizar ações.

    ![Ações de script persistidas apagam](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Função |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Recupere informações sobre as ações de guião persistidas. Este cmdlet não desfaz as ações realizadas por um guião, apenas remove a bandeira persistida.|
| `Get-AzHDInsightScriptActionHistory` |Recupere um histórico de ações de script aplicadas ao cluster ou detalhes para um script específico. |
| `Set-AzHDInsightPersistedScriptAction` |Promover uma ação de guião ad hoc para uma ação de guião persistente. |
| `Remove-AzHDInsightPersistedScriptAction` |Despromover uma ação de guião persistente para uma ação ad hoc. |

O seguinte exemplo de script demonstra usar os cmdlets para promover e, em seguida, despromover um script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>CLI do Azure

| Comando | Descrição |
| --- | --- |
| [az hdinsight script-action excluir](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Elimina uma ação de script persistida especificada do cluster. Este comando não desfaz as ações executadas por um guião, só remove a bandeira persistida.|
|[az hdinsight script-action executar](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Execute as ações do script no cluster HDInsight especificado.|
| [az hdinsight script-action list](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |Lista todas as ações de script persistidas para o cluster especificado. |
|[az hdinsight script-action lista-execução-histórico](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Lista o histórico de execução de todos os scripts para o cluster especificado.|
|[az hdinsight script-ação promover](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|Promove a execução especificada do guião ad-hoc a um script persistente.|
|[az hdinsight script-action show-detalhes de execução](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Obtém o detalhe de execução do guião para o id de execução do script.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Para um exemplo de utilização do .NET SDK para recuperar o histórico de scripts de um cluster, promover ou despromover scripts, consulte Aplicar uma Ação de [Script contra um cluster HDInsight baseado em Linux.](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)

> [!NOTE]  
> Este exemplo também demonstra como instalar uma aplicação HDInsight utilizando o .NET SDK.

## <a name="support-for-open-source-software"></a>Suporte para software de código aberto

O serviço Microsoft Azure HDInsight utiliza um ecossistema de tecnologias de código aberto formadas em torno do Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de código aberto. Para mais informações, consulte a secção de Suporte das [FAQs](https://azure.microsoft.com/support/faq/)de **Suporte** Azure . O serviço HDInsight fornece um nível adicional de suporte para componentes incorporados.

Dois tipos de componentes de código aberto estão disponíveis no serviço HDInsight:

* **Componentes incorporados.** Estes componentes são pré-instalados em clusters HDInsight e fornecem a funcionalidade central do cluster. Os seguintes componentes pertencem a esta categoria:

  * [YARN Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Gestor de Recursos.
  * A linguagem de consulta da Colmeia [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout.](https://mahout.apache.org/)

    Uma lista completa de componentes de cluster está disponível em [Quais são os componentes e versões Apache Hadoop disponíveis com hDInsight?](hdinsight-component-versioning.md)

* **Componentes personalizados.** Como utilizador do cluster, pode instalar ou utilizar na sua carga de trabalho qualquer componente disponível na comunidade ou criado por si.

> [!WARNING]  
> Os componentes fornecidos com o cluster HDInsight são totalmente suportados. O Microsoft Support ajuda a isolar e resolver problemas relacionados com estes componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a resolver o problema. O Microsoft Support poderá ser capaz de resolver o problema. Ou podem pedir-lhe para contratar canais disponíveis para as tecnologias de código aberto onde se encontra uma profunda experiência para essa tecnologia. Muitos locais comunitários podem ser usados. Exemplos são [o fórum MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) e [Stack Overflow](https://stackoverflow.com).
>
> Os projetos Apache também têm sites de projetos no [site da Apache.](https://apache.org) Um exemplo é [Hadoop.](https://hadoop.apache.org/)

O serviço HDInsight fornece várias formas de utilizar componentes personalizados. O mesmo nível de suporte aplica-se, independentemente da forma como um componente é utilizado ou instalado no cluster. A seguinte lista descreve as formas mais comuns de os componentes personalizados serem usados em clusters HDInsight:

1. **Submissão de emprego.** Hadoop ou outros tipos de trabalhos que executam ou utilizam componentes personalizados podem ser submetidos ao cluster.

2. **Personalização do cluster.** Durante a criação do cluster, pode especificar configurações adicionais e componentes personalizados que estão instalados nos nós do cluster.

3. **Amostras.** Para componentes personalizados populares, a Microsoft e outros podem fornecer amostras de como estes componentes podem ser usados em clusters HDInsight. Estas amostras são fornecidas sem apoio.

## <a name="troubleshooting"></a>Resolução de problemas

Você pode usar a UI web Ambari para ver informações registadas por ações de script. Se o script falhar durante a criação do cluster, os registos também estão disponíveis na conta de armazenamento predefinida associada ao cluster. Esta secção fornece informações sobre como recuperar os registos utilizando ambas as opções.

### <a name="the-apache-ambari-web-ui"></a>A Teia Apache Ambari UI

1. De um navegador web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, onde `CLUSTERNAME` é o nome do seu cluster.

1. A partir do bar no topo da página, selecione a entrada de **operações.** Uma lista apresenta operações atuais e anteriores feitas no cluster através de Ambari.

    ![Barra de UI web ambari com ops selecionadas](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

1. Encontre as entradas que **tenham executado\_customscriptaction** na coluna **Operações.** Estas entradas são criadas quando as ações do script são executadas.

    ![Operações de ação do script Apache Ambari](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Para ver a saída **STDOUT** e **STDERR,** selecione a entrada de **execução\customscriptaction** e faça a broca através das ligações. Esta saída é gerada quando o script corre e pode ter informações úteis.

### <a name="access-logs-from-the-default-storage-account"></a>Registos de acesso da conta de armazenamento por defeito

Se a criação do cluster falhar devido a um erro de script, os registos são mantidos na conta de armazenamento do cluster.

* Os registos de armazenamento estão disponíveis em `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Registos de ação de script](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    Sob este diretório, os troncos são organizados separadamente para **o nódeo de cabeça,** **nó de trabalhador,** e **nó de zookeeper.** Veja os exemplos seguintes:

    * **Headnode**: `<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Nó dos trabalhadores**: `<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Nó do zookeeper**: `<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Toda **a stdout** e **stderr** do anfitrião correspondente é enviada para a conta de armazenamento. Há uma **saída:\*.txt** e **erros-\*.txt** para cada ação de guião. O ficheiro **output-*.txt** contém informações sobre o URI do script que foi executado no hospedeiro. O seguinte texto é um exemplo desta informação:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* É possível que crie repetidamente um grupo de ação de guião com o mesmo nome. Nesse caso, pode distinguir os registos relevantes com base no nome da pasta **DATE.** Por exemplo, a estrutura de pasta para um cluster, **mycluster,** criada em datas diferentes, parece semelhante às seguintes entradas de registo:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se criar um cluster de ação de script com o mesmo nome no mesmo dia, pode utilizar o prefixo único para identificar os ficheiros de registo relevantes.

* Se criar um aglomerado perto das 00:00, meia-noite, é possível que os ficheiros de registo se estem por dois dias. Nesse caso, vê-se duas pastas de datadiferentes para o mesmo cluster.

* O upload de ficheiros de registo para o recipiente predefinido pode demorar até cinco minutos, especialmente para grandes aglomerados. Por isso, se quiser aceder aos registos, não deve eliminar imediatamente o cluster se uma ação de script falhar.

### <a name="ambari-watchdog"></a>Cão de guarda ambari

> [!WARNING]  
> Não altere a palavra-passe para o cão de guarda Ambari, hdinsightwatchdog, no seu cluster HDInsight baseado em Linux. Alterar a palavra-passe para esta conta quebra a capacidade de executar novas ações de script no cluster HDInsight.

### <a name="cant-import-name-blobservice"></a>Não se pode importar o nome BlobService

__Sintomas.__ A ação do guião falha. Texto semelhante aos seguintes erros exibidos quando visualiza a operação em Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Porque.__ Este erro ocorre se atualizar o cliente Python Azure Storage que está incluído no cluster HDInsight. A HDInsight espera o cliente do Azure Storage 0.20.0.

__Resolução__. Para resolver este erro, ligue-se manualmente a cada nó de cluster utilizando `ssh`. Executar o seguinte comando para reinstalar a versão correta do cliente de armazenamento:

```bash
sudo pip install azure-storage==0.20.0
```

Para obter informações sobre a ligação ao cluster com o SSH, consulte [Connect to HDInsight (Apache Hadoop) utilizando o SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>A história não mostra os scripts usados durante a criação de clusters

Se o seu cluster foi criado antes de 15 de março de 2016, pode não ver uma entrada no histórico de ação de scripts. Redimensionar o cluster faz com que os scripts apareçam no histórico de ação de scripts.

Há duas exceções:

* O seu cluster foi criado antes de 1 de setembro de 2015. Esta data é quando as ações do guião foram introduzidas. Qualquer cluster criado antes desta data não poderia ter usado ações de script para criação de clusters.

* Usaste várias ações de guião durante a criação de clusters. Ou usou o mesmo nome para vários scripts ou o mesmo nome, o mesmo URI, mas diferentes parâmetros para vários scripts. Nestes casos, obtém-se o seguinte erro:

    Nenhuma nova ação de script pode ser executada neste cluster por causa de nomes de scripts contraditórios em scripts existentes. Os nomes de scriptfornecidos na criação de clusters devem ser únicos. Os scripts existentes são executados em redimensionamento.

## <a name="next-steps"></a>Passos seguintes

* [Desenvolver scripts de ação de script para HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Adicione armazenamento adicional a um cluster HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Estágios durante a criação de cluster"
