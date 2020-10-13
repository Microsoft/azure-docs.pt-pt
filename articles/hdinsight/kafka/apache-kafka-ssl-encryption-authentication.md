---
title: Encriptação Apache Kafka TLS & autenticação - Azure HDInsight
description: Configurar encriptação TLS para comunicação entre clientes Kafka e corretores Kafka, bem como entre corretores Kafka. Configurar a autenticação SSL dos clientes.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: 9a95970647a26ea80db9f63fb8523c6a65cc5e06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86082085"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurar encriptação e autenticação TLS para Apache Kafka em Azure HDInsight

Este artigo mostra-lhe como configurar a encriptação de Segurança da Camada de Transporte (TLS), anteriormente conhecida como Secure Sockets Layer (SSL), entre clientes Apache Kafka e corretores Apache Kafka. Também mostra como configurar a autenticação de clientes (por vezes referido como TLS bidirecional).

> [!Important]
> Existem dois clientes que pode utilizar para aplicações Kafka: um cliente Java e um cliente de consola. Apenas o cliente Java `ProducerConsumer.java` pode usar TLS para produzir e consumir. O cliente produtor de consola `console-producer.sh` não trabalha com a TLS.

> [!Note]
> O produtor de consolas HDInsight Kafka com a versão 1.1 não suporta sSL.

## <a name="apache-kafka-broker-setup"></a>Configuração do corretor Apache Kafka

A configuração do corretor Kafka TLS utilizará quatro VMs de cluster HDInsight da seguinte forma:

* headnode 0 - Autoridade de Certificados (CA)
* nó 0, 1 e 2 - corretores

> [!Note] 
> Este guia utilizará certificados auto-assinados, mas a solução mais segura é utilizar certificados emitidos por CAs fidedignos.

O resumo do processo de configuração do corretor é o seguinte:

1. Repetem-se os seguintes passos em cada um dos três nós do trabalhador:

    1. Crie um certificado.
    1. Crie um pedido de assinatura de certificado.
    1. Envie o pedido de assinatura do certificado à Autoridade de Certificados (CA).
    1. Inscreva-se na AC e assine o pedido.
    1. SCP o certificado assinado de volta para o nó do trabalhador.
    1. SCP o certificado público da AC para o nó do trabalhador.

1. Assim que tiver todos os certificados, coloque os certificados na loja de certificados.
1. Vá a Ambari e altere as configurações.

Utilize as seguintes instruções detalhadas para completar a configuração do corretor:

> [!Important]
> No código seguinte, o WNX é uma abreviatura para um dos três nós operários e deve ser substituído por `wn0` , `wn1` ou conforme `wn2` apropriado. `WorkerNode0_Name` e `HeadNode0_Name` deve ser substituído pelos nomes das respetivas máquinas.

1. Executar a configuração inicial no nó de cabeça 0, que para o HDInsight preencherá o papel da Autoridade de Certificados (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Execute a mesma configuração inicial em cada um dos corretores (nós de trabalhador 0, 1 e 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Em cada um dos nós do trabalhador, execute os seguintes passos utilizando o corte de código abaixo.
    1. Crie uma loja de chaves e povoe-a com um novo certificado privado.
    1. Crie um pedido de assinatura de certificado.
    1. SCP o pedido de assinatura do certificado para a AC (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Na máquina CA execute o seguinte comando para criar ficheiros ca-cert e ca-key:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Altere para a máquina de CA e assine todos os pedidos de assinatura do certificado recebido:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Envie os certificados assinados de volta para os nóns do trabalhador da AC (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Envie o certificado público da AC para cada nó de trabalhador.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Em cada nó de trabalhador, adicione o certificado público de CAs à loja de fisiológica e à loja de chaves. Em seguida, adicione o certificado assinado do próprio nó operário à teclaria

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Atualizar a configuração kafka para usar TLS e reiniciar corretores

Você agora criou cada corretor Kafka com uma loja de chaves e loja de confiança, e importou os certificados corretos. Em seguida, modifique propriedades de configuração kafka relacionadas usando Ambari e, em seguida, reinicie os corretores Kafka.

Para completar a modificação de configuração, faça os seguintes passos:

1. Inscreva-se no portal Azure e selecione o seu cluster Apache Kafka Azure HDInsight.
1. Vá ao Ambari UI clicando em **casa de Ambari** sob **os dashboards cluster.**
1. Sob **Kafka Broker** definir a propriedade **dos ouvintes** para `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Sob **o advanced kafka-broker** definir a propriedade **security.inter.broker.protocol** para `SSL`

    ![Edição de propriedades de configuração ssl kafka em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Sob **o costume kafka-broker** definir a propriedade **ssl.client.auth** para `required` . Este passo só é necessário se estiver a configurar a autenticação e a encriptação.

    ![Edição de propriedades de configuração de ssl kafka em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Para a versão HDI 3.6, vá à UI Ambari e adicione as seguintes configurações em **Kafka-env Avançado** e na propriedade **de modelo kafka-env.**

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

1. Aqui está a imagem que mostra a configuração de Ambari UI com estas alterações.

    Para a versão HDI 3.6:

    ![Edição da propriedade do modelo kafka-env em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    Para a versão HDI 4.0:

     ![Edição da propriedade do modelo kafka-env em Ambari quatro](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)

1. Reinicie todos os corretores Kafka.

## <a name="client-setup-without-authentication"></a>Configuração do cliente (sem autenticação)

Se não necessitar de autenticação, o resumo dos passos para configurar apenas a encriptação TLS são:

1. Inscreva-se na AC (nó de cabeça ativa).
1. Copie o certificado ca para a máquina de cliente da máquina CA (wn0).
1. Inscreva-se na máquina do cliente (hn1) e navegue para a `~/ssl` pasta.
1. Importe o certificado de CA para a loja de confiança.
1. Importe o certificado de CA para a loja de chaves.

Estes passos são detalhados nos seguintes fragmentos de código.

1. Inscreva-se no nó de ca.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Copie o ca-cert para a máquina do cliente

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Inscreva-se na máquina do cliente (nó de cabeça de espera).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importe o certificado de CA para a loja de confiança.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importe o certificado de CA para keystore.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Criar o ficheiro `client-ssl-auth.properties` na máquina do cliente (hn1) . Deve ter as seguintes linhas:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Inicie o cliente administrativo com opções de produtor e consumidor para verificar se tanto os produtores como os consumidores estão a trabalhar no porto 9093. Consulte a secção [de verificação](apache-kafka-ssl-encryption-authentication.md#verification) abaixo para verificar as etapas necessárias para verificar a configuração utilizando o produtor/consumidor da consola.

## <a name="client-setup-with-authentication"></a>Configuração do cliente (com autenticação)

> [!Note]
> Os seguintes passos só são necessários se estiver a configurar a encriptação **e** a autenticação TLS. Se estiver apenas a configurar a encriptação, consulte [a configuração do Cliente sem autenticação](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

Os quatro passos seguintes resumem as tarefas necessárias para completar a configuração do cliente:

1. Inscreva-se na máquina do cliente (nó de cabeça de espera).
1. Crie uma loja java e obtenha um certificado assinado para o corretor. Em seguida, copie o certificado para o VM onde a AC está em execução.
1. Mude para a máquina CA (nó de cabeça ativa) para assinar o certificado de cliente.
1. Vá à máquina do cliente (nó de cabeça de espera) e navegue para a `~/ssl` pasta. Copie o certificado assinado para a máquina do cliente.

Os detalhes de cada passo são dados abaixo.

1. Inscreva-se na máquina do cliente (nó de cabeça de espera).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Remova qualquer diretório de ssl existente.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Crie uma loja java keystore e crie um pedido de assinatura de certificado. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Copiar o pedido de assinatura do certificado para a AC

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Mude para a máquina CA (nó de cabeça ativa) e assine o certificado de cliente.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Copiar o certificado do cliente assinado da AC (nó de cabeça ativa) para a máquina do cliente.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Copie o ca-cert para a máquina do cliente

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. Inscreva-se na máquina do cliente (nó de cabeça de espera) e navegue para o diretório ssl.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Crie uma loja de clientes com certificado assinado e importe ca cert na loja de chaves e na loja de confiança na máquina cliente (hn1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Criar um ficheiro `client-ssl-auth.properties` na máquina do cliente (hn1) . Deve ter as seguintes linhas:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Verificação

Passa estes passos na máquina do cliente.

> [!Note]
> Se o HDInsight 4.0 e o Kafka 2.1 estiverem instalados, pode utilizar o produtor/consumidores da consola para verificar a sua configuração. Caso contrário, gere o produtor Kafka no porto 9092 e envie mensagens para o tema e, em seguida, utilize o consumidor kafka no porto 9093 que utiliza o TLS.

### <a name="kafka-21-or-above"></a>Kafka 2.1 ou superior

1. Crie um tópico se já não existir.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Inicie o produtor de consolas e forneça o caminho para `client-ssl-auth.properties` como um ficheiro de configuração para o produtor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Abra outra ligação ssh à máquina cliente e inicie o consumidor de consolas e forneça o caminho para `client-ssl-auth.properties` como um ficheiro de configuração para o consumidor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1.1

1. Crie um tópico se já não existir.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Inicie o produtor de consolas e forneça o caminho para as propriedades cliente-ssl-auth.como um ficheiro de configuração para o produtor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Abra outra ligação ssh à máquina cliente e inicie o consumidor de consolas e forneça o caminho para `client-ssl-auth.properties` como um ficheiro de configuração para o consumidor.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Passos seguintes

* [O que é o Apache Kafka no HDInsight?](apache-kafka-introduction.md)
