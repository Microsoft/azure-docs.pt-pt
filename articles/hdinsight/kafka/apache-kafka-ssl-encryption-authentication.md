---
title: Configurar a criptografia e a autenticação SSL para Apache Kafka no Azure HDInsight
description: Configure a criptografia SSL para a comunicação entre os clientes Kafka e os agentes Kafka, bem como entre os agentes Kafka. Configurar a autenticação SSL de clientes.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: d555c51838f3595367e931341a3cf6161857faef
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554598"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurar a criptografia e autenticação do protocolo SSL (SSL) para Apache Kafka no Azure HDInsight

Este artigo mostra como configurar a criptografia SSL entre Apache Kafka clientes e Apache Kafka agentes. Ele também mostra como configurar a autenticação de clientes (às vezes chamados de SSL bidirecional).

> [!Important]
> Há dois clientes que você pode usar para aplicativos Kafka: um cliente Java e um cliente de console. Somente o cliente Java `ProducerConsumer.java` pode usar SSL para produção e consumo. O cliente do produtor do console `console-producer.sh` não funciona com SSL.

## <a name="apache-kafka-broker-setup"></a>Instalação do Apache Kafka Broker

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
> Nos trechos de código a seguir, wnX é uma abreviação de um dos três nós de trabalho e deve ser substituído por `wn0`, `wn1` ou `wn2`, conforme apropriado. `WorkerNode0_Name` e `HeadNode0_Name` devem ser substituídos pelos nomes dos respectivos computadores, como `wn0-abcxyz` ou `hn0-abcxyz`.

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

1. Em **Advanced Kafka-env,** adicione as seguintes linhas ao final da propriedade de **modelo Kafka-env** .

    ```config
    # Needed to configure IP address advertising
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

    ![Editando a propriedade de modelo Kafka-env em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

1. Reinicie todos os agentes Kafka.
1. Inicie o cliente administrador com as opções produtor e consumidor para verificar se os produtores e os consumidores estão trabalhando na porta 9093.

## <a name="client-setup-with-authentication"></a>Configuração do cliente (com autenticação)

> [!Note]
> As etapas a seguir serão necessárias apenas se você estiver configurando a criptografia **e** a autenticação SSL. Se você estiver configurando apenas a criptografia, vá para a [configuração do cliente sem autenticação](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Conclua as seguintes etapas para concluir a configuração do cliente:

1. Entre no computador cliente (hn1).
1. Crie um repositório de chaves Java e obtenha um certificado assinado para o agente. Em seguida, copie o certificado para a VM em que a autoridade de certificação está em execução.
1. Alterne para o computador da CA (hn0) para assinar o certificado do cliente.
1. Vá para o computador cliente (hn1) e navegue até a pasta `~/ssl`. Copie o certificado assinado no computador cliente.

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

Por fim, exiba o arquivo `client-ssl-auth.properties` com o comando `cat client-ssl-auth.properties`. Ele deve ter as seguintes linhas:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Configuração do cliente (sem autenticação)

Se você não precisar de autenticação, as etapas para configurar somente a criptografia SSL serão:

1. Entre no computador cliente (hn1) e navegue até a pasta `~/ssl`
1. Copie o certificado assinado no computador cliente do computador da CA (WN0).
1. Importar o certificado de autoridade de certificação para o trustStore
1. Importar o certificado de autoridade de certificação para o repositório de chaves

Essas etapas são mostradas no trecho de código a seguir.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Por fim, exiba o arquivo `client-ssl-auth.properties` com o comando `cat client-ssl-auth.properties`. Ele deve ter as seguintes linhas:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Passos seguintes

* [O que é Apache Kafka no HDInsight?](apache-kafka-introduction.md)
