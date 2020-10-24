---
title: Use o serviço Azure Kubernetes com Kafka em HDInsight
description: Saiba como usar Kafka em HDInsight a partir de imagens de contentores hospedadas no Serviço Azure Kubernetes (AKS).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: ab87f181f78158d2ea0dd6575a30e6087600f60c
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485686"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Use o serviço Azure Kubernetes com Apache Kafka em HDInsight

Saiba como utilizar o Serviço Azure Kubernetes (AKS) com [Apache Kafka](https://kafka.apache.org/) no cluster HDInsight. Os passos deste documento utilizam uma aplicação Node.js hospedada na AKS para verificar a conectividade com a Kafka. Esta aplicação utiliza o pacote [kafka-node](https://www.npmjs.com/package/kafka-node) para comunicar com Kafka. Utiliza [Socket.io](https://socket.io/) para mensagens dirigidas a eventos entre o cliente do navegador e o back-end hospedado em AKS.

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de transmissão em fluxo distribuída de código aberto que pode ser utilizada para criar aplicações e pipelines de dados de transmissão em fluxo em tempo real. O Serviço Azure Kubernetes gere o ambiente de Kubernetes hospedado e torna-o rápido e fácil de implementar aplicações contentorizadas. Utilizando uma Rede Virtual Azure, pode ligar os dois serviços.

> [!NOTE]  
> O foco deste documento está nos passos necessários para permitir que o Serviço Azure Kubernetes comunique com a Kafka na HDInsight. O exemplo em si é apenas um cliente básico de Kafka para demonstrar que a configuração funciona.

## <a name="prerequisites"></a>Pré-requisitos

* [CLI do Azure](/cli/azure/install-azure-cli)
* Uma subscrição do Azure

Este documento pressupõe que está familiarizado com a criação e utilização dos seguintes serviços Azure:

* Kafka no HDInsight
* Azure Kubernetes Service
* Redes Virtuais do Azure

Este documento também assume que você andou pelo tutorial do [Serviço Azure Kubernetes.](../../aks/tutorial-kubernetes-prepare-app.md) Este artigo cria um serviço de contentores, cria um cluster Kubernetes, um registo de contentores e configura o `kubectl` utilitário.

## <a name="architecture"></a>Arquitetura

### <a name="network-topology"></a>Topologia da rede

Tanto o HDInsight como o AKS utilizam uma Rede Virtual Azure como recipiente para obter recursos de computação. Para ativar a comunicação entre HDInsight e AKS, deve ativar a comunicação entre as suas redes. Os passos deste documento utilizam a Rede Virtual Espreitando para as redes. Outras ligações, como a VPN, também devem funcionar. Para obter mais informações sobre o seu espremiamento, consulte o documento [de observação da rede Virtual.](../../virtual-network/virtual-network-peering-overview.md)

O diagrama a seguir ilustra a topologia da rede utilizada neste documento:

![HDInsight em uma rede virtual, AKS em outra, usando olhando](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> A resolução de nomes não está ativada entre as redes espreitadas, pelo que é utilizado o endereço IP. Por padrão, a Kafka no HDInsight está configurada para devolver nomes de anfitriões em vez de endereços IP quando os clientes se ligam. Os passos deste documento modificam Kafka para utilizar a publicidade IP.

## <a name="create-an-azure-kubernetes-service-aks"></a>Criar um serviço Azure Kubernetes (AKS)

Se ainda não tiver um cluster AKS, utilize um dos seguintes documentos para aprender a criar um:

* [Implementar um cluster de Serviço Azure Kubernetes (AKS) - Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Implementar um cluster de Serviço Azure Kubernetes (AKS) - CLI](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> A AKS cria uma rede virtual durante a instalação num grupo de recursos **adicionais.** O grupo de recursos adicionais segue a convenção de nomeação de **MC_resourceGroup_AKSclusterName_location.**  
> Esta rede é espreitada para a criada para HDInsight na secção seguinte.

## <a name="configure-virtual-network-peering"></a>Configurar o espreitamento da rede virtual

### <a name="identify-preliminary-information"></a>Identificar informações preliminares

1. A partir do [portal Azure,](https://portal.azure.com)localize o grupo de **Recursos** adicionais que contém a rede virtual para o seu cluster AKS.

2. A partir do grupo de recursos, selecione o recurso __de rede Virtual.__ Anote o nome para utilização posterior.

3. Em **Definições**, selecione __Espaço de endereço__. Note o espaço de endereço listado.

### <a name="create-virtual-network"></a>Criar a rede virtual

1. Para criar uma rede virtual para HDInsight, navegue para __+ Criar uma__rede virtual  >  __de rede de rede de__  >  __Virtual network__recursos.

1. Criar a rede utilizando as seguintes diretrizes para determinadas propriedades:

    |Propriedade | Valor |
    |---|---|
    |Espaço de endereços|Deve utilizar um espaço de endereço que não se sobreponha ao utilizado pela rede de clusters AKS.|
    |Localização|Utilize a mesma __localização__ para a rede virtual que usou para o cluster AKS.|

1. Aguarde até que a rede virtual tenha sido criada antes de passar ao passo seguinte.

### <a name="configure-peering"></a>Configurar o peering

1. Para configurar o espreitamento entre a rede HDInsight e a rede de clusterS AKS, selecione a rede virtual e, em seguida, __selecione Peerings__.

1. __Selecione + Adicione__ e use os seguintes valores para preencher o formulário:

    |Propriedade |Valor |
    |---|---|
    |Nome do que espreitar da \<this VN> rede virtual remota|Insira um nome único para esta configuração de espreitar.|
    |Rede virtual|selecione a rede virtual para o **cluster AKS**.|
    |Nome do que espreitar \<AKS VN> de \<this VN>|Introduza um nome exclusivo.|

    Deixe todos os outros campos pelo valor predefinido e, em seguida, selecione __OK__ para configurar o espreitamento.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Criar cluster Apache Kafka em HDInsight

Ao criar o cluster Kafka no HDInsight, deve juntar-se à rede virtual criada anteriormente para o HDInsight. Para obter mais informações sobre a criação de um cluster Kafka, consulte o [documento de cluster Apache Kafka.](apache-kafka-get-started.md)

## <a name="configure-apache-kafka-ip-advertising"></a>Configurar publicidade IP Apache Kafka

Utilize os seguintes passos para configurar Kafka para anunciar endereços IP em vez de nomes de domínio:

1. Num browser, aceda a `https://CLUSTERNAME.azurehdinsight.net`. Substitua o CLUSTERNAME pelo nome do Kafka no cluster HDInsight.

    Quando solicitado, utilize o nome de utilizador HTTPS e a palavra-passe para o cluster. É apresentado o UI Web Ambari para o cluster.

2. Para ver informações sobre Kafka, selecione __Kafka__ da lista à esquerda.

    ![Lista de serviços com Kafka em destaque](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Para ver a configuração de Kafka, selecione __Configs__ a partir do meio superior.

    ![Configuração dos serviços Apache Ambari](./media/apache-kafka-azure-container-services/select-kafka-config1.png)

4. Para encontrar a configuração __kafka-env,__ introduza `kafka-env` no campo __Filtro__ no canto superior direito.

    ![Configuração kafka, para kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Para configurar kafka para anunciar endereços IP, adicione o seguinte texto ao fundo do campo __de modelo kafka-env:__

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar a interface que Kafka ouve, entre `listeners` no campo __Filtro__ no canto superior direito.

7. Para configurar a Kafka para ouvir em todas as interfaces de rede, altere o valor no campo dos __ouvintes__ para `PLAINTEXT://0.0.0.0:9092` .

8. Para guardar as alterações de configuração, utilize o botão __Guardar.__ Introduza uma mensagem de texto descrevendo as alterações. Selecione __OK__ uma vez que as alterações tenham sido guardadas.

    ![Apache Ambari salvar configuração](./media/apache-kafka-azure-container-services/save-configuration-button.png)

9. Para evitar erros ao reiniciar o Kafka, utilize o botão __Ações de Serviço__ e selecione __Ligue o modo de manutenção__. Selecione OK para concluir esta operação.

    ![Ações de serviço, com a manutenção em destaque](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Para reiniciar Kafka, utilize o botão __Reiniciar__ e selecione __Reiniciar todos os afetados__. Confirme o reinício e, em seguida, utilize o botão __OK__ depois de concluída a operação.

    ![Reiniciar botão com reiniciar todos os afetados em destaque](./media/apache-kafka-azure-container-services/restart-required-button.png)

11. Para desativar o modo de manutenção, utilize o botão __Ações de Serviço__ e selecione __Desligue o Modo de Manutenção__. Selecione **OK** para concluir esta operação.

## <a name="test-the-configuration"></a>Testar a configuração

Neste momento, o Serviço Kafka e Azure Kubernetes estão em comunicação através das redes virtuais. Para testar esta ligação, utilize os seguintes passos:

1. Crie um tópico Kafka que seja utilizado pela aplicação de teste. Para obter informações sobre a criação de tópicos kafka, consulte o documento [de cluster Apache Kafka.](apache-kafka-get-started.md)

2. Descarregue a aplicação exemplo a partir de [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test) .

3. Editar o `index.js` ficheiro e alterar as seguintes linhas:

    * `var topic = 'mytopic'`: `mytopic` Substitua-o pelo nome do tópico Kafka utilizado por esta aplicação.
    * `var brokerHost = '176.16.0.13:9092`: `176.16.0.13` Substitua-o pelo endereço IP interno de um dos anfitriões do corretor para o seu cluster.

        Para encontrar o endereço IP interno dos anfitriões do corretor (workernodes) no cluster, consulte o documento [Apache Ambari REST API.](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-internal-ip-address-of-cluster-nodes) Escolha o endereço IP de uma das entradas onde o nome de domínio começa por `wn` .

4. A partir de uma linha de comando no `src` diretório, instale dependências e use Docker para construir uma imagem para implantação:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > As embalagens necessárias a esta aplicação são colocadas no repositório, pelo que não é necessário utilizar o `npm` utilitário para os instalar.

5. Faça login no seu Registo de Contentores Azure (ACR) e encontre o nome do loginServer:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Se não sabe o nome do registo do seu contentor Azure, ou não estiver familiarizado com a utilização do CLI Azure para trabalhar com o Serviço Azure Kubernetes, consulte os [tutoriais da AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Marque a imagem local `kafka-aks-test` com o loginServer do seu ACR. Adicione também `:v1` ao final para indicar a versão de imagem:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Empurre a imagem para o registo:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Esta operação leva vários minutos para ser concluída.

8. Edite o ficheiro manifesto Kubernetes ( `kafka-aks-test.yaml` ) e `microsoft` substitua-o pelo nome de login ACRServer recuperado no passo 4.

9. Utilize o seguinte comando para implementar as definições de aplicação a partir do manifesto:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Utilize o seguinte comando para observar a `EXTERNAL-IP` aplicação:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Uma vez atribuído um endereço IP externo, utilize __CTRL + C__ para sair do relógio

11. Abra um navegador web e insira o endereço IP externo para o serviço. Chega a uma página semelhante à seguinte imagem:

    ![Apache Kafka test imagem da página web](./media/apache-kafka-azure-container-services/test-web-page-image1.png)

12. Introduza texto no campo e, em seguida, selecione o botão __Enviar.__ Os dados são enviados para Kafka. Em seguida, o consumidor kafka na aplicação lê a mensagem e adiciona-a às mensagens da secção __Kafka.__

    > [!WARNING]  
    > Pode receber várias cópias de uma mensagem. Este problema geralmente acontece quando você atualiza o seu navegador após a ligação, ou abre várias ligações de navegador à aplicação.

## <a name="next-steps"></a>Passos seguintes

Utilize as seguintes ligações para saber como utilizar o Apache Kafka no HDInsight:

* [Começa com o Apache Kafka na HDInsight](apache-kafka-get-started.md)

* [Use o MirrorMaker para criar uma réplica de Apache Kafka em HDInsight](apache-kafka-mirroring.md)

* [Use a Tempestade Apache com Apache Kafka em HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Use Apache Spark com Apache Kafka em HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Conecte-se a Apache Kafka através de uma Rede Virtual Azure](apache-kafka-connect-vpn-gateway.md)
