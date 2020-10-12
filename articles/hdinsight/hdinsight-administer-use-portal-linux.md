---
title: Gerir clusters Apache Hadoop em HDInsight usando o portal Azure
description: Aprenda a criar e gerir clusters Azure HDInsight utilizando o portal Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/24/2020
ms.openlocfilehash: 05d057be76a1b468f892b3123080e32a948153ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82598503"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gerir os clusters Apache Hadoop em HDInsight utilizando o portal Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Utilizando o [portal Azure,](https://portal.azure.com)pode gerir os clusters [Apache Hadoop](https://hadoop.apache.org/) em Azure HDInsight. Utilize o seletor de separadores acima para obter informações sobre a gestão dos clusters Hadoop em HDInsight utilizando outras ferramentas.

## <a name="prerequisites"></a>Pré-requisitos

Um aglomerado Apache Hadoop existente em HDInsight.  Consulte [os clusters baseados em Linux em HDInsight utilizando o portal Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Introdução

Inicie sessão em [https://portal.azure.com](https://portal.azure.com).

## <a name="list-and-show-clusters"></a><a name="showClusters"></a> Lista e mostrar clusters

A página **de clusters HDInsight** listará os seus clusters existentes.  A partir do portal:
1. Selecione **Todos os serviços** do menu esquerdo.
2. Selecione **clusters HDInsight** em **ANALYTICS**.

## <a name="cluster-home-page"></a><a name="homePage"></a> Página inicial do cluster

Selecione o nome do seu cluster na página [**de clusters HDInsight.**](#showClusters)  Isto abrirá a **vista geral,** que se parece com a seguinte imagem:

![Essenciais do cluster do portal Azure HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Menu superior:**  

| Item| Descrição |
|---|---|
|Mover|Move o cluster para outro grupo de recursos ou para outra subscrição.|
|Eliminar|Elimina o aglomerado. |
|Atualizar|Refresca a vista.|

**Menu esquerdo:**  

  - **Menu superior à esquerda**

    | Item| Descrição |
    |---|---|
    |Descrição geral|Fornece informações gerais para o seu cluster.|
    |Registo de atividades|Mostrar e consultar registos de atividades.|
    |Controlo de acesso (IAM)|Use tarefas de função.  Consulte [as atribuições de funções para gerir o acesso aos seus recursos de subscrição Azure.](../role-based-access-control/role-assignments-portal.md)|
    |Etiquetas|Permite-lhe definir pares de chaves/valor para definir uma taxonomia personalizada dos seus serviços na nuvem. Por exemplo, pode criar um **projeto**de nome chave , e depois usar um valor comum para todos os serviços associados a um projeto específico.|
    |Diagnosticar e resolver problemas|Apresentar informações de resolução de problemas.|
    |Início Rápido|Exibe informações que o ajudam a começar a usar HDInsight.|
    |Ferramentas|Informação de ajuda para ferramentas relacionadas com HDInsight.|

  - **Menu Definições**  

    | Item| Descrição |
    |---|---|
    |Tamanho do cluster|Verifique, aumente e diminua o número de nós de trabalhadores de cluster. Ver [aglomerados de escala](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Limites de quota|Exiba os núcleos usados e disponíveis para a sua subscrição.|
    |SSH + Login de Cluster|Mostra as instruções para ligar ao cluster utilizando a ligação Secure Shell (SSH). Para obter mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Ger1 de Armazenamento do Data Lake|Configure o acesso ao Data Lake Storage Gen1.  Consulte [Quickstart: Instale clusters em HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).|
    |Contas de armazenamento|Veja as contas de armazenamento e as chaves. As contas de armazenamento são configuradas durante o processo de criação do cluster.|
    |Aplicações|Adicionar/remover aplicações HDInsight.  Consulte [as aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).|
    |Ações de script|Executar scripts bash no cluster. Consulte [os clusters HDInsight baseados em Linux utilizando a Ação do Script](hdinsight-hadoop-customize-cluster-linux.md).|
    |Metastões externas|Veja as metástases [Apache Hive](https://hive.apache.org/) e [Apache Oozie.](https://oozie.apache.org/) As metástases só podem ser configuradas durante o processo de criação do cluster.|
    |Parceiro HDInsight|Adicione/remova o atual Parceiro HDInsight.|
    |Propriedades|Ver as propriedades do [cluster.](#properties)|
    |Bloqueios|Adicione um bloqueio para evitar que o cluster seja modificado ou eliminado.|
    |Exportar modelo|Exibir e exportar o modelo Azure Resource Manager para o cluster. Atualmente, só é possível exportar a conta de armazenamento Azure dependente. Consulte [os clusters Apache Hadoop baseados em Linux em HDInsight utilizando modelos do Gestor de Recursos Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Menu de monitorização**

    | Item| Descrição |
    |---|---|
    |Alertas|Gerir os alertas e ações.|
    |Métricas|Monitorize as métricas do cluster nos registos do Monitor Azure.|
    |Definições de diagnóstico|Definições sobre onde armazenar as métricas de diagnóstico.|
    |Azure Monitor|Monitorize o seu cluster no Monitor Azure.|

  - **Suporte + menu de resolução de problemas**

    | Item| Descrição |
    |---|---|
    |Estado de funcionamento de recursos|Consulte [a visão geral da saúde dos recursos Azure](../service-health/resource-health-overview.md).|
    |Novo pedido de suporte|Permite-lhe criar um bilhete de suporte com suporte da Microsoft.|

## <a name="cluster-properties"></a><a name="properties"></a> Propriedades de Cluster

A partir da página inicial do [cluster,](#homePage)em **Definições** selecione **Propriedades**.

|Item | Descrição |
|---|---|
|NOME ANFITRIÃO|Nome do cluster.|
|CLUSTER URL|O URL para a interface web Ambari.|
|Ponto Final Privado|O ponto final privado para o aglomerado.|
|Concha segura (SSH)|O nome de utilizador e o nome de anfitrião para utilizar no acesso ao cluster via SSH.|
|ESTADO|Um de: Abortado, Aceite, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operacional, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued ou ClusterCustomization.|
|REGIÃO|Local azul. Para obter uma lista de locais de Azure suportados, consulte a caixa de lista suspensa da **Região** nos [preços hdInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|DATA DE CRIAÇÃO|A data em que o aglomerado foi implantado.|
|Sistema Operativo|Ou **Windows** ou **Linux.**|
|TIPO|Hadoop, HBase, Storm, Spark.|
|Versão|Consulte [as versões HDInsight](hdinsight-component-versioning.md).|
|Versão TLS mínima|A versão TLS.|
|SUBSCRIÇÃO|Nome da assinatura.|
|FONTE DE DADOS PREDEFINIDO|O sistema de ficheiros de cluster predefinido.|
|Tamanhos dos nódoas do trabalhador|O tamanho VM selecionado dos nós operários.|
|Tamanho do nó Principal|O tamanho VM selecionado dos nós da cabeça.|
|Rede virtual|O nome da Rede Virtual, que o cluster é implantado, se um foi selecionado no momento da implementação.|

## <a name="move-clusters"></a>Mover aglomerados

Pode mover um cluster HDInsight para outro grupo de recursos Azure ou outra subscrição.

A partir da página inicial do [cluster:](#homePage)

1. Selecione **Mover-se** a partir do menu superior.
2. Selecione **Mover-se para outro grupo de recursos** ou **mover-se para outra subscrição**.
3. Siga as instruções da nova página.

## <a name="delete-clusters"></a>Eliminar aglomerados

A eliminação de um cluster não elimina a conta de armazenamento predefinido nem as contas de armazenamento ligadas. Pode recriar o cluster utilizando as mesmas contas de armazenamento e as mesmas metástases. Recomendamos a utilização de um novo recipiente Blob predefinido quando recriar o cluster.

A partir da página inicial do [cluster:](#homePage)

1. **Selecione Excluir** do menu superior.
2. Siga as instruções da nova página.

Consulte também [os agrupamentos de pausa/de encerramento](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Adicionar mais contas de armazenamento

Pode adicionar contas adicionais de Armazenamento Azure e contas de Armazenamento de Data Lake Azure após a criação de um cluster. Para obter mais informações, veja [Adicionar mais contas de armazenamento ao HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Aglomerados de escala

A funcionalidade de escala de cluster permite alterar o número de nós de trabalhadores utilizados por um cluster Azure HDInsight, sem ter de recriar o cluster.

Consulte [os clusters de escala HDInsight](./hdinsight-scaling-best-practices.md) para obter informações completas.

## <a name="pauseshut-down-clusters"></a>Agrupamentos de pausa/encerramento

A maioria dos trabalhos de Hadoop são trabalhos de lote que só são executados ocasionalmente. Para a maioria dos aglomerados de Hadoop, há grandes períodos de tempo que o cluster não está sendo usado para o processamento. Com o HDInsight, os seus dados são armazenados no Azure Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado.
Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados.

Há muitas formas de programar o processo:

- Utilizador Azure Data Factory. Consulte [os clusters Apache Hadoop baseados em Linux a pedido em HDInsight utilizando a Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) para criar serviços ligados a pedido hdInsight.
- Use Azure PowerShell.  Consulte [os dados de atraso de voo da Análise](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Utilize a CLI do Azure. Consulte [os clusters Azure HDInsight utilizando o Azure CLI](hdinsight-administer-use-command-line.md).
- Utilize HDInsight .NET SDK. Ver [Submeter empregos apache hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Para obter informações sobre preços, consulte [os preços da HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para eliminar um cluster do Portal, consulte [Eliminar clusters](#delete-clusters)

## <a name="upgrade-clusters"></a>Upgrade de clusters

Consulte [o cluster HdInsight upgrade para uma versão mais recente](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Abra a web UI Apache Ambari

Ambari fornece uma UI de gestão Hadoop intuitiva e fácil de usar, apoiada pelas suas APIs RESTful. O Ambari permite aos administradores do sistema gerir e monitorizar os clusters Hadoop.

A partir da página inicial do [cluster:](#homePage)

1. Selecione **dashboards cluster**.

    ![Menu de cluster HDInsight Apache Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Selecione **Ambari para casa** a partir da nova página.
1. Introduza o nome de utilizador do cluster e a palavra-passe.  O nome de utilizador do cluster predefinido é _administrador_.

Para obter mais informações, consulte [gerir os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Alterar palavras-passe

Um cluster HDInsight pode ter duas contas de utilizador. A conta de utilizador do cluster HDInsight (conta de utilizador HTTP) e a conta de utilizador SSH são criadas durante o processo de criação. Pode utilizar o portal para alterar a palavra-passe da conta de utilizador do cluster e script para alterar a conta de utilizador SSH.

### <a name="change-the-cluster-user-password"></a>Alterar a palavra-passe do utilizador do cluster

> [!NOTE]  
> Alterar a palavra-passe do utilizador do cluster (administrador) pode fazer com que as ações de scriptcorram contra este cluster. Se tiver alguma ação de scripts persistentes que direcione os nós dos trabalhadores, estes scripts podem falhar quando adicionam nós ao cluster através de operações de redimensionação. Para obter mais informações sobre as ações de script, consulte [os clusters HDInsight personalizos usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

A partir da página inicial do [cluster:](#homePage)
1. Selecione **SSH + Cluster login** em **Definições**.
2. Selecione **Reset credencial**.
3. Introduza e confirme nova palavra-passe nas caixas de texto.
4. Selecione **OK**.

A palavra-passe é alterada em todos os nós do cluster.

### <a name="change-the-ssh-user-password-or-public-key"></a>Alterar a palavra-passe do utilizador SSH ou a chave pública

1. Utilizando um editor de texto, guarde o seguinte texto como um ficheiro nomeado **changecredentials.sh**.

    > [!IMPORTANT]  
    > Deve usar um editor que use o LF como o fim da linha. Se o editor usa CRLF, então o script não funciona.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Faça o upload do ficheiro para um local de armazenamento que pode ser acedido a partir de HDInsight utilizando um endereço HTTP ou HTTPS. Por exemplo, uma loja de ficheiros públicos como o OneDrive ou o armazenamento Azure Blob. Guarde o endereço URI (HTTP ou HTTPS) no ficheiro, uma vez que este URI é necessário no passo seguinte.
3. A partir da página inicial do [cluster,](#homePage)selecione **as ações de Script** em **Definições**.
4. A partir da página **de ações** do Script, selecione **Enviar novo**.
5. A partir da página **de ação do script enviar,** insira as seguintes informações:

> [!NOTE]
> As palavras-passe SSH não podem conter os seguintes caracteres:
> ```
> " ' ` / \ < % ~ | $ & ! 
> ```

   | Campo | Valor |
   | --- | --- |
   | Tipo de script | Selecione **- Personalizado** da lista de drop-down.|
   | Nome |"Alterar credenciais de ssh" |
   | URI de guião de bash |O URI para o ficheiro changecredentials.sh |
   | Tipo de nó:(Cabeça, Trabalhador, Nimbus, Supervisor ou Zookeeper.) |✓ para todos os tipos de nós listados |
   | Parâmetros |Insira o nome de utilizador SSH e, em seguida, a nova palavra-passe. Deve haver um espaço entre o nome de utilizador e a senha. |
   | Persista esta ação do guião... |Deixe este campo descontrolado. |

6. Selecione **Criar** para aplicar o script. Assim que o script terminar, poderá ligar-se ao cluster usando SSH com as novas credenciais.

## <a name="find-the-subscription-id"></a>Encontre o ID de subscrição

Cada cluster está ligado a uma subscrição do Azure.  O ID de assinatura Azure é visível a partir da [página inicial](#homePage)do cluster .

## <a name="find-the-resource-group"></a>Encontre o grupo de recursos

No modo Azure Resource Manager, cada cluster HDInsight é criado com um grupo Azure Resource Manager. O grupo Resource Manager é visível a partir da [página inicial](#homePage)do cluster .

## <a name="find-the-storage-accounts"></a>Encontre as contas de armazenamento

Os clusters HDInsight utilizam uma conta de Armazenamento Azure ou um armazenamento do Lago de Dados Azure para armazenar dados. Cada cluster HDInsight pode ter uma conta de armazenamento padrão e uma série de contas de armazenamento ligadas. Para listar as contas de armazenamento, a partir da [página inicial](#homePage) do cluster em **Definições,** selecione **contas de Armazenamento**.

## <a name="monitor-jobs"></a>Monitorizar trabalhos

Consulte [os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Tamanho do cluster

O azulejo do tamanho do **cluster** da página inicial do [cluster](#homePage) exibe o número de núcleos atribuídos a este cluster e como são atribuídos para os nós dentro deste cluster.

> [!IMPORTANT]  
> Para monitorizar os serviços prestados pelo cluster HDInsight, deve utilizar a Ambari Web ou a API Ambari REST. Para obter mais informações sobre a utilização de Ambari, consulte [gerir clusters HDInsight usando Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Ligar a um cluster

- [Use a Colmeia Apache com HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu algumas funções administrativas básicas. Para saber mais, leia os artigos seguintes:

- [Administrar HDInsight usando a azure powershell](hdinsight-administer-use-powershell.md)
- [Administrar HDInsight Usando Azure CLI](hdinsight-administer-use-command-line.md)
- [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Detalhes sobre a utilização da API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Use a Colmeia Apache em HDInsight](hadoop/hdinsight-use-hive.md)
- [Use Apache Sqoop em HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Use funções definidas do utilizador Python (UDF) com a Colmeia Apache e o Porco Apache em HDInsight](hadoop/python-udf-hdinsight.md)
- [Que versão de Apache Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md)
