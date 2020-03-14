---
title: Gerir os clusters Apache Hadoop em HDInsight usando o portal Azure
description: Aprenda a criar e gerir os clusters Azure HDInsight utilizando o portal Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/12/2020
ms.openlocfilehash: b9d923b3272f9d8b3da39d7cdb771a766eee4eab
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272737"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gerir os clusters Apache Hadoop em HDInsight utilizando o portal Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Utilizando o [portal Azure,](https://portal.azure.com)pode gerir os clusters [Apache Hadoop](https://hadoop.apache.org/) em Azure HDInsight. Utilize o seletor de separadores acima para obter informações sobre a gestão dos clusters Hadoop no HDInsight utilizando outras ferramentas.

## <a name="prerequisites"></a>Pré-requisitos

Um aglomerado Apache Hadoop existente no HDInsight.  Consulte [a Create Linux clusters em HDInsight utilizando o portal Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Introdução

Inicie sessão em [https://portal.azure.com](https://portal.azure.com).

## <a name="showClusters"></a>Lista e mostrar aglomerados

A página de **clusters HDInsight** listará os seus clusters existentes.  Do portal:
1. Selecione **todos os serviços** do menu esquerdo.
2. Selecione **clusters HDInsight** em **ANALYTICS**.

## <a name="homePage"></a>Página inicial do cluster

Selecione o nome do cluster na página de [**clusters HDInsight.** ](#showClusters)  Isto abrirá a visão **geral,** que se parece com a seguinte imagem:

![Essenciais essenciais do cluster HDInsight do portal Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Menu superior:**  

| Item| Descrição |
|---|---|
|Mover|Move o cluster para outro grupo de recursos ou para outra subscrição.|
|Eliminar|Elimina o cluster. |
|Atualizar|Refresca a vista.|

**Menu esquerdo:**  

  - **Menu superior esquerdo**

    | Item| Descrição |
    |---|---|
    |Descrição geral|Fornece informações gerais para o seu cluster.|
    |Registo de atividades|Mostrar e consultar registos de atividade.|
    |Controlo de acesso (IAM)|Use tarefas de papel.  Consulte as atribuições de [funções de utilização para gerir o acesso aos seus recursos de subscrição Do Azure.](../role-based-access-control/role-assignments-portal.md)|
    |Etiquetas|Permite-lhe definir pares chave/valor para definir uma taxonomia personalizada dos seus serviços na nuvem. Por exemplo, pode criar um **projeto**com nome chave, e depois usar um valor comum para todos os serviços associados a um projeto específico.|
    |Diagnosticar e resolver problemas|Mostrar informações de resolução de problemas.|
    |Início Rápido|Exibe informações que o ajudam a começar a usar o HDInsight.|
    |Ferramentas|Ajuda a informações sobre ferramentas relacionadas com o HDInsight.|

  - **Menu de definições**  

    | Item| Descrição |
    |---|---|
    |Tamanho do cluster|Verifique, aumente e diminua o número de nós de trabalhadores do cluster. Ver [clusters de escala](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Limites de quota|Exiba os núcleos utilizados e disponíveis para a sua subscrição.|
    |SSH + Login cluster|Mostra as instruções de ligação ao cluster utilizando a ligação Secure Shell (SSH). Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Armazenamento do Data Lake Ger1|Configure o acesso data lake storage Gen1.  Ver [Quickstart: Configurar clusters em HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).|
    |Contas de armazenamento|Veja as contas de armazenamento e as chaves. As contas de armazenamento são configuradas durante o processo de criação do cluster.|
    |Aplicações|Adicionar/remover aplicações HDInsight.  Consulte a instalação de [aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).|
    |Ações de script|Executa guiões bash no aglomerado. Consulte os [clusters HDInsight baseados em Linux utilizando](hdinsight-hadoop-customize-cluster-linux.md)a Ação do Script .|
    |Metalojas externas|Veja as metastores [Apache Hive](https://hive.apache.org/) e [Apache Oozie.](https://oozie.apache.org/) As metalojas só podem ser configuradas durante o processo de criação do cluster.|
    |Parceiro HDInsight|Adicione/remova o atual Parceiro HDInsight.|
    |Propriedades|Veja as propriedades do [cluster](#properties).|
    |Bloqueios|Adicione um bloqueio para evitar que o cluster seja modificado ou eliminado.|
    |Exportar modelo|Exiba e exporte o modelo do Gestor de Recursos Azure para o cluster. Atualmente, só pode exportar a conta de armazenamento azure dependente. Consulte os [clusters Apache Hadoop baseados em Linux no HDInsight utilizando modelos](hdinsight-hadoop-create-linux-clusters-arm-templates.md)de Gestor de Recursos Azure .|

  - **Menu de monitorização**

    | Item| Descrição |
    |---|---|
    |Alertas|Gerir os alertas e ações.|
    |Métricas|Monitorize as métricas do cluster nos registos do Monitor Azure.|
    |Definições de diagnóstico|Definições sobre onde armazenar as métricas de diagnóstico.|
    |Azure Monitor|Monitorize o seu cluster no Monitor Azure.|

  - **Menu de suporte + resolução de problemas**

    | Item| Descrição |
    |---|---|
    |Estado de funcionamento de recursos|Consulte a [visão geral da saúde dos recursos azure.](../service-health/resource-health-overview.md)|
    |Novo pedido de suporte|Permite-lhe criar um bilhete de suporte com suporte à Microsoft.|

## <a name="properties"></a>Propriedades cluster

A partir da página inicial do [cluster,](#homePage)em **Definições** selecione **Propriedades**.

|Item | Descrição |
|---|---|
|NOME DE ANFITRIÃO|Nome do aglomerado.|
|CLUSTER URL|O URL para a interface web ambari.|
|Ponto Final Privado|O ponto final privado para o aglomerado.|
|Concha segura (SSH)|O nome de utilizador e o nome do anfitrião a utilizar no acesso ao cluster através do SSH.|
|ESTADO|Um dos: Abortado, Aceite, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operacional, Running, Error, Deleting, Eliminado, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, Redimensionado ou ClusterCustomization.|
|REGIÃO|Localização azul. Para obter uma lista de localizações suportadas do Azure, consulte a caixa de lista seletiva **da Região** nos [preços HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|DATA CRIADA|A data em que o aglomerado foi implantado.|
|SISTEMA OPERATIVO|Ou **Windows** ou **Linux.**|
|TIPO|Hadoop, HBase, Storm, Spark.|
|Versão|Ver [ver versões HDInsight](hdinsight-component-versioning.md).|
|Versão TLS mínima|A versão TLS.|
|SUBSCRIÇÃO|Nome de subscrição.|
|FONTE DE DADOS PADRÃO|O sistema de ficheiros de cluster padrão.|
|Tamanhos dos nódosos operários|O tamanho vm selecionado dos nódosos operários.|
|Tamanho do nó da cabeça|O tamanho vm selecionado dos nóóis da cabeça.|
|Rede virtual|O nome da Rede Virtual, que o cluster é implantado, se um foi selecionado no momento da implantação.|

## <a name="move-clusters"></a>Mover aglomerados

Pode mover um cluster HDInsight para outro grupo de recursos Azure ou outra subscrição.

A partir da página inicial do [cluster:](#homePage)

1. Selecione **Mover-se** a partir do menu superior.
2. Selecione **Mover para outro grupo de recursos** ou mudar para outra **subscrição**.
3. Siga as instruções da nova página.

## <a name="delete-clusters"></a>Eliminar aglomerados

A eliminação de um cluster não elimina a conta de armazenamento predefinida nem as contas de armazenamento ligadas. Pode recriar o cluster utilizando as mesmas contas de armazenamento e as mesmas metalojas. Recomendamos a utilização de um novo recipiente Blob padrão quando recriar o cluster.

A partir da página inicial do [cluster:](#homePage)

1. **Selecione Eliminar** a partir do menu superior.
2. Siga as instruções da nova página.

Ver também [grupos de pausa/desligamento](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Adicionar mais contas de armazenamento

Pode adicionar contas adicionais de Armazenamento Azure e contas de Armazenamento de Lagos De Dados Azure após a criação de um cluster. Para obter mais informações, veja [Adicionar mais contas de armazenamento ao HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Aglomerados de escala

A função de dimensionamento do cluster permite alterar o número de nós de trabalhador usados por um cluster Azure HDInsight, sem ter de recriar o cluster.

Consulte os [clusters Scale HDInsight](./hdinsight-scaling-best-practices.md) para obter informações completas.

## <a name="pauseshut-down-clusters"></a>Pausa/desligar aglomerados

A maioria dos empregos de Hadoop são trabalhos de lote que só são executados ocasionalmente. Para a maioria dos aglomerados hadoop, há grandes períodos de tempo que o cluster não está sendo usado para processamento. Com o HDInsight, os seus dados são armazenados no Armazenamento Azure, para que possa eliminar com segurança um cluster quando não estiver a ser utilizado.
Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar clusters quando não estão em uso.

Há muitas maneiras de programar o processo:

- User Azure Data Factory. Consulte os [clusters Apache Hadoop baseados em Linux a pedido no HDInsight utilizando](hdinsight-hadoop-create-linux-clusters-adf.md) a Azure Data Factory para criar serviços ligados a pedido do HDInsight.
- Utilize o Azure PowerShell.  Consulte [os dados do atraso de voo da Análise.](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
- Utilize o Azure CLI. Ver [Gerir os clusters Azure HDInsight utilizando o Azure CLI](hdinsight-administer-use-command-line.md).
- Utilize hdinsight .NET SDK. Ver [Submeter empregos apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Para obter informações sobre preços, consulte [os preços hDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para eliminar um cluster do Portal, consulte [Eliminar aglomerados](#delete-clusters)

## <a name="upgrade-clusters"></a>Agrupamentos de upgrade

Consulte o [cluster Upgrade HDInsight para uma versão mais recente](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Abra a Web UI apache Ambari

A Ambari fornece uma web de gestão hadoop intuitiva e fácil de usar, apoiada pelas suas APIs RESTful. A Ambari permite que os administradores do sistema gerem e monitorizem os clusters Hadoop.

A partir da página inicial do [cluster:](#homePage)

1. Selecione **dashboards cluster**.

    ![Menu de cluster HDInsight Apache Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. **Selecione ambari casa** a partir da nova página.
1. Introduza o nome de utilizador do cluster e a palavra-passe.  O nome de utilizador do cluster predefinido é _administrador_.

Para obter mais informações, consulte [Gerir os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Alterar palavras-passe

Um cluster HDInsight pode ter duas contas de utilizador. A conta de utilizador do cluster HDInsight (conta de utilizador HTTP) e a conta de utilizador SSH são criadas durante o processo de criação. Pode utilizar o portal para alterar a palavra-passe da conta de utilizador do cluster e as ações de script para alterar a conta de utilizador SSH.

### <a name="change-the-cluster-user-password"></a>Alterar a palavra-passe do utilizador do cluster

> [!NOTE]  
> Alterar a palavra-passe do utilizador do cluster (administrador) pode fazer com que as ações de script saem contra este cluster. Se tiver alguma ação de script persistente que se direcione os nós dos trabalhadores, estes scripts podem falhar quando adicionas nós ao cluster através de operações de redimensionação. Para obter mais informações sobre as ações do script, consulte [Customize HDInsight clusters usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

A partir da página inicial do [cluster:](#homePage)
1. Selecione **sSH + Cluster em** **definições**.
2. Selecione **Reset credencial**.
3. Introduza e confirme nova senha nas caixas de texto.
4. Selecione **OK**.

A palavra-passe é alterada em todos os nós do cluster.

### <a name="change-the-ssh-user-password"></a>Alterar a palavra-passe do utilizador SSH

1. Utilizando um editor de texto, guarde o seguinte texto como ficheiro chamado **changepassword.sh**.

    > [!IMPORTANT]  
    > Deve usar um editor que use LF como o fim da linha. Se o editor usa CRLF, então o guião não funciona.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Faça o upload do ficheiro para um local de armazenamento que possa ser acedido a partir do HDInsight utilizando um endereço HTTP ou HTTPS. Por exemplo, uma loja de ficheiros pública como o armazenamento OneDrive ou Azure Blob. Guarde o URI (endereço HTTP ou HTTPS) no ficheiro, uma vez que este URI é necessário no passo seguinte.
3. A partir da página inicial do [cluster,](#homePage)selecione **as ações do Script** em **Definições**.
4. A partir da página de ações do **Script,** selecione **Enviar nova**.
5. A partir da página de ação do **script Enviar,** introduza as seguintes informações:

   | Campo | Valor |
   | --- | --- |
   | Tipo de script | Selecione **- Personalizado** da lista de lançamentos.|
   | Nome |"Alterar a senha ssh" |
   | Roteiro de bash URI |O URI para o ficheiro changepassword.sh |
   | Tipo de nó): (Cabeça, Trabalhador, Nimbus, Supervisor ou Zookeeper.) |✓ para todos os tipos de nó listados |
   | Parâmetros |Introduza o nome de utilizador SSH e, em seguida, a nova palavra-passe. Deve haver um espaço entre o nome do utilizador e a palavra-passe. |
   | Persista esta ação do guião... |Deixe este campo descontrolado. |

6. Selecione **Criar** para aplicar o script. Uma vez terminada a script, é possível ligar-se ao cluster utilizando o SSH com a nova senha.

## <a name="find-the-subscription-id"></a>Encontre o ID de subscrição

Cada cluster está ligado a uma assinatura Azure.  O ID de subscrição Azure é visível a partir da [página inicial](#homePage)do cluster .

## <a name="find-the-resource-group"></a>Encontre o grupo de recursos

No modo Azure Resource Manager, cada cluster HDInsight é criado com um grupo de Gestor de Recursos Azure. O grupo Gestor de Recursos é visível a partir da [página inicial](#homePage)do cluster .

## <a name="find-the-storage-accounts"></a>Encontre as contas de armazenamento

Os clusters HDInsight utilizam uma conta de Armazenamento Azure ou armazenamento de lagos de dados Azure para armazenar dados. Cada cluster HDInsight pode ter uma conta de armazenamento predefinida e uma série de contas de armazenamento ligadas. Para listar as contas de armazenamento, a partir da [página inicial](#homePage) do cluster em **Definições,** selecione **contas de Armazenamento**.

## <a name="monitor-jobs"></a>Monitorizar trabalhos

Consulte [os clusters HDInsight , utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Tamanho do cluster

O azulejo do tamanho do **Cluster** da [página inicial](#homePage) do cluster mostra o número de núcleos atribuídos a este cluster e como são alocados para os nós dentro deste cluster.

> [!IMPORTANT]  
> Para monitorizar os serviços fornecidos pelo cluster HDInsight, deve utilizar a Ambari Web ou a Ambari REST API. Para obter mais informações sobre a utilização de Ambari, consulte [Gerir os clusters HDInsight usando Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Ligar a um cluster

- [Use a Colmeia Apache com HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu algumas funções administrativas básicas. Para saber mais, consulte os seguintes artigos:

- [Administrar hDInsight usando a PowerShell Azure](hdinsight-administer-use-powershell.md)
- [Administrar hDInsight usando o AZURE CLI](hdinsight-administer-use-command-line.md)
- [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Detalhes sobre a utilização da API De REPOUSO Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Use a Colmeia Apache no HDInsight](hadoop/hdinsight-use-hive.md)
- [Use Apache Sqoop em HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Utilize funções definidas pelo utilizador Python (UDF) com hiv apache e porco Apache em HDInsight](hadoop/python-udf-hdinsight.md)
- [Que versão do Apache Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md)
