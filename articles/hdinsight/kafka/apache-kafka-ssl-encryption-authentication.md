---
title: Encriptação Apache Kafka TLS & autenticação - Azure HDInsight
description: Configurar a encriptação TLS para comunicação entre clientes Kafka e corretores Kafka, bem como entre corretores Kafka. Configurar a autenticação SSL dos clientes.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: b0154401a9233a6ea85a8e8c06ee14fcc918b2b6
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657086"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurar encriptação e autenticação TLS para Apache Kafka em Azure HDInsight

Este artigo mostra-lhe como configurar a encriptação Transport Layer Security (TLS), anteriormente conhecida como encriptação Secure Sockets Layer (SSL), entre clientes Apache Kafka e corretores Apache Kafka. Também mostra como configurar a autenticação dos clientes (por vezes referido como TLS bidirecional).

> [!Important]
> Existem dois clientes que pode utilizar para aplicações Kafka: um cliente Java e um cliente de consola. Apenas o `ProducerConsumer.java` cliente Java pode usar TLS tanto para produzir como consumir. O cliente `console-producer.sh` produtor de consolas não trabalha com a TLS.

> [!Note] 
> O produtor de consolaS HDInsight Kafka com a versão 1.1 não suporta o SSL.
## <a name="apache-kafka-broker-setup"></a>Configuração de corretor Apache Kafka

A configuração do corretor Kafka TLS utilizará quatro VMs de cluster HDInsight da seguinte forma:

* headnode 0 - Autoridade de Certificados (CA)
* nó de trabalhador 0, 1 e 2 - corretores

> [!Note] 
> Este guia utilizará certificados auto-assinados, mas a solução mais segura é utilizar certificados emitidos por AEm fidedignos.

O resumo do processo de configuração do corretor é o seguinte:

1. Repetem-se os seguintes passos em cada um dos três nós operários:

    1. Crie um certificado.
    1. Crie um pedido de assinatura cert.
    1. Envie o pedido de assinatura cert à Autoridade de Certificados (AC).
    1. Inscreva-se na AC e assine o pedido.
    1. SCP o certificado assinado de volta ao nó operário.
    1. SCP o certificado público da AC para o nó operário.

1. Assim que tiver todos os certificados, coloque os certificados na loja de certificados.
1. Vá a Ambari e mude as configurações.

Utilize as seguintes instruções detalhadas para completar a configuração do corretor:

> [!Important]
> No código seguinte, o wnX é uma abreviatura para um dos três nós operários `wn0` `wn1` e `wn2` deve ser substituído por, ou conforme apropriado. `WorkerNode0_Name`e `HeadNode0_Name` deve ser substituído pelos nomes das respetivas máquinas.

1. Execute a configuração inicial no nó de cabeça 0, que para o HDInsight preencherá o papel da Autoridade de Certificados (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Efetuar a mesma configuração inicial em cada um dos corretores (os nós dos trabalhadores 0, 1 e 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Em cada um dos nós dos trabalhadores, execute os seguintes passos utilizando o código de corte abaixo.
    1. Crie uma loja-chave e povoei-a com um novo certificado privado.
    1. Crie um pedido de assinatura de certificado.
    1. SCP o pedido de assinatura de certificado à AC (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Na máquina CA executa o seguinte comando para criar ficheiros ca-cert e ca-key:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Mude para a máquina ca e assine todos os pedidos de assinatura cert recebidos:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Envie os certificados assinados de volta para os nódosos operários da AC (headnode0).

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

1. Em cada nó de trabalhador, adicione o certificado público dos CAs à loja de fiduciários e à loja de chaves. Em seguida, adicione o próprio certificado assinado do nó de trabalhador à loja de chaves

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Atualizar a configuração kafka para usar TLS e reiniciar corretores

Criou agora cada corretor kafka com uma loja de chaves e uma loja de fidedignidade, e importou os certificados corretos. Em seguida, modificar propriedades de configuração kafka relacionadas usando Ambari e, em seguida, reiniciar os corretores Kafka.

Para completar a modificação de configuração, faça os seguintes passos:

1. Inscreva-se no portal Azure e selecione o seu cluster Azure HDInsight Apache Kafka.
1. Vá ao Ambari UI clicando em **ambari em casa** sob **dashboards cluster**.
1. Sob **Kafka Broker** definir a propriedade dos **ouvintes** para`PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Sob **avançado kafka-broker** definir a **propriedade security.inter.broker.protocol** para`SSL`

    ![Editar propriedades de configuração ssl kafka em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Sob o **custom kafka-broker** definir a propriedade **ssl.client.auth** para `required`. Este passo só é necessário se estiver a configurar a autenticação e a encriptação.

    ![Editar propriedades de configuração kafka ssl em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

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

    Para a versão 3.6 do HDI:

    ![Editar propriedade modelo kafka-env em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    Para a versão 4.0 do HDI:

     ![Editar propriedade modelo kafka-env em Ambari quatro](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)   

1. Reinicie todos os corretores Kafka.
1. Inicie o cliente administrativo com opções de produtor e consumidor para verificar se tanto os produtores como os consumidores estão a trabalhar no porto 9093.

## <a name="client-setup-without-authentication"></a>Configuração do cliente (sem autenticação)

Se não necessitar de autenticação, o resumo dos passos para configurar apenas a encriptação TLS é:

1. Inscreva-se na AC (nó de cabeça ativa).
1. Copie o ca cert para a máquina cliente da máquina CA (wn0).
1. Inscreva-se na máquina cliente (hn1) `~/ssl` e navegue para a pasta.
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

1. Inscreva-se na máquina cliente (nó de cabeça de espera).

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

1. Crie `client-ssl-auth.properties`o ficheiro . Deve ter as seguintes linhas:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

## <a name="client-setup-with-authentication"></a>Configuração do cliente (com autenticação)

> [!Note]
> Os seguintes passos só são necessários se estiver a configurar a encriptação TLS **e** a autenticação. Se estiver apenas a configurar a encriptação, consulte a [configuração do Cliente sem autenticação.](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Os quatro passos seguintes resumem as tarefas necessárias para completar a configuração do cliente:

1. Inscreva-se na máquina cliente (nó de cabeça de espera).
1. Crie uma loja de chaves java e obtenha um certificado assinado para o corretor. Em seguida, copie o certificado para o VM onde a AC está em execução.
1. Mude para a máquina CA (nó de cabeça ativa) para assinar o certificado de cliente.
1. Vá à máquina cliente (nó de cabeça de `~/ssl` espera) e navegue para a pasta. Copie o certificado assinado para a máquina cliente.

Os detalhes de cada passo são dados abaixo.

1. Inscreva-se na máquina cliente (nó de cabeça de espera).

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

1. Criar um `client-ssl-auth.properties`ficheiro. Deve ter as seguintes linhas:

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
> Se o HDInsight 4.0 e o Kafka 2.1 estiverem instalados, pode utilizar o produtor/consumidores da consola para verificar a sua configuração. Caso contrário, mande o produtor Kafka no porto 9092 e envie mensagens para o tema e, em seguida, utilize o consumidor kafka no porto 9093 que utiliza TLS.

### <a name="kafka-21-or-above"></a>Kafka 2.1 ou superior

1. Crie um tópico se já não existir.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Inicie o produtor de `client-ssl-auth.properties` consolas e forneça o caminho para como um ficheiro de configuração para o produtor.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Abra outra ligação ssh à máquina cliente e `client-ssl-auth.properties` inicie o consumidor de consolas e forneça o caminho para como um ficheiro de configuração para o consumidor.

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

1. Abra outra ligação ssh à máquina cliente e `client-ssl-auth.properties` inicie o consumidor de consolas e forneça o caminho para como um ficheiro de configuração para o consumidor.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Passos seguintes

* [O que é o Apache Kafka no HDInsight?](apache-kafka-introduction.md)
