---
title: Use o Serviço Azure Kubernetes com Kafka no HDInsight
description: Saiba como utilizar kafka no HDInsight a partir de imagens de contentores hospedadas no Serviço Azure Kubernetes (AKS).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 55373f71c78b6d45b9c78c52dea61a37b89b4a00
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383050"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Use o Serviço Azure Kubernetes com Apache Kafka no HDInsight

Saiba como utilizar o Serviço Azure Kubernetes (AKS) com [Apache Kafka](https://kafka.apache.org/) no cluster HDInsight. Os passos neste documento utilizam uma aplicação Node.js alojada na AKS para verificar a conectividade com kafka. Esta aplicação utiliza o pacote [kafka-nó](https://www.npmjs.com/package/kafka-node) para comunicar com Kafka. Utiliza [Socket.io](https://socket.io/) para mensagens orientadas por eventos entre o cliente do navegador e o back-end hospedado no AKS.

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de transmissão em fluxo distribuída de código aberto que pode ser utilizada para criar aplicações e pipelines de dados de transmissão em fluxo em tempo real. O Serviço Azure Kubernetes gere o seu ambiente kubernetes hospedado, e torna-o rápido e fácil de implementar aplicações contentorizadas. Utilizando uma Rede Virtual Azure, pode ligar os dois serviços.

> [!NOTE]  
> O foco deste documento está nos passos necessários para permitir ao Serviço Azure Kubernetes comunicar com Kafka no HDInsight. O exemplo em si é apenas um cliente básico de Kafka para demonstrar que a configuração funciona.

## <a name="prerequisites"></a>Pré-requisitos

* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Uma subscrição do Azure.

Este documento assume que está familiarizado com a criação e utilização dos seguintes serviços Azure:

* Kafka no HDInsight
* Azure Kubernetes Service
* Redes Virtuais do Azure

Este documento também assume que você andou pelo tutorial do [Serviço Azure Kubernetes.](../../aks/tutorial-kubernetes-prepare-app.md) Este artigo cria um serviço de contentores, cria um cluster Kubernetes, um registo de contentores, e confunde a `kubectl` utilidade.

## <a name="architecture"></a>Arquitetura

### <a name="network-topology"></a>Topologia da rede

Tanto o HDInsight como o AKS utilizam uma Rede Virtual Azure como recipiente para recursos de computação. Para permitir a comunicação entre o HDInsight e o AKS, deve ativar a comunicação entre as suas redes. Os passos neste documento utilizam o Virtual Network Peering para as redes. Outras ligações, como a VPN, também devem funcionar. Para obter mais informações sobre o peering, consulte o documento de observação da [rede Virtual.](../../virtual-network/virtual-network-peering-overview.md)

O diagrama seguinte ilustra a topologia da rede utilizada neste documento:

![HDInsight em uma rede virtual, AKS em outra, usando o peering](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> A resolução de nomes não está ativada entre as redes peered, pelo que é utilizada a endereçação IP. Por padrão, kafka no HDInsight está configurado para devolver os nomes do anfitrião em vez de endereços IP quando os clientes se conectam. Os passos deste documento modificam kafka para usar publicidade IP em vez disso.

## <a name="create-an-azure-kubernetes-service-aks"></a>Criar um Serviço Azure Kubernetes (AKS)

Se ainda não tem um cluster AKS, utilize um dos seguintes documentos para aprender a criar um:

* [Implementar um cluster azure Kubernetes Service (AKS) - Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Implementar um cluster azure Kubernetes Service (AKS) - CLI](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> A AKS cria uma rede virtual durante a instalação num grupo de recursos **adicionais.** O grupo de recursos adicionais segue a convenção de nomeação de **MC_resourceGroup_AKSclusterName_location.**  
> Esta rede é espartada para a criada para o HDInsight na secção seguinte.

## <a name="configure-virtual-network-peering"></a>Configurar o peering da rede virtual

### <a name="identify-preliminary-information"></a>Identificar informações preliminares

1. A partir do [portal Azure, localize](https://portal.azure.com)o **grupo de Recursos** adicionais que contém a rede virtual para o seu cluster AKS.

2. Do grupo de recursos, selecione o recurso __de rede Virtual.__ Anote o nome para utilização posterior.

3. Em **Definições,** selecione __espaço 'Endereço'.__ Note o espaço de endereçolistado.

### <a name="create-virtual-network"></a>Criar a rede virtual

1. Para criar uma rede virtual para o HDInsight, navegue para __+ Criar uma__ > __rede virtual__de__rede__ > de recursos .

1. Criar a rede utilizando as seguintes orientações para determinadas propriedades:

    |Propriedade | Valor |
    |---|---|
    |Espaço de endereços|Deve utilizar um espaço de endereço que não se sobreponha ao utilizado pela rede de clusterAKS.|
    |Localização|Utilize o mesmo __Local__ para a rede virtual que utilizou para o cluster AKS.|

1. Aguarde até que a rede virtual tenha sido criada antes de passar para o próximo passo.

### <a name="configure-peering"></a>Configurar o peering

1. Para configurar o epeering entre a rede HDInsight e a rede de cluster AKS, selecione a rede virtual e, em seguida, selecione __Peerings__.

1. Selecione __+ Adicione__ e use os seguintes valores para preencher o formulário:

    |Propriedade |Valor |
    |---|---|
    |Nome do peering \<deste VN> para rede virtual remota|Introduza um nome único para esta configuração de observação.|
    |Rede virtual|selecione a rede virtual para o **cluster AKS**.|
    |Nome do olhar \<da AKS VN> para \<este> VN|Introduza um nome exclusivo.|

    Deixe todos os outros campos pelo valor padrão e, em seguida, selecione __OK__ para configurar o epeering.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Criar o cluster Apache Kafka no HDInsight

Ao criar o cluster Kafka no HDInsight, deve juntar-se à rede virtual criada anteriormente para o HDInsight. Para obter mais informações sobre a criação de um cluster Kafka, consulte o documento [de cluster Da Criação](apache-kafka-get-started.md) de Apache Kafka.

## <a name="configure-apache-kafka-ip-advertising"></a>Configure Publicidade IP Apache Kafka

Utilize os seguintes passos para configurar kafka para anunciar endereços IP em vez de nomes de domínio:

1. Num browser, aceda a `https://CLUSTERNAME.azurehdinsight.net`. Substitua o CLUSTERNAME pelo nome do Kafka no cluster HDInsight.

    Quando solicitado, utilize o nome de utilizador HTTPS e a palavra-passe para o cluster. A Ambari Web UI para o cluster é exibida.

2. Para ver informações sobre Kafka, __selecione Kafka__ da lista à esquerda.

    ![Lista de serviçocom Kafka em destaque](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Para ver a configuração kafka, selecione __Configs__ do meio superior.

    ![Configuração dos serviços Apache Ambari](./media/apache-kafka-azure-container-services/select-kafka-config1.png)

4. Para encontrar a configuração __kafka-env,__ introduza `kafka-env` no campo __Filter__ na parte superior direita.

    ![Configuração kafka, para kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Para configurar kafka para anunciar endereços IP, adicione o seguinte texto na parte inferior do campo __kafka-env-template:__

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar a interface que Kafka `listeners` ouve, introduza no campo __Filter__ na parte superior direita.

7. Para configurar kafka para ouvir todas as interfaces de `PLAINTEXT://0.0.0.0:9092`rede, altere o valor no campo dos __ouvintes__ para .

8. Para evitar as alterações de configuração, utilize o botão __Guardar.__ Introduza uma mensagem de texto descrevendo as alterações. Selecione __OK__ uma vez que as alterações tenham sido guardadas.

    ![Configuração de salvamento Apache Ambari](./media/apache-kafka-azure-container-services/save-configuration-button.png)

9. Para evitar erros ao reiniciar o Kafka, utilize o botão __Desviação__ de Ação de Serviço e selecione __modo de manutenção Ligar__. Selecione OK para completar esta operação.

    ![Ações de serviço, com a entrada em destaque na manutenção](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Para reiniciar o Kafka, utilize o botão __Reiniciar__ e selecione __Reiniciar Todos os Afetados__. Confirme o reinício e, em seguida, utilize o botão __OK__ depois de concluída a operação.

    ![Botão de reiniciar com reiniciar todos os afetados destacados](./media/apache-kafka-azure-container-services/restart-required-button.png)

11. Para desativar o modo de manutenção, utilize o botão "Ação de __Serviço"__ e selecione desligar o __modo de manutenção__. Selecione **OK** para completar esta operação.

## <a name="test-the-configuration"></a>Testar a configuração

Neste momento, o Kafka e o Azure Kubernetes Service estão em comunicação através das redes virtuais. Para testar esta ligação, utilize os seguintes passos:

1. Crie um tópico Kafka que seja usado pela aplicação de teste. Para obter informações sobre a criação de tópicos kafka, consulte o documento [de cluster Create a Apache Kafka.](apache-kafka-get-started.md)

2. Descarregue a [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test)aplicação exemplo a partir de .

3. Editar `index.js` o ficheiro e alterar as seguintes linhas:

    * `var topic = 'mytopic'`: `mytopic` Substitua o nome do tópico Kafka utilizado por esta aplicação.
    * `var brokerHost = '176.16.0.13:9092`: `176.16.0.13` Substitua com o endereço IP interno de um dos anfitriões do corretor para o seu cluster.

        Para encontrar o endereço IP interno dos anfitriões de corretor (nódeos de trabalhadores) no cluster, consulte o documento [Apache Ambari REST API.](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-internal-ip-address-of-cluster-nodes) Escolha o endereço IP de uma das `wn`entradas onde o nome de domínio começa com .

4. A partir de `src` uma linha de comando no diretório, instale dependências e use o Docker para construir uma imagem para implantação:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > As embalagens exigidas por esta aplicação são verificadas no repositório, pelo que não precisa de utilizar o `npm` utilitário para os instalar.

5. Inicie sessão no registo de contentores do Seu Azure (ACR) e encontre o nome loginServer:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Se não conhece o seu nome de Registo de Contentores Azure, ou não está familiarizado com a utilização do Azure CLI para trabalhar com o Serviço Azure Kubernetes, consulte os [tutoriais AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Marque `kafka-aks-test` a imagem local com o loginServer do seu ACR. Adicione `:v1` também ao final para indicar a versão da imagem:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Empurre a imagem para o registo:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Esta operação leva vários minutos para ser concluída.

8. Editar o ficheiro manifesto Kubernetes (`kafka-aks-test.yaml`) e substituir `microsoft` pelo nome ACR loginServer recuperado no passo 4.

9. Utilize o seguinte comando para implantar as definições de aplicação a partir do manifesto:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Utilize o seguinte comando `EXTERNAL-IP` para vigiar a aplicação:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Uma vez atribuído um endereço IP externo, utilize __CTRL + C__ para sair do relógio

11. Abra um navegador web e introduza o endereço IP externo para o serviço. Chega a uma página semelhante à seguinte imagem:

    ![Apache Kafka test web page imagem](./media/apache-kafka-azure-container-services/test-web-page-image1.png)

12. Introduza texto no campo e, em seguida, selecione o botão __Enviar.__ Os dados são enviados para Kafka. Em seguida, o consumidor de Kafka na aplicação lê a mensagem e adiciona-a às __Mensagens da__ secção Kafka.

    > [!WARNING]  
    > Pode receber várias cópias de uma mensagem. Este problema geralmente acontece quando atualiza o seu navegador após a ligação, ou abre várias ligações de navegador à aplicação.

## <a name="next-steps"></a>Passos seguintes

Utilize as seguintes ligações para saber como utilizar o Apache Kafka no HDInsight:

* [Começa com Apache Kafka no HDInsight](apache-kafka-get-started.md)

* [Use mirrormaker para criar uma réplica de Apache Kafka no HDInsight](apache-kafka-mirroring.md)

* [Use Apache Storm com Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Use Apache Spark com Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Ligue-se a Apache Kafka através de uma Rede Virtual Azure](apache-kafka-connect-vpn-gateway.md)
