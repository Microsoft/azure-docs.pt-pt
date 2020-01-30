---
title: Autenticação de & de criptografia SSL Apache Kafka-Azure HDInsight
description: Configure a criptografia SSL para a comunicação entre os clientes Kafka e os agentes Kafka, bem como entre os agentes Kafka. Configurar a autenticação SSL de clientes.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 9b07d16ed97a93b5b5b9422673cfc38ada8e8116
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76764367"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurar a criptografia e autenticação do protocolo SSL (SSL) para Apache Kafka no Azure HDInsight

Este artigo mostra como configurar a criptografia SSL entre Apache Kafka clientes e Apache Kafka agentes. Ele também mostra como configurar a autenticação de clientes (às vezes chamados de SSL bidirecional).

> [!Important]
> Há dois clientes que você pode usar para aplicativos Kafka: um cliente Java e um cliente de console. Somente o `ProducerConsumer.java` de cliente Java pode usar SSL para produção e consumo. O `console-producer.sh` do cliente do produtor do console não funciona com SSL.

## <a name="apache-kafka-broker-setup"></a>Configuração de corretor Apache Kafka

A instalação do agente SSL do Kafka usará quatro VMs do cluster HDInsight da seguinte maneira:

* cabeçalho 0-autoridade de certificação (CA)
* nó de trabalho 0, 1 e 2-agentes

> [!Note] 
> Este guia usará certificados autoassinados, mas a solução mais segura é usar certificados emitidos por autoridades de certificação confiáveis.

O resumo do processo de configuração do agente é o seguinte:

1. As etapas a seguir são repetidas em cada um dos três nós de trabalho:

    1. Gerar um certificado.
    1. Crie uma solicitação de assinatura de certificado.
    1. Envie a solicitação de assinatura de certificado para a autoridade de certificação (CA).
    1. Entre na AC e assine a solicitação.
    1. SCP o certificado assinado de volta para o nó de trabalho.
    1. SCP o certificado público da autoridade de certificação para o nó de trabalho.

1. Depois que você tiver todos os certificados, coloque os certificados no repositório de certificados.
1. Vá para Ambari e altere as configurações.

Use as seguintes instruções detalhadas para concluir a configuração do agente:

> [!Important]
> Nos trechos de código a seguir, wnX é uma abreviação de um dos três nós de trabalho e deve ser substituído por `wn0`, `wn1` ou `wn2` conforme apropriado. `WorkerNode0_Name` e `HeadNode0_Name` devem ser substituídos pelos nomes das respetivas máquinas.

1. Execute a configuração inicial no nó de cabeçalho 0, que para o HDInsight preencherá a função da autoridade de certificação (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Execute a mesma configuração inicial em cada um dos agentes (nós de trabalho 0, 1 e 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Em cada um dos nós de trabalho, execute as etapas a seguir usando o trecho de código abaixo.
    1. Crie um repositório de chaves e popule-o com um novo certificado privado.
    1. Crie uma solicitação de assinatura de certificado.
    1. SCP a solicitação de assinatura de certificado para a autoridade de certificação (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Na máquina CA, execute o seguinte comando para criar os arquivos CA-CERT e CA-Key:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Altere para o computador da CA e assine todas as solicitações de assinatura de certificado recebidas:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Envie os certificados assinados de volta para os nós de trabalho da CA (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Envie o certificado público da autoridade de certificação para cada nó de trabalho.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Em cada nó de trabalho, adicione o certificado público de CAs ao trustStore e ao keystore. Em seguida, adicione o próprio certificado assinado do nó de trabalho ao repositório de chaves

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Atualizar a configuração do Kafka para usar SSL e reiniciar os agentes

Agora você configurou cada agente do Kafka com um repositório de chaves e trustStore e importou os certificados corretos. Em seguida, modifique as propriedades de configuração de Kafka relacionadas usando Ambari e reinicie os agentes Kafka.

Para concluir a modificação de configuração, execute as seguintes etapas:

1. Entre no portal do Azure e selecione o cluster do Azure HDInsight Apache Kafka.
1. Acesse a interface do usuário do amAmbari clicando em **Ambari página inicial** em **painéis do cluster**.
1. Em **Kafka Broker** , defina a propriedade **listeners** como `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Em **Advanced Kafka-Broker,** defina a propriedade **Security. inter. Broker. Protocol** como `SSL`

    ![Editando propriedades de configuração do Kafka SSL no Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Em **Custom Kafka-Broker,** defina a propriedade **SSL. Client. auth** como `required`. Esta etapa só será necessária se você estiver configurando a autenticação e a criptografia.

    ![Editando propriedades de configuração do Kafka SSL no Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Adicione novas propriedades de configuração ao ficheiro servidor.properties.

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Vá à UI de configuração de Ambari e verifique se as novas propriedades aparecem sob **o avançado kafka-env** e a propriedade do **modelo kafka-env.**

    ![Editando a propriedade de modelo Kafka-env em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

1. Reinicie todos os agentes Kafka.
1. Inicie o cliente administrador com as opções produtor e consumidor para verificar se os produtores e os consumidores estão trabalhando na porta 9093.

## <a name="client-setup-without-authentication"></a>Configuração do cliente (sem autenticação)

Se não necessitar de autenticação, o resumo dos passos para configurar apenas a encriptação SSL é:

1. Inscreva-se na AC (nó de cabeça ativa).
1. Copie o ca cert para a máquina cliente da máquina CA (wn0).
1. Inscreva-se na máquina cliente (hn1) e navegue para a pasta `~/ssl`.
1. Importar o ca cert para a loja de fiduciários.
1. Importar o ca cert para a loja de chaves.

Estes passos são detalhados nos seguintes fragmentos de código.

1. Inscreva-se no nó da AC.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Copiar o ca-cert para a máquina cliente

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Entre no computador cliente (nó de cabeçalho de espera).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importar o certificado de AC para a loja fiduciário.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importar o ca cert para keystore.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Crie o ficheiro `client-ssl-auth.properties`. Ele deve ter as seguintes linhas:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

## <a name="client-setup-with-authentication"></a>Configuração do cliente (com autenticação)

> [!Note]
> As etapas a seguir serão necessárias apenas se você estiver configurando a criptografia **e** a autenticação SSL. Se estiver apenas a configurar a encriptação, consulte a [configuração do Cliente sem autenticação.](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Os quatro passos seguintes resumem as tarefas necessárias para completar a configuração do cliente:

1. Entre no computador cliente (nó de cabeçalho de espera).
1. Crie um repositório de chaves Java e obtenha um certificado assinado para o agente. Em seguida, copie o certificado para a VM em que a autoridade de certificação está em execução.
1. Alterne para o computador da AC (nó de cabeçalho ativo) para assinar o certificado do cliente.
1. Vá para o computador cliente (nó de cabeçalho de espera) e navegue até a pasta `~/ssl`. Copie o certificado assinado no computador cliente.

Os detalhes de cada passo são dados abaixo.

1. Entre no computador cliente (nó de cabeçalho de espera).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Remova qualquer diretório ssl existente.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Crie uma keystore java e crie um pedido de assinatura de certificado. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Copiar o pedido de assinatura de certificado à AC

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Mude para a máquina CA (nó de cabeça ativa) e assine o certificado de cliente.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Copie o cliente assinado cert da AC (nó de cabeça ativa) para a máquina cliente.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Copiar o ca-cert para a máquina cliente

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Crie loja de clientes com cert assinado, e importe ca cert para a loja de chaves e loja de fiduciários:

    ```bash
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    ```

1. Criar um ficheiro `client-ssl-auth.properties`. Ele deve ter as seguintes linhas:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Verificação

> [!Note]
> Se o HDInsight 4.0 e o Kafka 2.1 estiverem instalados, pode utilizar o produtor/consumidores da consola para verificar a sua configuração. Caso contrário, mande o produtor Kafka no porto 9092 e envie mensagens para o tema e, em seguida, utilize o consumidor kafka no porto 9093 que utiliza sSL.

### <a name="kafka-21-or-above"></a>Kafka 2.1 ou superior

1. Crie um tópico se já não existir.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  Inicie o produtor de consolas e forneça o caminho para `client-ssl-auth.properties` como um ficheiro de configuração para o produtor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1.  Abra outra ligação ssh à máquina cliente e inicie o consumidor de consolas e forneça o caminho para `client-ssl-auth.properties` como um ficheiro de configuração para o consumidor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1.1

1. Crie um tópico se já não existir.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  Inicie o produtor de consolas e forneça o caminho para as propriedades cliente-ssl-auth.como um ficheiro de configuração para o produtor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

3.  Abra outra ligação ssh à máquina cliente e inicie o consumidor de consolas e forneça o caminho para `client-ssl-auth.properties` como um ficheiro de configuração para o consumidor.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Passos seguintes

* [O que é Apache Kafka no HDInsight?](apache-kafka-introduction.md)
