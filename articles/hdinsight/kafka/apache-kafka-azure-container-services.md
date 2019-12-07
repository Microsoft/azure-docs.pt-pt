---
title: Usar o serviço kubernetes do Azure com o Kafka no HDInsight
description: Saiba como usar o Kafka no HDInsight de imagens de contêiner hospedadas no AKS (serviço kubernetes do Azure).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: e035c1ff4c8e16fbf40883b54e3153eab9729040
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894288"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Usar o serviço kubernetes do Azure com o Apache Kafka no HDInsight

Saiba como usar o AKS (serviço kubernetes do Azure) com [Apache Kafka](https://kafka.apache.org/) no cluster HDInsight. As etapas neste documento usam um aplicativo node. js hospedado no AKS para verificar a conectividade com o Kafka. Este aplicativo usa o pacote [Kafka](https://www.npmjs.com/package/kafka-node) para se comunicar com o Kafka. Ele usa [Socket.Io](https://socket.io/) para mensagens orientadas a eventos entre o cliente de navegador e o back-end hospedado em AKs.

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de transmissão em fluxo distribuída de código aberto que pode ser utilizada para criar aplicações e pipelines de dados de transmissão em fluxo em tempo real. O serviço kubernetes do Azure gerencia seu ambiente kubernetes hospedado e torna rápido e fácil a implantação de aplicativos em contêineres. Usando uma rede virtual do Azure, você pode conectar os dois serviços.

> [!NOTE]  
> O foco deste documento está nas etapas necessárias para habilitar o serviço kubernetes do Azure a se comunicar com o Kafka no HDInsight. O exemplo em si é apenas um cliente Kafka básico para demonstrar que a configuração funciona.

## <a name="prerequisites"></a>Pré-requisitos

* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Uma subscrição do Azure

Este documento pressupõe que você esteja familiarizado com a criação e o uso dos seguintes serviços do Azure:

* Kafka no HDInsight
* Serviço Kubernetes do Azure
* Redes Virtuais do Azure

Este documento também pressupõe que você tenha percorrido o [tutorial do serviço kubernetes do Azure](../../aks/tutorial-kubernetes-prepare-app.md). Este artigo cria um serviço de contêiner, cria um cluster kubernetes, um registro de contêiner e configura o utilitário `kubectl`.

## <a name="architecture"></a>Arquitetura

### <a name="network-topology"></a>Topologia da rede

O HDInsight e o AKS usam uma rede virtual do Azure como um contêiner para recursos de computação. Para habilitar a comunicação entre o HDInsight e o AKS, você deve habilitar a comunicação entre suas redes. As etapas neste documento usam o emparelhamento de rede virtual para as redes. Outras conexões, como VPN, também devem funcionar. Para obter mais informações sobre emparelhamento, consulte o documento [emparelhamento de rede virtual](../../virtual-network/virtual-network-peering-overview.md) .

O diagrama a seguir ilustra a topologia de rede usada neste documento:

![HDInsight em uma rede virtual, AKS em outra, usando o emparelhamento](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> A resolução de nomes não está habilitada entre as redes emparelhadas, portanto, o endereçamento IP é usado. Por padrão, o Kafka no HDInsight é configurado para retornar nomes de host em vez de endereços IP quando os clientes se conectam. Em vez disso, as etapas neste documento modificam o Kafka para usar o anúncio de IP.

## <a name="create-an-azure-kubernetes-service-aks"></a>Criar um serviço kubernetes do Azure (AKS)

Se você ainda não tiver um cluster AKS, use um dos documentos a seguir para saber como criar um:

* [Implantar um cluster do AKS (serviço kubernetes do Azure)-Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Implantar um cluster do AKS (serviço kubernetes do Azure)-CLI](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> O AKS cria uma rede virtual durante a instalação em um grupo de recursos **adicional** . O grupo de recursos adicional segue a Convenção de nomenclatura de **MC_resourceGroup_AKSclusterName_location**.  
> Essa rede é emparelhada com aquela criada para o HDInsight na próxima seção.

## <a name="configure-virtual-network-peering"></a>Configurar o emparelhamento de rede virtual

### <a name="identify-preliminary-information"></a>Identificar informações preliminares

1. No [portal do Azure](https://portal.azure.com), localize o grupo de **recursos** adicional que contém a rede virtual para o cluster AKs.

2. No grupo de recursos, selecione o recurso de __rede virtual__ . Anote o nome para utilização posterior.

3. Em **configurações**, selecione __espaço de endereço__. Observe o espaço de endereço listado.

### <a name="create-virtual-network"></a>Criar a rede virtual

1. Para criar uma rede virtual para o HDInsight, navegue até __+ criar um recurso__ > __rede__ > __rede virtual__.

1. Crie a rede usando as seguintes diretrizes para determinadas propriedades:

    |Propriedade | Valor |
    |---|---|
    |Espaço de endereços|Você deve usar um espaço de endereço que não se sobreponha ao usado pela rede de cluster AKS.|
    |Localização|Use o mesmo __local__ para a rede virtual que você usou para o cluster AKs.|

1. Aguarde até que a rede virtual tenha sido criada antes de ir para a próxima etapa.

### <a name="configure-peering"></a>Configurar o peering

1. Para configurar o emparelhamento entre a rede HDInsight e a rede de cluster AKS, selecione a rede virtual e, em seguida, selecione __emparelhamentos__.

1. Selecione __+ Adicionar__ e use os seguintes valores para preencher o formulário:

    |Propriedade |Valor |
    |---|---|
    |Nome do emparelhamento de \<este > de VN para a rede virtual remota|Insira um nome exclusivo para essa configuração de emparelhamento.|
    |Rede virtual|Selecione a rede virtual para o **cluster AKs**.|
    |Nome do emparelhamento de \<AKS VN > para \<este VN >|Introduza um nome exclusivo.|

    Deixe todos os outros campos no valor padrão e, em seguida, selecione __OK__ para configurar o emparelhamento.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Criar Apache Kafka cluster no HDInsight

Ao criar o Kafka no cluster HDInsight, você deve ingressar na rede virtual criada anteriormente para o HDInsight. Para obter mais informações sobre como criar um cluster Kafka, consulte o documento [criar um Apache Kafka cluster](apache-kafka-get-started.md) .

## <a name="configure-apache-kafka-ip-advertising"></a>Configurar o anúncio de IP Apache Kafka

Use as etapas a seguir para configurar o Kafka para anunciar endereços IP em vez de nomes de domínio:

1. Num browser, aceda a `https://CLUSTERNAME.azurehdinsight.net`. Substitua CLUSTERname pelo nome do Kafka no cluster HDInsight.

    Quando solicitado, use o nome de usuário HTTPS e a senha para o cluster. A interface do usuário da Web do amAmbari para o cluster é exibida.

2. Para exibir informações sobre o Kafka, selecione __Kafka__ na lista à esquerda.

    ![Lista de serviços com Kafka realçado](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Para exibir a configuração do Kafka __, selecione configurações__ no meio superior.

    ![Configuração de serviços Apache Ambari](./media/apache-kafka-azure-container-services/select-kafka-config1.png)

4. Para localizar a configuração __Kafka-env__ , insira `kafka-env` no campo __filtro__ no canto superior direito.

    ![Configuração do Kafka, para Kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Para configurar o Kafka para anunciar endereços IP, adicione o seguinte texto à parte inferior do campo __Kafka-env-template__ :

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar a interface que o Kafka escuta, digite `listeners` no campo __filtro__ no canto superior direito.

7. Para configurar o Kafka para escutar em todas as interfaces de rede, altere o valor no campo __ouvintes__ para `PLAINTEXT://0.0.0.0:9092`.

8. Para salvar as alterações de configuração, use o botão __salvar__ . Insira uma mensagem de texto que descreva as alterações. Selecione __OK__ depois que as alterações tiverem sido salvas.

    ![Salvar configuração do Apache Ambari](./media/apache-kafka-azure-container-services/save-configuration-button.png)

9. Para evitar erros ao reiniciar o Kafka, use o botão __ações de serviço__ e selecione __ativar o modo de manutenção__. Selecione OK para concluir esta operação.

    ![Ações de serviço, com ativar manutenção realçada](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Para reiniciar o Kafka, use o botão __reiniciar__ e selecione __reiniciar todos os afetados__. Confirme a reinicialização e use o botão __OK__ depois que a operação for concluída.

    ![Botão reiniciar com reiniciar todos os afetados realçado](./media/apache-kafka-azure-container-services/restart-required-button.png)

11. Para desabilitar o modo de manutenção, use o botão __ações de serviço__ e selecione desligar o __modo de manutenção__. Selecione **OK** para concluir esta operação.

## <a name="test-the-configuration"></a>Testar a configuração

Neste ponto, o Kafka e o serviço kubernetes do Azure estão em comunicação por meio das redes virtuais emparelhadas. Para testar essa conexão, use as seguintes etapas:

1. Crie um tópico Kafka que é usado pelo aplicativo de teste. Para obter informações sobre como criar tópicos do Kafka, consulte o documento [criar um Apache Kafka cluster](apache-kafka-get-started.md) .

2. Baixe o aplicativo de exemplo do [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test).

3. Edite o arquivo de `index.js` e altere as seguintes linhas:

    * `var topic = 'mytopic'`: substitua `mytopic` pelo nome do tópico Kafka usado por este aplicativo.
    * `var brokerHost = '176.16.0.13:9092`: substitua `176.16.0.13` pelo endereço IP interno de um dos hosts do agente para o cluster.

        Para localizar o endereço IP interno dos hosts do agente (nós) no cluster, consulte o documento da [API REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) . Escolha o endereço IP de uma das entradas em que o nome de domínio começa com `wn`.

4. Em uma linha de comando no diretório `src`, instale dependências e use o Docker para criar uma imagem para implantação:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Os pacotes exigidos por esse aplicativo são verificados no repositório, portanto, você não precisa usar o utilitário `npm` para instalá-los.

5. Faça logon no ACR (registro de contêiner do Azure) e localize o nome do loginServer:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Se você não souber o nome do registro de contêiner do Azure ou não estiver familiarizado com o uso do CLI do Azure para trabalhar com o serviço kubernetes do Azure, consulte os [tutoriais do AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Marque a imagem de `kafka-aks-test` local com o loginServer de seu ACR. Além disso, adicione `:v1` ao final para indicar a versão da imagem:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Envie a imagem por push para o registro:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Essa operação leva vários minutos para ser concluída.

8. Edite o arquivo de manifesto kubernetes (`kafka-aks-test.yaml`) e substitua `microsoft` pelo nome de loginServer do ACR recuperado na etapa 4.

9. Use o seguinte comando para implantar as configurações do aplicativo do manifesto:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Use o seguinte comando para observar a `EXTERNAL-IP` do aplicativo:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Quando um endereço IP externo for atribuído, use __Ctrl + C__ para sair da inspeção

11. Abra um navegador da Web e insira o endereço IP externo para o serviço. Você chegará a uma página semelhante à imagem a seguir:

    ![Imagem da página da Web do Apache Kafka Test](./media/apache-kafka-azure-container-services/test-web-page-image1.png)

12. Insira o texto no campo e, em seguida, selecione o botão __Enviar__ . Os dados são enviados para Kafka. Em seguida, o consumidor Kafka no aplicativo lê a mensagem e a adiciona à seção __mensagens da Kafka__ .

    > [!WARNING]  
    > Você pode receber várias cópias de uma mensagem. Esse problema geralmente ocorre quando você atualiza o navegador após a conexão ou abre várias conexões de navegador para o aplicativo.

## <a name="next-steps"></a>Passos seguintes

Utilize as seguintes ligações para saber como utilizar o Apache Kafka no HDInsight:

* [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md)

* [Use MirrorMaker para criar uma réplica de Apache Kafka no HDInsight](apache-kafka-mirroring.md)

* [Usar Apache Storm com Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Usar Apache Spark com Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Conectar-se a Apache Kafka por meio de uma rede virtual do Azure](apache-kafka-connect-vpn-gateway.md)
