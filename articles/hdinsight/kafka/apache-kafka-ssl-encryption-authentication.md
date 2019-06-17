---
title: Configurar a encriptação SSL e autenticação para o Apache Kafka no HDInsight do Azure
description: Configure a encriptação SSL para comunicação entre clientes de Kafka e mediadores Kafka, bem como entre mediadores Kafka. Configure a autenticação de SSL de clientes.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 5d567074a0038915cc43a585b34c9c71ccf3eb1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65464986"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurar a encriptação Secure Sockets Layer (SSL) e autenticação para o Apache Kafka no HDInsight do Azure

Este artigo mostra-lhe como configurar a encriptação SSL entre clientes do Apache Kafka e Apache Kafka mediadores. Ele também mostra como configurar a autenticação de clientes (por vezes referido como SSL bidirecional).

> [!Important]
> Existem dois clientes que pode usar para aplicativos de Kafka: um cliente de Java e um cliente da consola. Apenas o cliente de Java `ProducerConsumer.java` pode utilizar o SSL para produzir e consumir. O cliente de produtor consola `console-producer.sh` não funciona com SSL.

## <a name="apache-kafka-broker-setup"></a>Configuração do Mediador do Apache Kafka

A configuração de Mediador Kafka SSL usará quatro VMs do cluster do HDInsight, da seguinte forma:

* nó principal 0 - certificado de autoridade (CA)
* mediadores de nó de trabalho, 0, 1 e 2-

> [!Note] 
> Este guia irá utilizar certificados autoassinados, mas a solução mais segura consiste em utilizar certificados emitidos pela AC fidedignas.

O resumo do processo de configuração mediador é o seguinte:

1. Os seguintes passos são repetidos em cada um de nós de trabalho de três:

    1. Gere um certificado.
    1. Crie um pedido de assinatura de certificado.
    1. Envie o pedido para a autoridade de certificado (AC) de assinatura de certificado.
    1. Inicie sessão para a AC e assinar o pedido.
    1. SCP certificado assinado novamente para o nó de trabalho.
    1. SCP do certificado público da autoridade de certificação para o nó de trabalho.

1. Assim que tiver todos os certificados, colocados os certificados no arquivo de certificados.
1. Vá para Ambari e alterar as configurações.

Utilize as seguintes instruções detalhadas para concluir a configuração do Mediador:

> [!Important]
> Os seguintes fragmentos de código wnX é uma abreviatura de um de nós de trabalho de três e deve ser substituída pelo `wn0`, `wn1` ou `wn2` conforme adequado. `WorkerNode0_Name` e `HeadNode0_Name` devem ser substituídos com os nomes das respetivas máquinas, tais como `wn0-abcxyz` ou `hn0-abcxyz`.

1. Execute a configuração inicial no nó principal 0, que, para o HDInsight, irá preencher a função de certificado de autoridade (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Execute a configuração inicial do mesmo em cada um dos brokers (nós de trabalho 0, 1 e 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Em cada um de nós de trabalho, execute os seguintes passos usando o trecho de código abaixo.
    1. Crie um keystore e preenchê-lo com um novo certificado privado.
    1. Crie um pedido de assinatura de certificado.
    1. SCP o pedido de assinatura de certificado à AC (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Altere para a máquina de AC e assinar todos o certificado recebido pedidos de assinatura:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Envie os certificados autoassinados para os nós de trabalho da AC (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Envie o certificado público da AC para cada nó de trabalho.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Em cada nó de trabalho, adicione o certificado público de CAs ao truststore e keystore. Em seguida, adicione o certificado assinado do nó de trabalho para o keystore

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Atualizar a configuração do Kafka para utilizar SSL e reiniciar mediadores

Tem agora configurar cada mediador Kafka com um keystore e truststore e importar os certificados corretos. Em seguida, modificar propriedades de configuração relacionadas do Kafka com o Ambari e, em seguida, reinicie mediadores Kafka.

Para concluir a modificação da configuração, siga os passos abaixo:

1. Inicie sessão no portal do Azure e selecione o cluster Azure HDInsight Apache Kafka.
1. Aceda à IU do Ambari clicando **Ambari doméstica** sob **dashboards de clusters**.
1. Sob **mediador Kafka** definir o **serviços de escuta** propriedade para `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Sob **Advanced mediador kafka** definir o **security.inter.broker.protocol** propriedade para `SSL`

    ![Editar propriedades de configuração de ssl de Kafka em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Sob **mediador kafka personalizada** definir o **ssl.client.auth** propriedade `required`. Este passo só é necessário se estiver a configurar a autenticação e encriptação.

    ![Editar propriedades de configuração de ssl de kafka em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Adicionar propriedades de configuração para o Kafka `server.properties` ficheiro para anunciar os endereços IP em vez do nome completamente qualificado domínio (FQDN).

    ```bash
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

1. Para verificar se foram efetuadas as alterações anteriores corretamente, opcionalmente, pode verificar se as seguintes linhas estão presentes no Kafka `server.properties` ficheiro.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

1. Reinicie todos os mediadores de Kafka.
1. Inicie o cliente administrativo com opções de produtor e consumidor para verificar que os produtores e consumidores estão a funcionar na porta 9093.

## <a name="client-setup-with-authentication"></a>Configuração do cliente (com a autenticação)

> [!Note]
> Os seguintes passos são necessários apenas se estiver a configurar a encriptação SSL ambas **e** autenticação. Se estiver a configurar apenas encriptação, prossiga para [configuração do cliente sem autenticação](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Conclua os seguintes passos para concluir a configuração de cliente:

1. Inicie sessão no computador cliente (hn1).
1. Crie um java KeyStore se e obtenha um certificado assinado para o mediador. Em seguida, copie o certificado para a VM onde a AC está em execução.
1. Mude para a máquina de AC (hn0) para assinar os certificados de cliente.
1. Vá para a máquina de cliente (hn1) e navegue para o `~/ssl` pasta. Copie o certificado autoassinado para o computador cliente.

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Por último, ver o ficheiro `client-ssl-auth.properties` com o comando `cat client-ssl-auth.properties`. Ele deve ter as seguintes linhas:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Configuração do cliente (sem autenticação)

Se não precisar de autenticação, os passos para configurar a encriptação de SSL apenas são:

1. Inicie sessão no computador cliente (hn1) e navegue para o `~/ssl` pasta
1. Copie o certificado autoassinado para o computador cliente da máquina de AC (wn0).
1. Importar o certificado de AC para o truststore
1. Importar o certificado de AC para o keystore

Essas etapas são mostradas no seguinte fragmento de código.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Por fim, ver o ficheiro `client-ssl-auth.properties` com o comando `cat client-ssl-auth.properties`. Ele deve ter as seguintes linhas:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Passos Seguintes

* [O que é o Apache Kafka no HDInsight?](apache-kafka-introduction.md)
