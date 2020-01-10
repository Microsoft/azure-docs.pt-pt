---
title: Tutorial-Apache Kafka & segurança empresarial-Azure HDInsight
description: Tutorial-saiba como configurar políticas do Apache Ranger para Kafka no Azure HDInsight com o Enterprise Security Package.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: a0205d57fa68585b1a91b99b19e008eb92e73c0d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435845"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Tutorial: configurar políticas de Apache Kafka no HDInsight com Enterprise Security Package (versão prévia)

Saiba como configurar políticas do Apache Ranger para clusters de Apache Kafka Enterprise Security Package (ESP). Os clusters do ESP estão ligados a um domínio, permitindo que os utilizadores sejam autenticados com credenciais do domínio. Neste tutorial, vai criar duas políticas do Ranger para restringir o acesso aos tópicos `sales` e `marketingspend`.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar utilizadores de domínio
> * Criar políticas do Ranger
> * Crie tópicos num cluster do Kafka
> * Testar as políticas do Ranger

## <a name="prerequisite"></a>Pré-requisito

Um [cluster HDInsight Kafka com Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Ligar à IU do Apache Ranger Admin

1. Num browser, ligue à interface de utilizador do Ranger Admin através do URL `https://ClusterName.azurehdinsight.net/Ranger/`. Não se esqueça de alterar `ClusterName` para o nome do cluster do Kafka. As credenciais do Ranger não são iguais às credenciais de cluster do Hadoop. Para impedir que os browsers utilizem credenciais em cache do Hadoop, utilize uma nova janela do browser InPrivate para ligar à IU do Ranger Admin.

2. Inicie sessão com as suas credenciais de administrador do Azure Active Directory (AD). As credenciais de administrador do Azure AD não iguais às credenciais de cluster do HDInsight ou às credenciais SSH de nó HDInsight do Linux.

   ![IU de administração do Apache Ranger do HDInsight](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Criar utilizadores de domínio

Visite [Criar um cluster do HDInsight com o Pacote de Segurança Enterprise](./apache-domain-joined-configure-using-azure-adds.md), para saber como criar os utilizadores de domínio **sales_user** e **marketing_user**. Num cenário de produção, os utilizadores de domínio são provenientes do seu inquilino do Active Directory.

## <a name="create-ranger-policy"></a>Criar política do Ranger

Crie uma política do Ranger para **sales_user** e **marketing_user**.

1. Abra a **IU do Ranger Admin**.

2. Selecione **\<clustername > _kafka** em **Kafka**. Poderá ser apresentada uma política pré-configurada.

3. Selecione **Adicionar nova política** e insira os seguintes valores:

   |Definição  |Valor sugerido  |
   |---------|---------|
   |Nome da Política  |  política hdi sales*   |
   |Tópico   |  sales* |
   |Selecionar Utilizador  |  sales_user1 |
   |Permissões  | publicar, consumir, criar |

   Os carateres universais seguintes podem ser incluídos no nome do tópico:

   * “*” indica nenhuma ou mais ocorrências de carateres.
   * “?” indica um caráter único.

   ![Interface do usuário do administrador do Apache Ranger Create Policy1](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   Aguarde um momento enquanto o Ranger sincroniza com o Azure AD, se um utilizador de domínio não for preenchido automaticamente em **Selecionar Utilizador**.

4. Selecione **Adicionar** para salvar a política.

5. Selecione **Adicionar nova política** e, em seguida, insira os seguintes valores:

   |Definição  |Valor sugerido  |
   |---------|---------|
   |Nome da Política  |  política hdi marketing   |
   |Tópico   |  marketingspend |
   |Selecionar Utilizador  |  marketing_user1 |
   |Permissões  | publicar, consumir, criar |

   ![Interface do usuário do administrador do Apache Ranger Create Policy2](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Selecione **Adicionar** para salvar a política.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Criar tópicos num cluster do Kafka com o ESP

Para criar dois tópicos, `salesevents` e `marketingspend`:

1. Utilize o seguinte comando para abrir uma ligação SSH ao cluster:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Substitua `DOMAINADMIN` pelo usuário administrador do cluster configurado durante a [criação do cluster](./apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)e substitua `CLUSTERNAME` pelo nome do cluster. Se solicitado, insira a senha para a conta de usuário administrador. Para obter mais informações sobre como utilizar `SSH` com o HDInsight, veja [Utilizar SSH com o HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize os comandos seguintes para guardar o nome do cluster numa variável e instalar um utilitário de análise JSON `jq`. Quando lhe for pedido, introduza o nome do cluster do Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Use os comandos a seguir para obter os hosts do agente do Kafka. Quando lhe for pedido, introduza a palavra-passe da conta de administrador do cluster.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Antes de continuar, talvez seja necessário configurar seu ambiente de desenvolvimento se você ainda não tiver feito isso. Você precisará de componentes como Java JDK, Apache Maven e um cliente SSH com o scp. Para obter mais informações, consulte [instruções de instalação](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Transfira os [exemplos de consumidor produtor associados a um domínio do Apache Kafka](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Siga os passos 2 e 3 em **Criar e implementar o exemplo** no [Tutorial: utilizar APIs de Produtor e Consumidor do Apache Kafka](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)

1. Execute os seguintes comandos:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testar as políticas do Ranger

Com base nas políticas de Ranger configuradas, **sales_user** pode produzir/consumir o tópico `salesevents`, mas não o tópico `marketingspend`. Por outro lado, **marketing_user** pode produzir/consumir o tópico `marketingspend`, mas não o tópico `salesevents`.

1. Abra uma nova ligação SSH ao cluster. Utilize o seguinte comando para iniciar sessão como **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Execute o seguinte comando:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Use os nomes do agente da seção anterior para definir a seguinte variável de ambiente:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Exemplo: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Siga a etapa 3 em **criar e implantar o exemplo** no [tutorial: Use o Apache Kafka produtor e as APIs de consumidor](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) para garantir que a `kafka-producer-consumer.jar` também esteja disponível para **sales_user**.

5. Verifique se **sales_user1** pode produzir para o tópico `salesevents` executando o seguinte comando:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Execute o seguinte comando para consumir do tópico `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Verifique se você é capaz de ler as mensagens.

7. Verifique se a **sales_user1** não pode produzir para o tópico `marketingspend` executando o seguinte na mesma janela SSH:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Um erro de autorização ocorre e pode ser ignorado.

8. Observe que **marketing_user1** não pode consumir do tópico `salesevents`.

   Repita as etapas 1-4 acima, mas desta vez como **marketing_user1**.

   Execute o seguinte comando para consumir do tópico `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   As mensagens anteriores não podem ser vistas.

9. Veja os eventos de acesso de auditoria da IU do Ranger.

   ![Eventos de acesso de auditoria da política de IU do Ranger ](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você não for continuar a usar este aplicativo, exclua o cluster Kafka que você criou com as seguintes etapas:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Na caixa de **pesquisa** na parte superior, digite **HDInsight**.
1. Selecione **clusters HDInsight** em **Serviços**.
1. Na lista de clusters HDInsight que aparece, clique em **...** ao lado do cluster que você criou para este tutorial. 
1. Clique em **Eliminar**. Clique em **Sim**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Traga sua própria chave para Apache Kafka](../kafka/apache-kafka-byok.md)