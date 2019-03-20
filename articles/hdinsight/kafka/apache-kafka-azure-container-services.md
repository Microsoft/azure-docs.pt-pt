---
title: Utilizar o serviço Kubernetes do Azure com o Kafka no HDInsight
description: Saiba como utilizar o Kafka no HDInsight a partir de imagens de contentor alojadas no Azure Kubernetes Service (AKS).
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: b9b4af353e5bbd36f1f8d7468c38542a0fabda85
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112182"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Utilizar o serviço Kubernetes do Azure com o Apache Kafka no HDInsight

Saiba como utilizar o Azure Kubernetes Service (AKS) com [Apache Kafka](https://kafka.apache.org/) num cluster do HDInsight. Os passos neste documento utilizam uma aplicação node. js alojada no AKS para verificar a conectividade com o Kafka. Esta aplicação utiliza a [kafka-nó](https://www.npmjs.com/package/kafka-node) pacote para comunicar com o Kafka. Ele usa [Socket.io](https://socket.io/) evento orientado a mensagens entre o cliente de navegador e o back-end alojado no AKS.

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de transmissão em fluxo distribuída de código aberto que pode ser utilizada para criar aplicações e pipelines de dados de transmissão em fluxo em tempo real. Serviço Kubernetes do Azure gere o seu ambiente alojado do Kubernetes e torna rápido e fácil de implementar aplicações em contentores. Ao utilizar uma rede Virtual do Azure, pode ligar os dois serviços.

> [!NOTE]  
> O foco deste documento é os passos necessários para ativar o serviço de Kubernetes do Azure comunicar com o Kafka no HDInsight. O exemplo em si é apenas um cliente de Kafka básica para demonstrar que a configuração funciona.

## <a name="prerequisites"></a>Pré-requisitos

* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Uma subscrição do Azure

Este documento parte do princípio de que está familiarizado com a criação e utilizar os serviços do Azure seguintes:

* Kafka no HDInsight
* Azure Kubernetes Service
* Redes Virtuais do Azure

Este documento assume também que tiver percorrido o [tutorial do Azure Kubernetes Service](../../aks/tutorial-kubernetes-prepare-app.md). Este tutorial cria um serviço de contentor, cria um cluster do Kubernetes, um registo de contentores e configura o `kubectl` utilitário.

## <a name="architecture"></a>Arquitetura

### <a name="network-topology"></a>Topologia da rede

O HDInsight e o AKS utilizar uma rede Virtual do Azure como um contêiner para recursos de computação. Para ativar a comunicação entre o HDInsight e o AKS, tem de ativar a comunicação entre as suas redes. Os passos neste documento utilizam o Peering de rede Virtual para as redes. Outras conexões, como VPN, também deverá funcionar. Para obter mais informações sobre o peering, veja a [peering de rede Virtual](../../virtual-network/virtual-network-peering-overview.md) documento.


O diagrama seguinte ilustra a topologia de rede utilizada neste documento:

![HDInsight numa rede virtual, AKS em outro e as redes ligadas a utilização do peering](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> Resolução de nomes não está ativada entre as redes, portanto, é utilizado o endereçamento IP em modo de peering. Por predefinição, o Kafka no HDInsight está configurado para devolver os nomes de anfitrião em vez de endereços IP, quando os clientes ligam. Os passos neste documento modificar Kafka para utilizar o IP em vez disso, a publicidade.

## <a name="create-an-azure-kubernetes-service-aks"></a>Criar um serviço de Kubernetes do Azure (AKS)

Se ainda não tiver um cluster do AKS, utilize um dos seguintes documentos para saber como criar um:

* [Implementar um cluster do Azure Kubernetes Service (AKS) - Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Implementar um cluster do Azure Kubernetes Service (AKS) - CLI](../../aks/kubernetes-walkthrough.md)

> [!NOTE]  
> AKS cria uma rede virtual durante a instalação. Esta rede é configurar o peering criado para HDInsight na próxima seção.

## <a name="configure-virtual-network-peering"></a>Configurar o peering de rede virtual

1. Partir do [portal do Azure](https://portal.azure.com), selecione __grupos de recursos__e, em seguida, localize o grupo de recursos que contém a rede virtual para o seu cluster do AKS. O nome do grupo de recursos é `MC_<resourcegroup>_<akscluster>_<location>`. O `resourcegroup` e `akscluster` entradas são o nome do grupo de recursos que criou o cluster e o nome do cluster. O `location` é a localização que o cluster foi criado.

2. No grupo de recursos, selecione o __rede Virtual__ recursos.

3. Selecione __espaço de endereços__. Tenha em atenção de que o espaço de endereço listado.

4. Para criar uma rede virtual para o HDInsight, selecione __+ criar um recurso__, __redes__e, em seguida __rede Virtual__.

    > [!IMPORTANT]  
    > Ao introduzir os valores para a nova rede virtual, tem de utilizar um espaço de endereços que não se sobreponha daquela utilizada na rede de cluster do AKS.

    Utilizar o mesmo __localização__ para a rede virtual que utilizou para o cluster do AKS.

    Aguarde até que a rede virtual tiver sido criada antes de avançar para o passo seguinte.

5. Para configurar o peering entre a rede de HDInsight e a rede de cluster do AKS, selecione a rede virtual e, em seguida, selecione __Peerings__. Selecione __+ adicionar__ e utilize os seguintes valores para preencher o formulário:

   * __Nome__: Introduza um nome exclusivo para esta configuração de peering.
   * __Rede virtual__: Utilize este campo para selecionar a rede virtual para o **cluster do AKS**.

     Deixe todos os outros campos com o valor predefinido, em seguida, selecione __OK__ para configurar o peering.

6. Para configurar o peering entre a rede de cluster do AKS e a rede de HDInsight, selecione o __rede virtual de cluster do AKS__e, em seguida, selecione __Peerings__. Selecione __+ adicionar__ e utilize os seguintes valores para preencher o formulário:

   * __Nome__: Introduza um nome exclusivo para esta configuração de peering.
   * __Rede virtual__: Utilize este campo para selecionar a rede virtual para o __cluster de HDInsight__.

     Deixe todos os outros campos com o valor predefinido, em seguida, selecione __OK__ para configurar o peering.

## <a name="install-apache-kafka-on-hdinsight"></a>Instalar o Apache Kafka no HDInsight

Ao criar o Kafka no cluster do HDInsight, tem de associar a rede virtual que criou anteriormente para o HDInsight. Para obter mais informações sobre como criar um cluster de Kafka, consulte a [criar um cluster do Apache Kafka](apache-kafka-get-started.md) documento.

> [!IMPORTANT]  
> Quando criar o cluster, tem de utilizar o __definições avançadas__ para associar a rede virtual que criou para o HDInsight.

## <a name="configure-apache-kafka-ip-advertising"></a>Configurar a publicidade de IP do Apache Kafka

Utilize os seguintes passos para configurar o Kafka para anunciar os endereços IP em vez de nomes de domínio:

1. Um browser, aceda à https://CLUSTERNAME.azurehdinsight.net. Substitua __CLUSTERNAME__ com o nome do cluster do Kafka no HDInsight.

    Quando lhe for pedido, utilize o nome de utilizador HTTPS e a palavra-passe para o cluster. A interface do Usuário de Web do Ambari do cluster é apresentado.

2. Para ver informações sobre o Kafka, selecione __Kafka__ da lista à esquerda.

    ![Lista de serviço com o Kafka realçado](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Para ver a configuração do Kafka, selecione __configurações__ da parte superior ao centro.

    ![Links de configurações para o Kafka](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Para localizar os __kafka env__ configuração, introduza `kafka-env` no __filtro__ campo no canto superior direito.

    ![Configuração do Kafka, para env do kafka](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Para configurar o Kafka para anunciar os endereços IP, adicione o seguinte texto para o fim dos __env de kafka-modelo__ campo:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar a interface que escuta Kafka, introduza `listeners` no __filtro__ campo no canto superior direito.

7. Para configurar o Kafka para escutar em todas as interfaces de rede, altere o valor na __serviços de escuta__ campo `PLAINTEXT://0.0.0.0:9092`.

8. Para guardar as alterações de configuração, utilize o __guardar__ botão. Introduza uma mensagem de texto que descreve as alterações. Selecione __OK__ assim que as alterações foram guardadas.

    ![Configuração botão Guardar](./media/apache-kafka-azure-container-services/save-button.png)

9. Para evitar erros quando reiniciar o Kafka, utilize o __ações de serviço__ e selecione __ativar no modo de manutenção__. Selecione OK para concluir esta operação.

    ![Ações de serviço, com ative a manutenção realçada](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Para reiniciar o Kafka, utilize o __reinicie__ e selecione __reiniciar todos os afetados__. Confirme o reinício e, em seguida, utilize o __OK__ botão após a conclusão da operação.

    ![Reinicie o botão ao reiniciar todos os afetados realçado](./media/apache-kafka-azure-container-services/restart-button.png)

11. Para desativar o modo de manutenção, utilize o __ações de serviço__ e selecione __Ativar desativar modo de manutenção__. Selecione **OK** para concluir esta operação.

## <a name="test-the-configuration"></a>Testar a configuração

Neste momento, Kafka e o serviço Kubernetes do Azure estão em comunicação através de redes virtuais em modo de peering. Para testar esta ligação, utilize os seguintes passos:

1. Crie um tópico do Kafka que é utilizado pela aplicação de teste. Para obter informações sobre como criar tópicos de Kafka, consulte a [criar um cluster do Apache Kafka](apache-kafka-get-started.md) documento.

2. Transferir a aplicação de exemplo da [ https://github.com/Blackmist/Kafka-AKS-Test ](https://github.com/Blackmist/Kafka-AKS-Test).

3. Editar o `index.js` de ficheiros e alterar as seguintes linhas:

    * `var topic = 'mytopic'`: Substitua `mytopic` com o nome do tópico Kafka usado por esse aplicativo.
    * `var brokerHost = '176.16.0.13:9092`: Substitua `176.16.0.13` com o endereço IP interno de um dos anfitriões broker para o seu cluster.

        Para obter o endereço IP interno de Mediador (workernodes) de anfitriões no cluster, consulte a [Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) documento. Escolher endereço IP de uma das entradas em que o nome de domínio começa com `wn`.

4. De uma linha de comando no `src` directory, instale as dependências e utilizar o Docker para criar uma imagem para a implementação:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Pacotes necessários por esta aplicação são verificados para o repositório, para que não é necessário utilizar o `npm` utilitário para instalá-los.

5. Inicie sessão para o Azure Container Registry (ACR) e encontrar o nome loginServer:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Se não souber o nome do registo de contentor do Azure, ou caso esteja familiarizada com a utilização da CLI do Azure para trabalhar com o serviço de Kubernetes do Azure, consulte a [tutoriais AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Etiquetar local `kafka-aks-test` imagem com o loginServer do seu ACR. Também adicionar `:v1` ao fim para indicar a versão da imagem:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Envie a imagem para o registo:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Esta operação demora vários minutos a concluir.

8. Edite o ficheiro de manifesto do Kubernetes (`kafka-aks-test.yaml`) e substitua `microsoft` com o nome loginServer do ACR obtidos no passo 4.

9. Utilize o seguinte comando para implementar as definições de aplicação a partir do manifesto:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Utilize o seguinte comando para ver para o `EXTERNAL-IP` do aplicativo:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Assim que for atribuído um endereço IP externo, utilize __CTRL + C__ para sair do watch

11. Abra um browser e introduza o endereço IP externo para o serviço. Chega a uma página semelhante à seguinte imagem:

    ![Imagem da página da web](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Introduza o texto no campo e, em seguida, selecione o __enviar__ botão. Os dados são enviados ao Kafka. Em seguida, o consumidor de Kafka no aplicativo lê a mensagem e adiciona-o para o __mensagens do Kafka__ secção.

    > [!WARNING]  
    > Poderá receber várias cópias de uma mensagem. Esse problema geralmente acontece quando atualizar o browser depois de ligar ou abrir várias ligações de browser para a aplicação.

## <a name="next-steps"></a>Passos Seguintes

Utilize as seguintes ligações para saber como utilizar o Apache Kafka no HDInsight:

* [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md)

* [Utilize o MirrorMaker para criar uma réplica do Apache Kafka no HDInsight](apache-kafka-mirroring.md)

* [Utilizar o Apache Storm com o Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Utilizar o Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Ligar ao Apache Kafka através de uma rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)
