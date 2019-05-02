---
title: Configurar políticas do Apache Kafka no HDInsight com o Enterprise Security Package - Azure
description: Saiba como configurar as políticas do Apache Ranger para Kafka no Azure HDInsight com o Pacote de Segurança Enterprise.
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 01/14/2019
ms.openlocfilehash: 6434f7cae3c3fa402efad00b2f6bfb0bc405f9e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730244"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Tutorial: Configurar políticas do Apache Kafka no HDInsight com o Enterprise Security Package (pré-visualização)

Saiba como configurar políticas do Apache Ranger para clusters do Apache Kafka do pacote de segurança da empresa (ESP). Os clusters do ESP estão ligados a um domínio, permitindo que os utilizadores sejam autenticados com credenciais do domínio. Neste tutorial, vai criar duas políticas do Ranger para restringir o acesso aos tópicos `sales*` e `marketingspend`.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar utilizadores de domínio
> * Criar políticas do Ranger
> * Crie tópicos num cluster do Kafka
> * Testar as políticas do Ranger

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

* Crie um [cluster Kafka no HDInsight com o Pacote de Segurança Enterprise](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Ligar à IU do Apache Ranger Admin

1. Num browser, ligue à interface de utilizador do Ranger Admin através do URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Não se esqueça de alterar `<ClusterName>` para o nome do cluster do Kafka.

    > [!NOTE]  
    > As credenciais do Ranger não são iguais às credenciais de cluster do Hadoop. Para impedir que os browsers utilizem credenciais em cache do Hadoop, utilize uma nova janela do browser InPrivate para ligar à IU do Ranger Admin.

2. Inicie sessão com as suas credenciais de administrador do Azure Active Directory (AD). As credenciais de administrador do Azure AD não iguais às credenciais de cluster do HDInsight ou às credenciais SSH de nó HDInsight do Linux.

   ![IU do Apache Ranger Admin](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Criar utilizadores de domínio

Visite [Criar um cluster do HDInsight com o Pacote de Segurança Enterprise](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds), para saber como criar os utilizadores de domínio **sales_user** e **marketing_user**. Num cenário de produção, os utilizadores de domínio são provenientes do seu inquilino do Active Directory.

## <a name="create-ranger-policy"></a>Criar política do Ranger

Crie uma política do Ranger para **sales_user** e **marketing_user**.

1. Abra a **IU do Ranger Admin**.

2. Clique em **\<ClusterName>_kafka** em **Kafka**. Poderá ser apresentada uma política pré-configurada.

3. Clique em **Adicionar Nova Política** e introduza os seguintes valores:

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da Política  |  política hdi sales*   |
   |Tópico   |  sales* |
   |Selecionar Utilizador  |  sales_user1 |
   |Permissões  | publicar, consumir, criar |

   Os carateres universais seguintes podem ser incluídos no nome do tópico:

   * “*” indica nenhuma ou mais ocorrências de carateres.
   * “?” indica um caráter único.

   ![Criar Política da IU do Apache Ranger Admin](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)   

   >[!NOTE]
   >Aguarde um momento enquanto o Ranger sincroniza com o Azure AD, se um utilizador de domínio não for preenchido automaticamente em **Selecionar Utilizador**.

4. Clique em **Adicionar** para guardar a política.

5. Clique em **Adicionar Nova Política** e, em seguida, introduza os seguintes valores:

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da Política  |  política hdi marketing   |
   |Tópico   |  marketingspend |
   |Selecionar Utilizador  |  marketing_user1 |
   |Permissões  | publicar, consumir, criar |

   ![Criar Política da IU do Apache Ranger Admin](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Clique em **Adicionar** para guardar a política.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Criar tópicos num cluster do Kafka com o ESP

Para criar dois tópicos, `salesevents` e `marketingspend`:

1. Utilize o seguinte comando para abrir uma ligação SSH ao cluster:

   ```bash
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Substitua `DOMAINADMIN` com o utilizador de administrador para o seu cluster configurado durante [criação de cluster](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-hdinsight-cluster-with-esp)e substitua `CLUSTERNAME` com o nome do cluster. Se lhe for pedido, introduza a palavra-passe da conta de utilizador de administrador. Para obter mais informações sobre como utilizar `SSH` com o HDInsight, veja [Utilizar SSH com o HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix).

2. Utilize os comandos seguintes para guardar o nome do cluster numa variável e instalar um utilitário de análise JSON `jq`. Quando lhe for pedido, introduza o nome do cluster do Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Utilize os seguintes comandos para obter o mediador Kafka anfitriões. Quando lhe for pedido, introduza a palavra-passe da conta de administrador do cluster.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   > [!Note]  
   > Antes de continuar, terá de configurar o ambiente de desenvolvimento, se ainda não o tiver feito. Precisará de componentes, como o Java JDK, Apache Maven e um cliente SSH com scp. Para obter mais detalhes, consulte [instruções de configuração](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).
   
1. Transfira os [exemplos de consumidor produtor associados a um domínio do Apache Kafka](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Siga os passos 2 e 3 sob **criar e implementar o exemplo** no [Tutorial: Utilizar o Apache Kafka produtor e consumidor APIs](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example)

1. Execute os seguintes comandos:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testar as políticas do Ranger

Com base em políticas do Ranger configuradas, **sales_user** pode produzir/consumir tópico `salesevents` mas não tópico `marketingspend`. Por outro lado, **marketing_user** pode produzir/consumir tópico `marketingspend` mas não tópico `salesevents`.

1. Abra uma nova ligação SSH ao cluster. Utilize o seguinte comando para iniciar sessão como **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Execute o seguinte comando:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Utilize os nomes de Mediador da secção anterior para definir a variável de ambiente seguintes:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Exemplo: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Siga o passo 3 sob **criar e implementar o exemplo** no [Tutorial: Utilizar as APIs de consumidor e o Apache Kafka produtor](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example) para se certificar de que o `kafka-producer-consumer.jar` também está disponível para **sales_user**.

5. Certifique-se de que **sales_user1** pode produzir para tópico `salesevents` executando o seguinte comando:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Execute o seguinte comando para consumir de tópico `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Certifique-se de que pode ler as mensagens.

7. Certifique-se de que o **sales_user1** não é possível produzir para tópico `marketingspend` executando o seguinte na mesma ssh janela:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Um erro de autorização ocorre e pode ser ignorado.

8. Tenha em atenção que **marketing_user1** não pode consumir de tópico `salesevents`.

   Repita os passos 1 a 4 acima, mas desta vez, como **marketing_user1**.

   Execute o seguinte comando para consumir de tópico `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   As mensagens anteriores não podem ser vistas.

9. Veja os eventos de acesso de auditoria da IU do Ranger.

   ![Auditoria à Política da IU do Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>Passos Seguintes

* [Traga a sua própria chave ao Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [Uma introdução à segurança do Apache Hadoop com o Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
