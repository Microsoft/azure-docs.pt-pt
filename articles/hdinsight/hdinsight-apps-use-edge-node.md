---
title: Usar nós de borda vazios em clusters de Apache Hadoop no HDInsight – Azure
description: Como adicionar um nó de borda vazio a um cluster HDInsight que pode ser usado como um cliente e, em seguida, testar/hospedar seus aplicativos do HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: hrasheed
ms.openlocfilehash: a669c6fe0ffd6ff1c4d4613a91074edfe9fa87e9
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162647"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Usar nós de borda vazios em clusters de Apache Hadoop no HDInsight

Saiba como adicionar um nó de borda vazio a um cluster HDInsight. Um nó de borda vazio é uma máquina virtual Linux com as mesmas ferramentas de cliente instaladas e configuradas como no cabeçalho, mas sem serviços de [Apache Hadoop](https://hadoop.apache.org/) em execução. Você pode usar o nó de borda para acessar o cluster, testar seus aplicativos cliente e hospedar seus aplicativos cliente. 

Você pode adicionar um nó de borda vazio a um cluster HDInsight existente para um novo cluster ao criar o cluster. A adição de um nó de borda vazio é feita usando Azure Resource Manager modelo.  O exemplo a seguir demonstra como isso é feito usando um modelo:

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

Como mostrado no exemplo, você pode opcionalmente chamar uma [ação de script](hdinsight-hadoop-customize-cluster-linux.md) para executar uma configuração adicional, como instalar o [Apache matiz](hdinsight-hadoop-hue-linux.md) no nó de borda. O script de ação de script deve ser acessível publicamente na Web.  Por exemplo, se o script for armazenado no armazenamento do Azure, use contêineres públicos ou BLOBs públicos.

O tamanho da máquina virtual do nó de borda deve atender aos requisitos de tamanho da VM do nó de trabalho do cluster HDInsight. Para os tamanhos de VM do nó de trabalho recomendados, confira [Criar clusters Apache Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Depois de criar um nó de borda, você pode se conectar ao nó de borda usando SSH e executar ferramentas de cliente para acessar o cluster Hadoop no HDInsight.

> [!WARNING]   
> Os componentes personalizados instalados no nó de borda recebem suporte comercialmente razoável da Microsoft. Isso pode resultar na resolução de problemas encontrados. Ou você pode ser chamado de recursos da Comunidade para obter assistência adicional. A seguir estão alguns dos sites mais ativos para obter ajuda da Comunidade:
>
> * [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Se você estiver usando uma tecnologia Apache, poderá encontrar assistência por meio dos sites do projeto do Apache em [https://apache.org](https://apache.org), como o site do [Apache Hadoop](https://hadoop.apache.org/) .

> [!IMPORTANT]
> As imagens do Ubuntu ficam disponíveis para a criação do novo cluster HDInsight dentro de 3 meses após a publicação. A partir de janeiro de 2019, os clusters em execução (incluindo nós de borda) **não** são automaticamente corrigidos. Os clientes devem usar ações de script ou outros mecanismos para aplicar patch em um cluster em execução.  Para obter mais informações, consulte [aplicação de patch do sistema operacional para HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Adicionar um nó de borda a um cluster existente
Nesta seção, você usará um modelo do Resource Manager para adicionar um nó de borda a um cluster HDInsight existente.  O modelo do Resource Manager pode ser encontrado no [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). O modelo do Resource Manager chama uma ação de script localizada em https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. O script não executa nenhuma ação.  É demonstrar a chamada de ação de script de um modelo do Resource Manager.

**Para adicionar um nó de borda vazio a um cluster existente**

1. Clique na imagem a seguir para entrar no Azure e abra o modelo Azure Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>
3. Configure as seguintes propriedades:
   
   * **Assinatura**: selecione uma assinatura do Azure usada para criar o cluster.
   * **Grupo de recursos**: selecione o grupo de recursos usado para o cluster HDInsight existente.
   * **Local**: selecione o local do cluster HDInsight existente.
   * **Nome do cluster**: Insira o nome de um cluster HDInsight existente.
   * **Tamanho do nó de borda**: selecione um dos tamanhos de VM. O tamanho da VM deve atender aos requisitos de tamanho da VM do nó de trabalho. Para os tamanhos de VM do nó de trabalho recomendados, confira [Criar clusters Apache Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Prefixo do nó de borda**: o valor padrão é **novo**.  Usando o valor padrão, o nome do nó de borda é **New-edgenode**.  Você pode personalizar o prefixo no Portal. Você também pode personalizar o nome completo do modelo.

4. Marque **eu concordo com os termos e condições declarados acima**e, em seguida, clique em **comprar** para criar o nó de borda.

>[!IMPORTANT]  
> Certifique-se de selecionar o grupo de recursos do Azure para o cluster HDInsight existente.  Caso contrário, você receberá a mensagem de erro "não é possível executar a operação solicitada no recurso aninhado. O recurso pai '&lt;ClusterName > ' não foi encontrado. "

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adicionar um nó de borda ao criar um cluster
Nesta seção, você usa um modelo do Resource Manager para criar um cluster HDInsight com um nó de borda.  O modelo do Resource Manager pode ser encontrado na [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). O modelo do Resource Manager chama uma ação de script localizada em https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. O script não executa nenhuma ação.  É demonstrar a chamada de ação de script de um modelo do Resource Manager.

**Para criar um cluster HDInsight com um nó de borda**

1. Crie um cluster HDInsight se você ainda não tiver um.  Consulte [introdução ao uso do Hadoop no HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem a seguir para entrar no Azure e abra o modelo Azure Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>
3. Configure as seguintes propriedades:
   
   * **Assinatura**: selecione uma assinatura do Azure usada para criar o cluster.
   * **Grupo de recursos**: Crie um novo grupo de recursos usado para o cluster.
   * **Localização**: Selecione uma localização para o grupo de recursos.
   * **Nome do cluster**: Insira um nome para o novo cluster a ser criado.
   * **Nome de usuário de logon do cluster**: Insira o nome de usuário http do Hadoop.  O nome predefinido é **admin**.
   * **Senha de logon do cluster**: Insira a senha de usuário http do Hadoop.
   * **Nome de usuário SSH**: Insira o nome de usuário SSH. O nome padrão é **sshuser**.
   * **Senha ssh**: Insira a senha de usuário do SSH.
   * **Ação de script de instalação**: Mantenha o valor padrão para passar por este artigo.
     
     Algumas propriedades foram codificadas no modelo: tipo de cluster, contagem de nós de trabalho do cluster, tamanho do nó de borda e nome do nó de borda.
4. Marque **eu concordo com os termos e condições declarados acima**e clique em **comprar** para criar o cluster com o nó de borda.

## <a name="add-multiple-edge-nodes"></a>Adicionar vários nós de borda

Você pode adicionar vários nós de borda a um cluster HDInsight.  A configuração de vários nós de borda só pode ser feita usando modelos de Azure Resource Manager.  Consulte o exemplo de modelo no início deste artigo.  Você precisa atualizar o **targetInstanceCount** para refletir o número de nós de borda que deseja criar.

## <a name="access-an-edge-node"></a>Acessar um nó de borda
O ponto de extremidade SSH do nó de borda é &lt;EdgeNodeName >.&lt;ClusterName >-ssh.azurehdinsight.net:22.  Por exemplo, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

O nó de borda aparece como um aplicativo no portal do Azure.  O portal fornece as informações para acessar o nó de borda usando SSH.

**Para verificar o ponto de extremidade SSH do nó de borda**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Clique em **Aplicações**. Você deverá ver o nó de borda.  O nome padrão é **New-edgenode**.
4. Clique no nó de borda. Você deverá ver o ponto de extremidade SSH.

**Para usar o hive no nó de borda**

1. Utilize o SSH para ligar ao nó de extremidade. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Depois de se conectar ao nó de borda usando o SSH, use o seguinte comando para abrir o console do hive:
   
        hive
3. Execute o seguinte comando para mostrar tabelas Hive no cluster:
   
        show tables;

## <a name="delete-an-edge-node"></a>Excluir um nó de borda
Você pode excluir um nó de borda do portal do Azure.

**Para acessar um nó de borda**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de borda.
3. Clique em **Aplicações**. Você deverá ver uma lista de nós de borda.  
4. Clique com o botão direito do mouse no nó de borda que você deseja excluir e clique em **excluir**.
5. Clique em **Sim** para confirmar.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu como adicionar um nó de borda e como acessar o nó de borda. Para saber mais, confira os seguintes artigos:

* [Instalar aplicações HDInsight](hdinsight-apps-install-applications.md): Saiba como instalar aplicações HDInsight nos clusters.
* [Instalar aplicativos personalizados do hdinsight](hdinsight-apps-install-custom-applications.md): saiba como implantar um aplicativo do hdinsight não publicado no hdinsight.
* [Publicar aplicações do HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar as suas aplicações do HDInsight personalizadas no Azure Marketplace.
* [MSDN: Instalar uma aplicação do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicações do HDInsight.
* [Personalizar clusters do HDInsight baseados em Linux através da Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como utilizar a Ação de Script para instalar outras aplicações.
* [Criar clusters Apache Hadoop baseados em Linux no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.

