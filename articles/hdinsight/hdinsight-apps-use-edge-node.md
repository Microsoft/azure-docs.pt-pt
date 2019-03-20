---
title: Utilizar nós de extremidade em branco em clusters do Apache Hadoop no HDInsight - Azure
description: Como adicionar um nó de extremidade vazio para um cluster do HDInsight que pode ser utilizado como um cliente e, em seguida, teste/anfitrião suas aplicações do HDInsight.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: aa9cdbaece2714dd4869268f394e6840fe42edda
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225404"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Utilizar nós de extremidade em branco em clusters do Apache Hadoop no HDInsight

Saiba como adicionar um nó de extremidade vazio para um cluster do HDInsight. Um nó de extremidade vazio é uma máquina virtual do Linux com as mesmas ferramentas de cliente instalado e configurado como nos principais, mas sem qualquer [Apache Hadoop](https://hadoop.apache.org/) serviços em execução. Pode utilizar o nó de extremidade para aceder ao cluster, testar as suas aplicações de cliente e hospedar seus aplicativos de cliente. 

Pode adicionar um nó de extremidade vazio para um cluster do HDInsight existente, para um novo cluster ao criar o cluster. Adicionar um nó de extremidade em branco é feito usando o modelo Azure Resource Manager.  O exemplo seguinte demonstra como isso é feito através de um modelo:

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
                            "vmSize": "Standard_D3"
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

Conforme mostrado no exemplo, pode, opcionalmente, chamar um [ação de script](hdinsight-hadoop-customize-cluster-linux.md) para efetuar configuração adicional, como a instalação [Apache Hue](hdinsight-hadoop-hue-linux.md) no nó de extremidade. O script de ação de script tem de ser acessível publicamente na web.  Por exemplo, se o script é armazenado no armazenamento do Azure, utilize contentores públicos ou blobs públicos.

O tamanho de máquina virtual do nó de extremidade tem de cumprir os requisitos de tamanho de vm de nó do HDInsight cluster trabalho. Para os tamanhos de vm de nó de trabalho recomendada, consulte [Apache Hadoop criar clusters no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Depois de ter criado um nó de extremidade, pode ligar ao nó de extremidade através de SSH e executar as ferramentas de cliente para aceder ao cluster de Hadoop no HDInsight.

> [!WARNING]   
> Componentes personalizados que estão instalados no nó de extremidade recebem suporte comercialmente razoável da Microsoft. Isto pode resultar na resolução de problemas que encontrar. Em alternativa, pode ser direcionado para os recursos da Comunidade para obter mais assistência. Seguem-se alguns dos sites de ativos mais para obter ajuda da Comunidade:
>
> * [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Se estiver a utilizar uma tecnologia do Apache, talvez consiga encontrar assistência através do Apache sites de projeto na [ https://apache.org ](https://apache.org), como a [Apache Hadoop](https://hadoop.apache.org/) site.

> [!IMPORTANT]
> Imagens do Ubuntu tornam-se disponível para criação de cluster do HDInsight novo dentro de 3 meses de ser publicada. A partir de Janeiro de 2019, os clusters em execução (incluindo nós periféricos) estão **não** aplicação de patches automática. Os clientes tem de utilizar as ações de script ou de outros mecanismos para corrigir um cluster em execução.  Para obter mais informações, consulte [aplicação de patches de SO para o HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Adicionar um nó de extremidade a um cluster existente
Nesta secção, vai utilizar um modelo do Resource Manager para adicionar um nó de extremidade a um cluster do HDInsight existente.  O modelo do Resource Manager pode ser encontrado na [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). O modelo do Resource Manager chama uma ação de script localizada em https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. O script não executa qualquer ação.  É demonstrar a chamada de ação de script de um modelo do Resource Manager.

**Para adicionar um nó de extremidade vazio para um cluster existente**

1. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo Azure Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure as seguintes propriedades:
   
   * **Subscrição**: Selecione uma subscrição do Azure utilizada para criar o cluster.
   * **Grupo de recursos**: Selecione o grupo de recursos utilizado para o cluster do HDInsight existente.
   * **Localização**: Selecione a localização do cluster HDInsight existente.
   * **Nome do cluster**: Introduza o nome de um cluster do HDInsight existente.
   * **Tamanho do nó de extremidade**: Selecione um dos tamanhos de VM. O tamanho da vm tem de cumprir os requisitos de tamanho de vm de nó de trabalho. Para os tamanhos de vm de nó de trabalho recomendada, consulte [Apache Hadoop criar clusters no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Prefixo de nó de extremidade**: O valor predefinido é **novo**.  Com o valor predefinido, o nome do nó de extremidade é **novo edgenode**.  Pode personalizar o prefixo do portal. Também pode personalizar o nome completo do modelo.

4. Verifique **concordo com os termos e condições indicados acima**e, em seguida, clique em **Compra** para criar o nó de extremidade.

>[!IMPORTANT]  
> Certifique-se selecionar o grupo de recursos do Azure para o cluster do HDInsight existente.  Caso contrário, obtém a mensagem de erro "não é possível executar a operação pedida no recurso aninhado. Recurso principal "&lt;ClusterName >' não foi encontrado."

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adicionar um nó de extremidade ao criar um cluster
Nesta seção, usar um modelo do Resource Manager para criar o cluster do HDInsight com um nó de extremidade.  O modelo do Resource Manager pode ser encontrado na [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). O modelo do Resource Manager chama uma ação de script localizada em https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. O script não executa qualquer ação.  É demonstrar a chamada de ação de script de um modelo do Resource Manager.

**Para criar um cluster do HDInsight com um nó de extremidade**

1. Se ainda não tiver uma, crie um cluster do HDInsight.  Ver [começar a utilizar o Hadoop no HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo Azure Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure as seguintes propriedades:
   
   * **Subscrição**: Selecione uma subscrição do Azure utilizada para criar o cluster.
   * **Grupo de recursos**: Crie um novo grupo de recursos utilizado para o cluster.
   * **Localização**: Selecione uma localização para o grupo de recursos.
   * **Nome do cluster**: Introduza um nome para o novo cluster criar.
   * **Nome de utilizador de início de sessão do cluster**: Introduza o nome de utilizador HTTP do Hadoop.  O nome predefinido é **admin**.
   * **Palavra-passe de início de sessão do cluster**: Introduza a palavra-passe de utilizador do Hadoop HTTP.
   * **Nome de utilizador SSH**: Introduza o nome de utilizador SSH. O nome predefinido é **sshuser**.
   * **SSH palavra-passe**: Introduza a palavra-passe de utilizador do SSH.
   * **Instalar a ação de Script**: Mantenha o valor predefinido para percorrer neste tutorial.
     
     Algumas propriedades foram codificadas no modelo: Tipo de cluster, contagem de nós de trabalho de Cluster, o tamanho de nó de extremidade e o nome de nó de extremidade.
4. Verifique **concordo com os termos e condições indicados acima**e, em seguida, clique em **Compra** para criar o cluster com o nó de extremidade.

## <a name="add-multiple-edge-nodes"></a>Adicionar vários nós de extremidade

Pode adicionar vários nós de extremidade para um cluster do HDInsight.  A configuração de nós de extremidade vários só pode ser feita utilizando modelos do Azure Resource Manager.  Veja o exemplo de modelo no início deste artigo.  Tem de atualizar o **targetInstanceCount** para refletir o número de nós de extremidade que gostaria de criar.

## <a name="access-an-edge-node"></a>Aceder a um nó de extremidade
O nó de extremidade ssh ponto final está &lt;EdgeNodeName >.&lt; ClusterName >-ssh.azurehdinsight.net:22.  Por exemplo, novo-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Nó de extremidade é apresentada como uma aplicação no portal do Azure.  O portal fornece as informações para aceder ao nó de extremidade através de SSH.

**Para verificar o ponto final SSH do nó de extremidade**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster do HDInsight com um nó de extremidade.
3. Clique em **Aplicações**. Deverá ver o nó de extremidade.  O nome predefinido é **novo edgenode**.
4. Clique no nó de extremidade. Deverá ver o ponto final SSH.

**Para utilizar o Hive no nó de extremidade**

1. Utilize o SSH para ligar ao nó de extremidade. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Depois de ligar ao nó de extremidade através de SSH, utilize o seguinte comando para abrir a consola do Hive:
   
        hive
3. Execute o seguinte comando para mostrar tabelas do Hive no cluster:
   
        show tables;

## <a name="delete-an-edge-node"></a>Eliminar um nó de extremidade
Pode eliminar um nó de extremidade do portal do Azure.

**Para aceder a um nó de extremidade**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster do HDInsight com um nó de extremidade.
3. Clique em **Aplicações**. Deverá ver uma lista de nós de extremidade.  
4. Com o botão direito do nó de extremidade que pretende eliminar e, em seguida, clique em **eliminar**.
5. Clique em **Sim** para confirmar.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu como adicionar um nó de extremidade e como aceder ao nó de extremidade. Para obter mais informações, consulte os artigos seguintes:

* [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar uma aplicação aos seus clusters do HDInsight.
* [Instalar aplicações do HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): Saiba como implementar uma aplicação HDInsight não publicada para o HDInsight.
* [Publicar aplicações HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar aplicações HDInsight personalizadas no Azure Marketplace.
* [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicações do HDInsight.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Criar clusters do Apache Hadoop baseado em Linux no HDInsight com modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Saiba como chamar modelos do Resource Manager para criar clusters do HDInsight.

