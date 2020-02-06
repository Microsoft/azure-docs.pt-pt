---
title: Personalizar clusters do Azure HDInsight usando ações de script
description: Adicione componentes personalizados a clusters HDInsight baseados em Linux usando ações de script. As ações de script são scripts bash que podem ser usados para personalizar a configuração do cluster ou adicionar serviços e utilitários adicionais como matiz, Solr ou R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: 0930bbcfff41a667f08f5dfc5744c16476ddd8a1
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031458"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Personalizar clusters do Azure HDInsight usando ações de script

O Azure HDInsight fornece um método de configuração chamado ações de **script** que invoca scripts personalizados para personalizar o cluster. Esses scripts são usados para instalar componentes adicionais e alterar as definições de configuração. As ações de script podem ser usadas durante ou após a criação do cluster.

As ações de script também podem ser publicadas no Azure Marketplace como um aplicativo do HDInsight. Para obter mais informações sobre as aplicações HDInsight, consulte [Publicar uma aplicação HDInsight no Mercado Azure](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Permissões

Para um cluster HDInsight ingressado no domínio, há duas permissões do Apache Ambari que são necessárias quando você usa ações de script com o cluster:

* **AMBARI. EXECUTAR\_COMANDO DE\_PERSONALIZADO**. A função de administrador Ambari tem essa permissão por padrão.
* **AGLOMERADO. EXECUTAR\_COMANDO DE\_PERSONALIZADO**. O administrador do cluster HDInsight e o administrador do Ambari têm essa permissão por padrão.

Para obter mais informações sobre o trabalho com permissões com hDInsight unida por domínios, consulte [Gerir os clusters HDInsight com](./domain-joined/apache-domain-joined-manage.md)o Pacote de Segurança Empresarial .

## <a name="access-control"></a>Controlo de acesso

Se você não for o administrador ou o proprietário de sua assinatura do Azure, sua conta deverá ter pelo menos acesso de colaborador ao grupo de recursos que contém o cluster HDInsight.

Se você criar um cluster HDInsight, alguém com pelo menos acesso de colaborador à assinatura do Azure deverá ter registrado anteriormente o provedor para HDInsight. O registo do fornecedor acontece quando um utilizador com o acesso Contribuidor para a subscrição cria um recurso na mesma pela primeira vez. Também pode ser feito sem criar um recurso se [registar um fornecedor utilizando o REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Obtenha mais informações sobre como trabalhar com o gerenciamento de acesso:

* [Get started with access management in the Azure portal (Introdução à gestão do acesso no portal do Azure)](../role-based-access-control/overview.md)
* [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Entender as ações de script

Uma ação de script é um script bash que é executado em nós em um cluster HDInsight. As características e os recursos das ações de script são os seguintes:

* Deve ser armazenado em um URI que seja acessível a partir do cluster HDInsight. Os seguintes locais de armazenamento são possíveis:

    * Para clusters regulares:

      * ADLS Gen1: a entidade de serviço que o HDInsight usa para acessar Data Lake Storage deve ter acesso de leitura ao script. O formato URI para scripts armazenados em Data Lake Storage Gen1 é `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

      * Um blob em uma conta de armazenamento do Azure que seja a conta de armazenamento primária ou adicional para o cluster HDInsight. O HDInsight recebe acesso a esses dois tipos de contas de armazenamento durante a criação do cluster.

        > [!IMPORTANT]  
        > Não gire a chave de armazenamento nesta conta de armazenamento do Azure, pois isso causará ações de script subsequentes com scripts armazenados ali para falhar.

      * Um serviço de compartilhamento de arquivos público acessível por meio de caminhos http://. Os exemplos são blob do Azure, GitHub, OneDrive.

        Por exemplo, URIs, consulte [scripts](#example-script-action-scripts)de ação de script exemplo .

     * Para clusters com ESP:

         * Apoiam-se as wasb:// ou wasbs:// ou http[s]:// URIs.

* Pode ser restrito a executar somente em determinados tipos de nó. Os exemplos são nós de cabeçalho ou nós de trabalho.

* Pode ser persistente ou ad hoc.

    Os scripts persistentes são usados para personalizar novos nós de trabalho adicionados ao cluster por meio de operações de dimensionamento. Um script persistente também pode aplicar alterações a outro tipo de nó quando ocorrem operações de dimensionamento. Um exemplo é um nó de cabeçalho.

  > [!IMPORTANT]  
  > As ações de script persistentes devem ter um nome exclusivo.

    Scripts ad hoc não são persistentes. Eles não são aplicados a nós de trabalho adicionados ao cluster após a execução do script. Em seguida, você pode promover um script ad hoc para um script persistente ou rebaixar um script persistente para um script ad hoc.

  > [!IMPORTANT]  
  > As ações de script usadas durante a criação do cluster são persistidas automaticamente.
  >
  > Scripts que falham não são persistidos, mesmo que você indique especificamente que eles devem ser.

* Pode aceitar parâmetros que são usados pelo script durante a execução.

* Execute com privilégios de nível raiz nos nós de cluster.

* Pode ser usado por meio do portal do Azure, Azure PowerShell, a CLI clássica do Azure ou o SDK do .NET do HDInsight.

O cluster mantém um histórico de todos os scripts que foram executados. O histórico ajuda quando você precisa localizar a ID de um script para operações de promoção ou rebaixamento.

> [!IMPORTANT]  
> Não há uma maneira automática de desfazer as alterações feitas por uma ação de script. Reverta manualmente as alterações ou forneça um script que as reverta.

### <a name="script-action-in-the-cluster-creation-process"></a>Ação de script no processo de criação de cluster

As ações de script usadas durante a criação do cluster são ligeiramente diferentes das ações de script executadas em um cluster existente:

* O script é persistido automaticamente.

* Uma falha no script pode fazer com que o processo de criação do cluster falhe.

O diagrama a seguir ilustra quando a ação de script é executada durante o processo de criação:

![Personalização e estágios de cluster HDInsight durante a criação de cluster][img-hdi-cluster-states]

O script é executado enquanto o HDInsight está sendo configurado. O script é executado em paralelo em todos os nós especificados no cluster. Ele é executado com privilégios de raiz nos nós.

> [!NOTE]  
> Você pode executar operações como parar e iniciar serviços, incluindo serviços relacionados ao Apache Hadoop. Se você parar os serviços do, verifique se o serviço Ambari e outros serviços relacionados ao Hadoop estão em execução antes de o script ser concluído. Esses serviços são necessários para determinar com êxito a integridade e o estado do cluster enquanto ele está sendo criado.

Durante a criação do cluster, você pode usar várias ações de script ao mesmo tempo. Esses scripts são invocados na ordem em que foram especificados.

> [!IMPORTANT]  
> As ações de script devem ser concluídas dentro de 60 minutos ou expiram. Durante o provisionamento do cluster, o script é executado simultaneamente com outros processos de instalação e configuração. A competição por recursos como tempo de CPU ou largura de banda de rede pode fazer com que o script demore mais para ser concluído do que em seu ambiente de desenvolvimento.
>
> Para minimizar o tempo necessário para executar o script, evite tarefas como baixar e compilar aplicativos da origem. Compile aplicativos e armazene o binário no armazenamento do Azure.

### <a name="script-action-on-a-running-cluster"></a>Ação de script em um cluster em execução

Uma falha em um script executado em um cluster já em execução não faz com que o cluster mude automaticamente para um estado de falha. Após a conclusão de um script, o cluster deve retornar a um estado de execução.

> [!IMPORTANT]  
> Mesmo que o cluster tenha um estado de execução, o script com falha pode ter quebrado coisas. Por exemplo, um script pode excluir arquivos necessários para o cluster.
>
> As ações de scripts são executadas com privilégios de raiz. Verifique se você entendeu o que um script faz antes de aplicá-lo ao cluster.

Quando aplica um guião a um cluster, o estado de cluster muda de **Execução** para **Aceito**. Em seguida, muda para a **configuração HDInsight** e, finalmente, de volta a **Running** para scripts de sucesso. O status do script é registrado no histórico de ações de script. Essas informações indicam se o script foi bem-sucedido ou falhou. Por exemplo, o cmdlet `Get-AzHDInsightScriptActionHistory` PowerShell mostra o estado de um script. Ele retorna informações semelhantes ao seguinte texto:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Se você alterar o usuário do cluster, o administrador, a senha após a criação do cluster, as ações de script executadas nesse cluster poderão falhar. Se você tiver ações de script persistentes que destinam-se a nós de trabalho, esses scripts poderão falhar quando você dimensionar o cluster.

## <a name="example-script-action-scripts"></a>Scripts de ação de script de exemplo

Scripts de ação de script podem ser usados por meio dos seguintes utilitários:

* O portal do Azure
* Azure PowerShell
* A CLI clássica do Azure
* SDK do .NET HDInsight An

O HDInsight fornece scripts para instalar os seguintes componentes em clusters HDInsight:

| Nome | Script |
| --- | --- |
| Adicionar uma conta de armazenamento do Azure |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Ver Adicionar contas de [armazenamento adicionais ao HDInsight](hdinsight-hadoop-add-storage.md). |
| Instalar Matiz |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Consulte [instalar e utilizar o Hue nos clusters Hadoop HDInsight](hdinsight-hadoop-hue-linux.md). |
| Pré-carregar bibliotecas Hive |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Consulte [adicionar bibliotecas personalizadas da Hive Apache ao criar o seu cluster HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Usar uma ação de script durante a criação do cluster

Esta seção explica as diferentes maneiras pelas quais você pode usar ações de script ao criar um cluster HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Usar uma ação de script durante a criação do cluster no portal do Azure

1. Comece a criar um cluster como descrito em [Clusters baseados em Linux em HDInsight utilizando o portal Azure](hdinsight-hadoop-create-linux-clusters-portal.md). A partir do separador **de preços Configuração +,** selecione **+ Adicione a ação do script**.

    ![Ação de script de cluster portal do Azure](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Utilize a entrada __de script Seletiva__ para selecionar um script pré-fabricado. Para utilizar um script personalizado, selecione __Custom__. Em seguida, forneça o __nome__ e o __guião de Bash URI__ para o seu script.

    ![Adicionar um script no formulário selecionar script](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    A tabela a seguir descreve os elementos no formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecionar um script | Para utilizar o seu próprio script, selecione __Custom__. Caso contrário, selecione um dos scripts fornecidos. |
    | Nome |Especifique um nome para a ação de script. |
    | URI do script bash |Especifique o URI do script. |
    | Cabeçalho/trabalhador/ZooKeeper |Especifique os nós em que o guião é executado: **Cabeça,** **Trabalhador**ou **ZooKeeper**. |
    | Parâmetros |Especifique os parâmetros, se exigido pelo script. |

    Utilize a entrada __de ação do Script Persist__ para se certificar de que o script é aplicado durante as operações de escala.

1. Selecione __Criar__ para salvar o script. Depois pode usar __+ Submeter novo__ para adicionar outro script.

    ![Ações múltiplas de script do HDInsight](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Quando terminar de adicionar scripts, volte ao separador de **preços Configuração +.**

1. Complete os passos restantes de criação do cluster, como de costume.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Usar uma ação de script de modelos de Azure Resource Manager

As ações de script podem ser usadas com modelos de Azure Resource Manager. Por exemplo, consulte [create HDInsight Linux Cluster e execute uma ação](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/)de script .

Neste exemplo, a ação de script é adicionada usando o seguinte código:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Obtenha mais informações sobre como implantar um modelo:

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Implementar recursos com modelos de Gestor de Recursos e o Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Usar uma ação de script durante a criação do cluster de Azure PowerShell

Nesta secção, utiliza o cmdlet [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) para invocar scripts para personalizar um cluster. Antes de começar, certifique-se de instalar e configurar o Azure PowerShell. Para utilizar estes comandos PowerShell, precisa do [Módulo AZ](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O script a seguir mostra como aplicar uma ação de script quando você cria um cluster usando o PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Pode levar vários minutos para que o cluster seja criado.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Usar uma ação de script durante a criação do cluster do SDK do .NET do HDInsight

O SDK do .NET do HDInsight fornece bibliotecas de cliente que facilitam o trabalho com o HDInsight de um aplicativo .NET. Para obter uma amostra de código, consulte [Script Actions](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Aplicar uma ação de script a um cluster em execução

Esta seção explica como aplicar ações de script a um cluster em execução.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Aplicar uma ação de script a um cluster em execução do portal do Azure

Vá ao [portal Azure:](https://portal.azure.com)

1. Do menu esquerdo, navegue para **todos os serviços** >  **Analytics** > **clusters HDInsight**.

1. Selecione o cluster na lista, que abre a exibição padrão.

1. A partir da vista predefinida, em **Definições,** selecione **as ações do Script**.

1. A partir do topo da página de ações do **Script,** selecione **+ Submeta novo**.

    ![Adicionar um script a um cluster em execução](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Utilize a entrada __de script Seletiva__ para selecionar um script pré-fabricado. Para utilizar um script personalizado, selecione __Custom__. Em seguida, forneça o __nome__ e o __guião de Bash URI__ para o seu script.

    ![Adicionar um script no formulário selecionar script](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    A tabela a seguir descreve os elementos no formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecionar um script | Para utilizar o seu próprio script, selecione __personalizado__. Caso contrário, selecione um script fornecido. |
    | Nome |Especifique um nome para a ação de script. |
    | URI do script bash |Especifique o URI do script. |
    | Head/Worker/ZooKeeper |Especifique os nós em que o guião é executado: **Cabeça,** **Trabalhador**ou **ZooKeeper**. |
    | Parâmetros |Especifique os parâmetros, se exigido pelo script. |

    Utilize a entrada __de ação do script Persist__ para se certificar de que o script é aplicado durante as operações de escala.

1. Por fim, selecione o botão **Criar** para aplicar o script ao cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Aplicar uma ação de script a um cluster em execução do Azure PowerShell

Para utilizar estes comandos PowerShell, precisa do [Módulo AZ](https://docs.microsoft.com/powershell/azure/overview).

O exemplo a seguir mostra como aplicar uma ação de script a um cluster em execução:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Após a conclusão da operação, você receberá informações semelhantes ao seguinte texto:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Aplicar uma ação de script a um cluster em execução do CLI do Azure

Antes de começar, certifique-se de instalar e configurar o CLI do Azure. Para mais informações, consulte [Instale o clássico CLI azure](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Alternar para o modo de Azure Resource Manager:

    ```bash
    azure config mode arm
    ```

2. Autenticar para sua assinatura do Azure:

    ```bash
    azure login
    ```

3. Aplicar uma ação de script a um cluster em execução:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Se você omitir parâmetros para esse comando, você será solicitado a fornecê-los. Se o script que especifica com `-u` aceitar parâmetros, pode especificá-los utilizando o parâmetro `-p`.

    Os tipos de nós válidos são `headnode`, `workernode`e `zookeeper`. Se o script for aplicado a vários tipos de nó, especifique os tipos separados por um ponto e vírgula `;`. Por exemplo, `-n headnode;workernode`.

    Para persistir o guião, adicione `--persistOnSuccess`. Também pode persistir o guião mais tarde, utilizando `azure hdinsight script-action persisted set`.

    Após a conclusão do trabalho, você receberá uma saída semelhante ao seguinte texto:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Aplicar uma ação de script a um cluster em execução usando a API REST

Consulte [cluster REST API em Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Aplicar uma ação de script a um cluster em execução do SDK do .NET do HDInsight

Para um exemplo de utilização do .NET SDK para aplicar scripts num cluster, consulte Aplicar uma Ação de [Script contra um cluster HDInsight baseado em Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)em execução .

## <a name="view-history-and-promote-and-demote-script-actions"></a>Exibir histórico e promover e rebaixar ações de script

### <a name="the-azure-portal"></a>O portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Do menu esquerdo, navegue para **todos os serviços** > **Analytics** > **clusters HDInsight**.

1. Selecione o cluster na lista, que abre a exibição padrão.

1. A partir da vista predefinida, em **Definições,** selecione **as ações do Script**.

1. Um histórico de scripts para este cluster é exibido na seção ações de script. Essas informações incluem uma lista de scripts persistentes. A captura de tela a seguir mostra que o script Solr foi executado neste cluster. A captura de tela não mostra nenhum script persistente.

    ![Histórico de envio de ações de script do portal](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Selecione um script do histórico para exibir a secção **Propriedades** para este script. Na parte superior da tela, você pode executar novamente o script ou promovê-lo.

    ![Propriedades de ações de script promover](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Também pode selecionar a elipse, **...** ao direito de entradas na secção de ações de script para realizar ações.

    ![Exclusão de ações de script persistentes](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Função |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Recuperar informações sobre ações de script persistentes. |
| `Get-AzHDInsightScriptActionHistory` |Recupere um histórico das ações de script aplicadas ao cluster ou detalhes de um script específico. |
| `Set-AzHDInsightPersistedScriptAction` |Promover uma ação de script ad hoc a uma ação de script persistente. |
| `Remove-AzHDInsightPersistedScriptAction` |Rebaixar uma ação de script persistente para uma ação ad hoc. |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction` não desfaz as ações executadas por um guião. Esse cmdlet Remove apenas o sinalizador persistente.

O script de exemplo a seguir demonstra como usar os cmdlets para promover e, em seguida, rebaixar um script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>A CLI clássica do Azure

| cmdlet | Função |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Recupere uma lista de ações de script persistentes. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Recuperar informações sobre uma ação de script persistente específica. |
| `azure hdinsight script-action history list <clustername>` |Recupere um histórico das ações de script aplicadas ao cluster. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Recuperar informações sobre uma ação de script específica. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Promover uma ação de script ad hoc a uma ação de script persistente. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Rebaixar uma ação de script persistente para uma ação ad hoc. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` não desfaz as ações executadas por um guião. Esse cmdlet Remove apenas o sinalizador persistente.

### <a name="the-hdinsight-net-sdk"></a>O SDK do .NET do HDInsight

Para um exemplo de utilização do .NET SDK para recuperar o histórico de scripts de um cluster, promover ou despromover scripts, consulte Aplicar uma Ação de [Script contra um cluster HDInsight baseado em Linux.](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)

> [!NOTE]  
> Este exemplo também demonstra como instalar um aplicativo do HDInsight usando o SDK do .NET.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para software livre usado em clusters HDInsight

O serviço de Microsoft Azure HDInsight usa um ecossistema de tecnologias de software livre, formadas sobre Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre. Para mais informações, consulte a secção de Suporte das [FAQs](https://azure.microsoft.com/support/faq/)de **Suporte** Azure . O serviço HDInsight fornece um nível adicional de suporte para componentes internos.

Dois tipos de componentes de código-fonte aberto estão disponíveis no serviço HDInsight:

* **Componentes incorporados.** Esses componentes são pré-instalados em clusters HDInsight e fornecem a funcionalidade básica do cluster. Os seguintes componentes pertencem a esta categoria:

  * [YARN Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Gestor de Recursos.
  * A linguagem de consulta da Colmeia [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout.](https://mahout.apache.org/)

    Uma lista completa de componentes de cluster está disponível em [Quais são os componentes e versões Apache Hadoop disponíveis com hDInsight?](hdinsight-component-versioning.md)

* **Componentes personalizados.** Como usuário do cluster, você pode instalar ou usar o em sua carga de trabalho qualquer componente disponível na Comunidade ou criado por você.

> [!WARNING]  
> Há suporte total para os componentes fornecidos com o cluster HDInsight. Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Suporte da Microsoft pode ser capaz de resolver o problema. Ou eles podem pedir que você envolva canais disponíveis para as tecnologias de software livre, nas quais a profunda experiência para essa tecnologia é encontrada. Muitos sites de comunidade podem ser usados. Exemplos são [o fórum MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) e [Stack Overflow](https://stackoverflow.com).
>
> Os projetos Apache também têm sites de projetos no [site da Apache.](https://apache.org) Um exemplo é [Hadoop.](https://hadoop.apache.org/)

O serviço HDInsight fornece várias maneiras de usar componentes personalizados. O mesmo nível de suporte se aplica, independentemente de como um componente é usado ou instalado no cluster. A lista a seguir descreve as maneiras mais comuns de usar os componentes personalizados em clusters HDInsight:

1. **Submissão de emprego.** O Hadoop ou outros tipos de trabalhos que executam ou usam componentes personalizados podem ser enviados para o cluster.

2. **Personalização do cluster.** Durante a criação do cluster, você pode especificar configurações adicionais e componentes personalizados que estão instalados nos nós do cluster.

3. **Amostras.** Para componentes personalizados populares, a Microsoft e outros podem fornecer exemplos de como esses componentes podem ser usados em clusters HDInsight. Esses exemplos são fornecidos sem suporte.

## <a name="troubleshooting"></a>Resolução de problemas

Você pode usar a interface do usuário da Web do amAmbari para exibir informações registradas por ações de script. Se o script falhar durante a criação do cluster, os logs também estarão disponíveis na conta de armazenamento padrão associada ao cluster. Esta seção fornece informações sobre como recuperar os logs usando essas duas opções.

### <a name="the-apache-ambari-web-ui"></a>A interface do usuário da Web do Apache amAmbari

1. No seu navegador, vá para `https://CLUSTERNAME.azurehdinsight.net`. Substitua **CLUSTERNAME** pelo nome do cluster do HDInsight.

    Quando solicitado, introduza o nome da conta de administração, **o administrador**e a palavra-passe para o cluster. Talvez seja necessário inserir novamente as credenciais de administrador em um formulário da Web.

2. A partir do bar no topo da página, selecione a entrada de **operações.** Uma lista exibe as operações atuais e anteriores realizadas no cluster por meio de Ambari.

    ![Barra de interface do usuário da Web do amAmbari com Ops selecionado](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

3. Encontre as entradas que **tenham executado\_customscriptaction** na coluna **Operações.** Essas entradas são criadas quando as ações de script são executadas.

    ![Operações de ação de script do Apache Ambari](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Para ver a saída **STDOUT** e **STDERR,** selecione a entrada de **execução\customscriptaction** e faça a broca através das ligações. Essa saída é gerada quando o script é executado e pode ter informações úteis.

### <a name="access-logs-from-the-default-storage-account"></a>Acessar logs da conta de armazenamento padrão

Se a criação do cluster falhar devido a um erro de script, os logs serão mantidos na conta de armazenamento de cluster.

* Os registos de armazenamento estão disponíveis em `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Logs de ação de script](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    Sob este diretório, os troncos são organizados separadamente para **o nódeo de cabeça,** **nó de trabalhador,** e **nó de zookeeper.** Veja os exemplos seguintes:

    * **Headnode**: `<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Nó dos trabalhadores**: `<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Nó do zookeeper**: `<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Toda **a stdout** e **stderr** do anfitrião correspondente é enviada para a conta de armazenamento. Há uma **saída:\*.txt** e **erros-\*.txt** para cada ação de guião. O ficheiro **output-*.txt** contém informações sobre o URI do script que foi executado no hospedeiro. O texto a seguir é um exemplo dessas informações:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* É possível que você crie repetidamente um cluster de ação de script com o mesmo nome. Nesse caso, pode distinguir os registos relevantes com base no nome da pasta **DATE.** Por exemplo, a estrutura de pasta para um cluster, **mycluster,** criada em datas diferentes, parece semelhante às seguintes entradas de registo:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se você criar um cluster de ação de script com o mesmo nome no mesmo dia, poderá usar o prefixo exclusivo para identificar os arquivos de log relevantes.

* Se você criar um cluster perto de 12:00, meia-noite, é possível que os arquivos de log abranjam em dois dias. Nesse caso, você verá duas pastas de data diferentes para o mesmo cluster.

* Carregar arquivos de log no contêiner padrão pode levar até cinco minutos, especialmente para clusters grandes. Portanto, se você quiser acessar os logs, não deverá excluir imediatamente o cluster se uma ação de script falhar.

### <a name="ambari-watchdog"></a>Watchdog Ambari

> [!WARNING]  
> Não altere a senha do Watchdog do Ambari, hdinsightwatchdog, em seu cluster HDInsight baseado em Linux. A alteração da senha para essa conta interrompe a capacidade de executar novas ações de script no cluster HDInsight.

### <a name="cant-import-name-blobservice"></a>Não é possível importar o nome BlobService

__Sintomas.__ A ação de script falha. Um texto semelhante ao erro a seguir é exibido quando você exibe a operação em Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Porque.__ Esse erro ocorrerá se você atualizar o cliente de armazenamento do Azure do Python que está incluído no cluster HDInsight. O HDInsight espera o cliente de armazenamento do Azure 0.20.0.

__Resolução__. Para resolver este erro, ligue-se manualmente a cada nó de cluster utilizando `ssh`. Execute o seguinte comando para reinstalar a versão correta do cliente de armazenamento:

```bash
sudo pip install azure-storage==0.20.0
```

Para obter informações sobre a ligação ao cluster com o SSH, consulte [Connect to HDInsight (Apache Hadoop) utilizando o SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>O histórico não mostra os scripts usados durante a criação do cluster

Se o cluster tiver sido criado antes de 15 de março de 2016, talvez você não veja uma entrada no histórico de ação de script. O redimensionamento do cluster faz com que os scripts apareçam no histórico de ações de script.

Há duas exceções:

* O cluster foi criado antes de 1º de setembro de 2015. Essa data é quando as ações de script foram introduzidas. Qualquer cluster criado antes dessa data não pode ter usado ações de script para a criação do cluster.

* Você usou várias ações de script durante a criação do cluster. Ou você usou o mesmo nome para vários scripts ou o mesmo nome, mesmo URI, mas parâmetros diferentes para vários scripts. Nesses casos, você obtém o seguinte erro:

    Nenhuma nova ação de script pode ser executada neste cluster devido a nomes de script conflitantes em scripts existentes. Os nomes de script fornecidos na criação do cluster devem ser todos exclusivos. Os scripts existentes são executados no redimensionamento.

## <a name="next-steps"></a>Passos seguintes

* [Desenvolver scripts de ação de script para HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Adicione armazenamento adicional a um cluster HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Estágios durante a criação de cluster"
