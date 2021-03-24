---
title: Tutorial - Apache Kafka & Segurança Empresarial - Azure HDInsight
description: Tutorial - Saiba como configurar as políticas apache ranger para Kafka em Azure HDInsight com Pacote de Segurança Empresarial.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: bab3df857dfdac3ca3b9193bda1caea0040a4cbb
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866986"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Tutorial: Configurar as políticas apache kafka em HDInsight com pacote de segurança empresarial (pré-visualização)

Saiba como configurar as políticas apache ranger para pacotes de segurança empresarial (ESP) apache kafka clusters. Os clusters do ESP estão ligados a um domínio, permitindo que os utilizadores sejam autenticados com credenciais do domínio. Neste tutorial, vai criar duas políticas do Ranger para restringir o acesso aos tópicos `sales` e `marketingspend`.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar utilizadores de domínio
> * Criar políticas do Ranger
> * Crie tópicos num cluster do Kafka
> * Testar as políticas do Ranger

## <a name="prerequisite"></a>Pré-requisito

Um [cluster HDInsight Kafka com pacote de segurança empresarial.](./apache-domain-joined-configure-using-azure-adds.md)

## <a name="connect-to-apache-ranger-admin-ui"></a>Ligar à IU do Apache Ranger Admin

1. Num browser, ligue à interface de utilizador do Ranger Admin através do URL `https://ClusterName.azurehdinsight.net/Ranger/`. Não se esqueça de alterar `ClusterName` para o nome do cluster do Kafka. As credenciais do Ranger não são iguais às credenciais de cluster do Hadoop. Para impedir que os browsers utilizem credenciais em cache do Hadoop, utilize uma nova janela do browser InPrivate para ligar à IU do Ranger Admin.

2. Inicie sessão com as suas credenciais de administrador do Azure Active Directory (AD). As credenciais de administrador do Azure AD não iguais às credenciais de cluster do HDInsight ou às credenciais SSH de nó HDInsight do Linux.

   :::image type="content" source="./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png" alt-text="HDInsight Apache Ranger Admin UI" border="true":::

## <a name="create-domain-users"></a>Criar utilizadores de domínio

Visite [Criar um cluster do HDInsight com o Pacote de Segurança Enterprise](./apache-domain-joined-configure-using-azure-adds.md), para saber como criar os utilizadores de domínio **sales_user** e **marketing_user**. Num cenário de produção, os utilizadores de domínio são provenientes do seu inquilino do Active Directory.

## <a name="create-ranger-policy"></a>Criar política do Ranger

Crie uma política do Ranger para **sales_user** e **marketing_user**.

1. Abra a **IU do Ranger Admin**.

2. Selecione **\<ClusterName> _kafka** em **Kafka**. Poderá ser apresentada uma política pré-configurada.

3. **Selecione Adicionar Nova Política** e introduza os seguintes valores:

   |Definição  |Valor sugerido  |
   |---------|---------|
   |Nome da Política  |  política hdi sales*   |
   |Tópico   |  sales* |
   |Selecionar Utilizador  |  sales_user1 |
   |Permissões  | publicar, consumir, criar |

   Os carateres universais seguintes podem ser incluídos no nome do tópico:

   * “*” indica nenhuma ou mais ocorrências de carateres.
   * “?” indica um caráter único.

   :::image type="content" source="./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png" alt-text="Apache Ranger Admin UI Criar Política1" border="true":::

   Aguarde um momento enquanto o Ranger sincroniza com o Azure AD, se um utilizador de domínio não for preenchido automaticamente em **Selecionar Utilizador**.

4. **Selecione Adicionar** para guardar a apólice.

5. **Selecione Adicionar Nova Política** e, em seguida, introduza os seguintes valores:

   |Definição  |Valor sugerido  |
   |---------|---------|
   |Nome da Política  |  política hdi marketing   |
   |Tópico   |  marketingspend |
   |Selecionar Utilizador  |  marketing_user1 |
   |Permissões  | publicar, consumir, criar |

   :::image type="content" source="./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png" alt-text="Apache Ranger Admin UI Criar Política2" border="true":::  

6. **Selecione Adicionar** para guardar a apólice.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Criar tópicos num cluster do Kafka com o ESP

Para criar dois tópicos, `salesevents` `marketingspend` e:

1. Utilize o seguinte comando para abrir uma ligação SSH ao cluster:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   `DOMAINADMIN`Substitua-o pelo utilizador administrativo do seu cluster configurado durante a [criação do cluster](./apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)e substitua-o `CLUSTERNAME` pelo nome do seu cluster. Se solicitado, introduza a palavra-passe para a conta de utilizador de administração. Para obter mais informações sobre como utilizar `SSH` com o HDInsight, veja [Utilizar SSH com o HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize os comandos seguintes para guardar o nome do cluster numa variável e instalar um utilitário de análise JSON `jq`. Quando lhe for pedido, introduza o nome do cluster do Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Use os seguintes comandos para obter os anfitriões corretores Kafka. Quando lhe for pedido, introduza a palavra-passe da conta de administrador do cluster.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Antes de prosseguir, poderá ter de configurar o seu ambiente de desenvolvimento se ainda não o fez. Você precisará de componentes como o Java JDK, Apache Maven, e um cliente SSH com scp. Para obter mais informações, consulte [as instruções de configuração](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Transfira os [exemplos de consumidor produtor associados a um domínio do Apache Kafka](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Siga os passos 2 e 3 em **Criar e implementar o exemplo** no [Tutorial: utilizar APIs de Produtor e Consumidor do Apache Kafka](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)

1. Execute os seguintes comandos:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testar as políticas do Ranger

Com base nas políticas dos Rangers configuradas, **sales_user** podem produzir/consumir `salesevents` tópico, mas não `marketingspend` tópico. Inversamente, **marketing_user** pode produzir/consumir `marketingspend` tópico, mas não `salesevents` tópico.

1. Abra uma nova ligação SSH ao cluster. Utilize o seguinte comando para iniciar sessão como **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Utilize os nomes de corretor da secção anterior para definir a seguinte variável ambiental:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Exemplo: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

3. Siga o Passo 3 em **Build e implemente o exemplo** em [Tutorial: Use as APIs do Produtor e do Consumidor Apache Kafka](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) para garantir que o `kafka-producer-consumer.jar` também está disponível para **sales_user**.

   > [!NOTE]  
   > Para este tutorial, utilize o kafka-produtor-consumidor.jar no âmbito do projeto "DomainJoined-Producer-Consumer" (não o Producer-Consumer projeto, que é para cenários não de domínio).

4. Verifique se **sales_user1** pode produzir para o tópico `salesevents` executando o seguinte comando:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

5. Execute o seguinte comando para consumir a partir do `salesevents` tópico:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Verifique se consegue ler as mensagens.

6. Verifique se o **sales_user1** não pode produzir para o tópico `marketingspend` executando o seguinte na mesma janela:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Um erro de autorização ocorre e pode ser ignorado.

7. Note que **marketing_user1** não podem consumir do `salesevents` tópico.

   Repita os passos 1 a 3 acima, mas desta vez, como **marketing_user1**.

   Execute o seguinte comando para consumir a partir do `salesevents` tópico:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   As mensagens anteriores não podem ser vistas.

8. Veja os eventos de acesso de auditoria da IU do Ranger.

   :::image type="content" source="./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png" alt-text="Eventos de acesso à auditoria política da Ranger UI " border="true":::
   
## <a name="produce-and-consume-topics-in-esp-kafka-by-using-the-console"></a>Produzir e consumir tópicos em ESP Kafka usando a consola

> [!NOTE]
> Não é possível usar comandos de consola para criar tópicos. Em vez disso, deve utilizar o código Java demonstrado na secção anterior. Para obter mais informações, consulte [Criar tópicos num cluster Kafka com ESP.](#create-topics-in-a-kafka-cluster-with-esp)

Para produzir e consumir tópicos em ESP Kafka utilizando a consola:

1. Utilize com o nome `kinit` de utilizador do utilizador. Introduza a palavra-passe quando solicitada.

   ```bash
   kinit sales_user1
   ```

2. Definir variáveis ambientais:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf"
   export KAFKABROKERS=<brokerlist>:9092
   ```

3. Produzir mensagens para o `salesevents` tópico:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --topic salesevents --broker-list $KAFKABROKERS --security-protocol SASL_PLAINTEXT
   ```

4. Consumir mensagens do `salesevents` tópico:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --topic salesevents --from-beginning --bootstrap-server $KAFKABROKERS --security-protocol SASL_PLAINTEXT
   ```

## <a name="clean-up-resources"></a>Limpar os recursos

Se não vai continuar a utilizar esta aplicação, elimine o cluster Kafka que criou com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Na caixa **de pesquisa** no topo, escreva **HDInsight**.
1. Selecione **clusters HDInsight** em **Serviços**.
1. Na lista de clusters HDInsight que aparecem, clique no **...** ao lado do cluster que criou para este tutorial. 
1. Clique em **Eliminar**. Clique em **Sim**.

## <a name="troubleshooting"></a>Resolução de problemas
Se o kafka-produtor-consumidor.jar não funcionar num cluster de domínios, certifique-se de que está a utilizar o kafka-produtor-produtor-consumidor.jar no âmbito do projeto "DomainJoined-Producer-Consumer" (não o Producer-Consumer projeto, que é para cenários não de domínio).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Encriptação de discos chave gerida pelo cliente](../disk-encryption.md)
