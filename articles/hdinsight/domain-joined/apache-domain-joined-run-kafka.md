---
title: Tutorial - Apache Kafka & Enterprise Security - Azure HDInsight
description: Tutorial - Saiba como configurar as políticas do Apache Ranger para kafka em Azure HDInsight com pacote de segurança empresarial.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 58c5b3bdd6d50f2e512cccffe78bd4e70805d729
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78204740"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Tutorial: Configure as políticas de Apache Kafka no HDInsight com pacote de segurança empresarial (pré-visualização)

Saiba como configurar as políticas apache Ranger para os clusters Apache Security Package (ESP) Apache Kafka. Os clusters do ESP estão ligados a um domínio, permitindo que os utilizadores sejam autenticados com credenciais do domínio. Neste tutorial, vai criar duas políticas do Ranger para restringir o acesso aos tópicos `sales` e `marketingspend`.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar utilizadores de domínio
> * Criar políticas do Ranger
> * Crie tópicos num cluster do Kafka
> * Testar as políticas do Ranger

## <a name="prerequisite"></a>Pré-requisito

Um [cluster HDInsight Kafka com pacote](./apache-domain-joined-configure-using-azure-adds.md)de segurança empresarial .

## <a name="connect-to-apache-ranger-admin-ui"></a>Ligar à IU do Apache Ranger Admin

1. Num browser, ligue à interface de utilizador do Ranger Admin através do URL `https://ClusterName.azurehdinsight.net/Ranger/`. Não se esqueça de alterar `ClusterName` para o nome do cluster do Kafka. As credenciais do Ranger não são iguais às credenciais de cluster do Hadoop. Para impedir que os browsers utilizem credenciais em cache do Hadoop, utilize uma nova janela do browser InPrivate para ligar à IU do Ranger Admin.

2. Inicie sessão com as suas credenciais de administrador do Azure Active Directory (AD). As credenciais de administrador do Azure AD não iguais às credenciais de cluster do HDInsight ou às credenciais SSH de nó HDInsight do Linux.

   ![HDInsight Apache Ranger Admin UI](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Criar utilizadores de domínio

Visite [Criar um cluster do HDInsight com o Pacote de Segurança Enterprise](./apache-domain-joined-configure-using-azure-adds.md), para saber como criar os utilizadores de domínio **sales_user** e **marketing_user**. Num cenário de produção, os utilizadores de domínio são provenientes do seu inquilino do Active Directory.

## <a name="create-ranger-policy"></a>Criar política do Ranger

Crie uma política do Ranger para **sales_user** e **marketing_user**.

1. Abra a **IU do Ranger Admin**.

2. Selecione ** \<ClusterName>_kafka** em **Kafka**. Poderá ser apresentada uma política pré-configurada.

3. Selecione **Adicionar Nova Política** e introduza os seguintes valores:

   |Definição  |Valor sugerido  |
   |---------|---------|
   |Nome da Política  |  política hdi sales*   |
   |Tópico   |  sales* |
   |Selecionar Utilizador  |  sales_user1 |
   |Permissões  | publicar, consumir, criar |

   Os carateres universais seguintes podem ser incluídos no nome do tópico:

   * “*” indica nenhuma ou mais ocorrências de carateres.
   * “?” indica um caráter único.

   ![Apache Ranger Admin UI Criar Política1](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   Aguarde um momento enquanto o Ranger sincroniza com o Azure AD, se um utilizador de domínio não for preenchido automaticamente em **Selecionar Utilizador**.

4. Selecione **Adicionar** para salvar a apólice.

5. Selecione **Adicionar Nova Política** e, em seguida, introduzir os seguintes valores:

   |Definição  |Valor sugerido  |
   |---------|---------|
   |Nome da Política  |  política hdi marketing   |
   |Tópico   |  marketingspend |
   |Selecionar Utilizador  |  marketing_user1 |
   |Permissões  | publicar, consumir, criar |

   ![Apache Ranger Admin UI Criar Política2](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Selecione **Adicionar** para salvar a apólice.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Criar tópicos num cluster do Kafka com o ESP

Para criar dois `salesevents` tópicos, e: `marketingspend`

1. Utilize o seguinte comando para abrir uma ligação SSH ao cluster:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Substitua-a `DOMAINADMIN` pelo utilizador administrativo do seu cluster `CLUSTERNAME` configurado durante a [criação](./apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)do cluster e substitua-a pelo nome do seu cluster. Se solicitado, introduza a palavra-passe para a conta de utilizador administrativo. Para obter mais informações sobre como utilizar `SSH` com o HDInsight, veja [Utilizar SSH com o HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize os comandos seguintes para guardar o nome do cluster numa variável e instalar um utilitário de análise JSON `jq`. Quando lhe for pedido, introduza o nome do cluster do Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Use os seguintes comandos para obter os anfitriões do corretor Kafka. Quando lhe for pedido, introduza a palavra-passe da conta de administrador do cluster.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Antes de prosseguir, poderá ter de configurar o seu ambiente de desenvolvimento se ainda não o tiver feito. Você precisará de componentes como o Java JDK, Apache Maven, e um cliente SSH com scp. Para mais informações, consulte [as instruções](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)de configuração .

1. Transfira os [exemplos de consumidor produtor associados a um domínio do Apache Kafka](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Siga os passos 2 e 3 em **Criar e implementar o exemplo** no [Tutorial: utilizar APIs de Produtor e Consumidor do Apache Kafka](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)

1. Execute os seguintes comandos:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testar as políticas do Ranger

Com base nas políticas dos Rangers configuradas, **sales_user** pode produzir/consumir tópico, `salesevents` mas não tópico. `marketingspend` Inversamente, **marketing_user** pode `marketingspend` produzir/consumir `salesevents`tópico, mas não tópico.

1. Abra uma nova ligação SSH ao cluster. Utilize o seguinte comando para iniciar sessão como **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Execute o seguinte comando:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Utilize os nomes do corretor da secção anterior para definir a seguinte variável ambiental:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Exemplo: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Siga o passo 3 sob **a construção e implemente o exemplo** no [Tutorial: Use o Produtor Apache Kafka e as APIs de consumo](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) para garantir que o `kafka-producer-consumer.jar` também está disponível para **sales_user**.

**Nota: Para este tutorial, utilize o frasco kafka-produtor-consumidor.no âmbito do projeto "DomainJoined-Producer-Consumer" (não o do projeto Produtor-Consumidor, que se destina a cenários não-domínio aderentes).**

5. Verifique se **sales_user1** pode `salesevents` produzir ao tópico executando o seguinte comando:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Executar o seguinte comando `salesevents`para consumir a partir do tópico:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Verifique se é capaz de ler as mensagens.

7. Verifique se o **sales_user1** não `marketingspend` pode produzir tópico executando o seguinte na mesma janela ssh:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Um erro de autorização ocorre e pode ser ignorado.

8. Note que **marketing_user1** não `salesevents`pode consumir do tópico.

   Repita os passos 1-4 acima, mas desta vez **como marketing_user1**.

   Executar o seguinte comando `salesevents`para consumir a partir do tópico:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   As mensagens anteriores não podem ser vistas.

9. Veja os eventos de acesso de auditoria da IU do Ranger.

   ![Eventos de acesso à auditoria política da Ranger UI ](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a usar esta aplicação, elimine o cluster Kafka que criou com os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Na caixa **de pesquisa** na parte superior, digite **HDInsight**.
1. Selecione **clusters HDInsight** em **Serviços**.
1. Na lista de clusters HDInsight que aparece, clique no **...** ao lado do cluster que criou para este tutorial. 
1. Clique em **Apagar**. Clique **sim**.

## <a name="troubleshooting"></a>Resolução de problemas
Se kafka-produtor-consumidor.jar não funcionar num cluster de domínio aderente, certifique-se de que está a utilizar o frasco kafka-produtor-consumidor.jar no âmbito do projeto "DomainJoined-Producer-Consumer" (não o do projeto Produtor-Consumidor, que se destina a cenários não-membros do domínio).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Encriptação de discos chave gerida pelo cliente](../disk-encryption.md)
