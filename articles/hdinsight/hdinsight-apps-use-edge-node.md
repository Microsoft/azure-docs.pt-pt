---
title: Use nós de borda vazia em clusters Apache Hadoop em Azure HDInsight
description: Como adicionar um nó de borda vazia a um cluster HDInsight. Usado como cliente, e depois testar, ou hospedar as suas aplicações HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: f6dea00bf3b3e8a58f42da8fd8ad59ccec2dea72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537802"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Use nós de borda vazia em clusters Apache Hadoop em HDInsight

Aprenda a adicionar um nó de borda vazia a um cluster HDInsight. Um nó de borda vazia é uma máquina virtual Linux com as mesmas ferramentas de cliente instaladas e configuradas como nos cabeçanos. Mas sem serviços [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) a funcionar. Pode utilizar o nó de borda para aceder ao cluster, testar as aplicações do seu cliente e hospedar as aplicações do seu cliente.

Pode adicionar um nó de borda vazia a um cluster HDInsight existente, a um novo cluster quando criar o cluster. A adição de um nó de borda vazia é feita usando o modelo do Gestor de Recursos Azure.  A amostra seguinte demonstra como é feito usando um modelo:

```json
"resources": [
    {
        "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
        "type": "Microsoft.HDInsight/clusters/applications",
        "apiVersion": "2015-03-01-preview",
        "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
        "properties": {
            "marketPlaceIdentifier": "EmptyNode",
            "computeProfile": {
                "roles": [{
                    "name": "edgenode",
                    "targetInstanceCount": 1,
                    "hardwareProfile": {
                        "vmSize": "{}"
                    }
                }]
            },
            "installScriptActions": [{
                "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                "uri": "[parameters('installScriptAction')]",
                "roles": ["edgenode"]
            }],
            "uninstallScriptActions": [],
            "httpsEndpoints": [],
            "applicationType": "CustomApplication"
        }
    }
],
```

Como mostrado na amostra, pode opcionalmente chamar uma ação de [script](hdinsight-hadoop-customize-cluster-linux.md) para fazer configuração adicional. Como instalar [o Apache Hue](hdinsight-hadoop-hue-linux.md) no nó de borda. O script de ação do guião deve ser acessível ao público na web.  Por exemplo, se o script estiver armazenado no Armazenamento Azure, utilize recipientes públicos ou bolhas públicas.

O tamanho da máquina virtual do nó de borda deve satisfazer os requisitos de tamanho vm do cluster do cluster HDInsight. Para os tamanhos vm do nó de trabalho recomendados, consulte [Os clusters De Hadoop Apache em HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type).

Depois de criar um nó de borda, pode ligar-se ao nó de borda usando SSH e executar ferramentas de cliente para aceder ao cluster Hadoop no HDInsight.

> [!WARNING]
> Os componentes personalizados que são instalados no nó de borda recebem suporte comercialmente razoável da Microsoft. Isto pode resultar na resolução de problemas que encontra. Ou, pode ser encaminhado para recursos comunitários para mais assistência. Seguem-se alguns dos locais mais ativos para obter ajuda da comunidade:
>
> * [Fórum MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Se estiver a usar uma tecnologia Apache, poderá encontrar assistência através [https://apache.org](https://apache.org)dos sites do projeto Apache, como o site [Apache Hadoop.](https://hadoop.apache.org/)

> [!IMPORTANT]
> As imagens Ubuntu ficam disponíveis para a nova criação de cluster HDInsight no prazo de 3 meses após a publicação. A partir de janeiro de 2019, os clusters de corrida (incluindo os nós de borda) **não** são auto-remendados. Os clientes devem usar ações de script ou outros mecanismos para remendar um cluster de execução.  Para mais informações, consulte [o oss opatching para HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Adicione um nó de borda a um cluster existente

Nesta secção, você usa um modelo de Gestor de Recursos para adicionar um nó de borda a um cluster HDInsight existente.  O modelo de Gestor de Recursos pode ser encontrado no [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). O modelo do Gestor de https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.shRecursos chama uma ação de script localizada em . O guião não faz nenhuma ação.  É para demonstrar chamar a ação do guião a partir de um modelo de Gestor de Recursos.

1. Selecione a seguinte imagem para iniciar sessão no Azure e abra o modelo do Gestor de Recursos Azure no portal Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Configure as seguintes propriedades:

    |Propriedade |Descrição |
    |---|---|
    |Subscrição|Selecione uma subscrição Azure utilizada para criar o cluster.|
    |Grupo de recursos|Selecione o grupo de recursos utilizado para o cluster HDInsight existente.|
    |Localização|Selecione a localização do cluster HDInsight existente.|
    |Nome do Cluster|Introduza o nome de um cluster HDInsight existente.|

1. Verifique **se concordo com os termos e condições acima indicados,** e depois selecione **Comprar** para criar o nó de borda.

> [!IMPORTANT]  
> Certifique-se de selecionar o grupo de recursos Azure para o cluster HDInsight existente.  Caso contrário, recebe a mensagem de erro "Não pode executar a operação solicitada em recursos aninhados. Recurso-mãe&lt;' ClusterName>' não encontrado."

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adicione um nó de borda ao criar um cluster

Nesta secção, você usa um modelo de Gestor de Recursos para criar cluster HDInsight com um nó de borda.  O modelo de Gestor de Recursos pode ser encontrado na galeria de [modelos de quickstart Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). O modelo do Gestor de https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.shRecursos chama uma ação de script localizada em . O guião não faz nenhuma ação.  É para demonstrar chamar a ação do guião a partir de um modelo de Gestor de Recursos.

1. Crie um cluster HDInsight se ainda não tiver um.  Ver [Get começou a usar Hadoop em HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

1. Selecione a seguinte imagem para iniciar sessão no Azure e abra o modelo do Gestor de Recursos Azure no portal Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Configure as seguintes propriedades:

    |Propriedade |Descrição |
    |---|---|
    |Subscrição|Selecione uma subscrição Azure utilizada para criar o cluster.|
    |Grupo de recursos|Crie um novo grupo de recursos usado para o cluster.|
    |Localização|Selecione uma localização para o grupo de recursos.|
    |Nome do Cluster|Introduza um nome para o novo cluster criar.|
    |Nome de Utilizador de Início de Sessão do Cluster|Introduza o nome de utilizador Hadoop HTTP.  O nome predefinido é **admin**.|
    |Palavra-passe de Início de Sessão do Cluster|Introduza a senha de utilizador hadoop HTTP.|
    |Nome do utilizador SSH|Introduza o nome de utilizador SSH. O nome padrão é **sshuser**.|
    |Palavra-passe ssh|Introduza a palavra-passe do utilizador SSH.|
    |Instalar ação de script|Mantenha o valor padrão para passar por este artigo.|

    Algumas propriedades foram codificadas no modelo: Tipo de cluster, contagem de nó de trabalhador cluster, tamanho do nó edge e nome do nó edge.

1. Verifique **se concordo com os termos e condições acima indicados,** e, em seguida, selecione **Comprar** para criar o cluster com o nó de borda.

## <a name="add-multiple-edge-nodes"></a>Adicione vários nódeos de borda

Pode adicionar vários nós de borda a um cluster HDInsight.  A configuração dos nódos múltiplos de borda só pode ser feita usando modelos de gestor de recursos Azure.  Veja a amostra do modelo no início deste artigo.  Atualize o **targetInstanceCount** para refletir o número de nós de borda que gostaria de criar.

## <a name="access-an-edge-node"></a>Aceda a um nó de borda

O ponto final do nó &lt;de borda é EdgeNodeName>. &lt;Nome de cluster>-ssh.azurehdinsight.net:22.  Por exemplo, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

O nó de borda aparece como uma aplicação no portal Azure.  O portal dá-lhe a informação para aceder ao nó de borda utilizando SSH.

**Para verificar o ponto final do nó de borda SSH**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Selecione **Aplicações**. Verá o nó da borda.  O nome predefinido é **new-edgenode**.
4. Selecione o nó de borda. Verá o ponto final do SSH.

**Para usar a Colmeia no nó de borda**

1. Utilize o SSH para ligar ao nó de extremidade. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Depois de ter ligado ao nó de borda utilizando SSH, utilize o seguinte comando para abrir a consola da Colmeia:

        hive

3. Executar o seguinte comando para mostrar as mesas da Colmeia no cluster:

        show tables;

## <a name="delete-an-edge-node"></a>Apagar um nó de borda

Pode apagar um nó de borda do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Selecione **Aplicações**. Verá uma lista de nódosos de borda.  
4. Clique no nó de borda que pretende eliminar e, em seguida, **selecione Delete**.
5. Selecione **Sim** para confirmar.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a adicionar um nó de borda e como aceder ao nó de borda. Para saber mais, consulte os seguintes artigos:

* [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar aplicações HDInsight nos clusters.
* [Instale aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar uma aplicação HDInsight não publicada para o HDInsight.
* [Publicar aplicações do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar as suas aplicações do HDInsight personalizadas no Azure Marketplace.
* [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicações do HDInsight.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Crie clusters Apache Hadoop baseados em Linux no HDInsight utilizando modelos](hdinsight-hadoop-create-linux-clusters-arm-templates.md)de Gestor de Recursos: aprenda a chamar modelos de Gestor de Recursos para criar clusters HDInsight.
