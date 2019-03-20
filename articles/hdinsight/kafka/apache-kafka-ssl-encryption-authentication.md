---
title: Configurar a encriptação SSL e autenticação para o Apache Kafka no HDInsight do Azure
description: Configure a encriptação SSL para comunicação entre clientes de Kafka e mediadores Kafka, bem como entre mediadores Kafka. Configure a autenticação de SSL de clientes.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: cd850808f656b211bf1fdbfd2fe7c5799150b030
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57839666"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurar a encriptação Secure Sockets Layer (SSL) e a autenticação para o Apache Kafka no HDInsight do Azure

Este artigo descreve como configurar a encriptação SSL entre clientes do Apache Kafka e Apache Kafka mediadores. Também apresenta os passos necessários para configurar a autenticação de clientes (por vezes referido como SSL bidirecional).

## <a name="server-setup"></a>Configuração do servidor

A primeira etapa é criar um keystore e truststore em cada mediador Kafka. Depois destes valores são criados, importe os certificados de autoridade de certificado (AC) e do Mediador para estes arquivos.

> [!Note] 
> Este guia irá utilizar certificados autoassinados, mas a solução mais segura consiste em utilizar certificados emitidos pela AC fidedignas.

Faça o seguinte para concluir a configuração de servidor:

1. Crie uma pasta denominada ssl e exportar a palavra-passe do servidor como uma variável de ambiente. Para o resto deste guia, substitua `<server_password>` com a palavra-passe de administrador real para o servidor.
1. Em seguida, crie um keystore de java (kafka.server.keystore.jks) e um certificado de AC.
1. Em seguida, crie um pedido de assinatura para obter o certificado que criou no passo anterior assinado pela AC.
1. Agora, enviar o pedido de assinatura para a AC e obter este certificado assinado. Uma vez que estamos a utilizar um certificado autoassinado, podemos assinar o certificado com a nossa utilização de AC a `openssl` comando.
1. Criar um arquivo de confiança e importar o certificado da AC.
1. Importe o certificado de AC pública para o keystore.
1. Importe o certificado autoassinado para o keystore.

Os comandos para concluir estes passos são apresentados no seguinte fragmento de código.

```bash
export SRVPASS=<server_password>
mkdir ssl
cd ssl

# Create a java keystore (kafka.server.keystore.jks) and a CA certificate.

keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12

# Create a signing request to get the certificate created in the previous step signed by the CA.

keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS

# Send the signing request to the CA and get this certificate signed.

openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS

# Create a trust store and import the certificate of the CA.

keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the public CA certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the signed certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# The output should say "Certificate reply was added to keystore"
```

Importar o certificado autoassinado para o keystore é a etapa final necessária para configurar o truststore e keystore para um mediador Kafka.

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Atualizar a configuração do Kafka para utilizar SSL e reiniciar mediadores

Tem agora a configuração cada Kafka mediador com um keystore e truststore e importar os certificados corretos.  Em seguida, modificar propriedades de configuração relacionadas do Kafka com o Ambari e, em seguida, reinicie mediadores Kafka. 

Para concluir a modificação da configuração, siga os passos abaixo:

1. Inicie sessão no portal do Azure e selecione o cluster Azure HDInsight Apache Kafka.
1. Aceda à IU do Ambari clicando **Ambari doméstica** sob **dashboards de clusters**.
1. Sob **mediador Kafka** definir o **serviços de escuta** propriedade para `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Sob **Advanced mediador kafka** definir o **security.inter.broker.protocol** propriedade para `SSL`

    ![Editar propriedades de configuração de ssl de Kafka em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Sob **mediador kafka personalizada** definir o **ssl.client.auth** propriedade `required`. Este passo só é necessário se estiver a configurar a autenticação, bem como a encriptação.

    ![Editar propriedades de configuração de ssl de kafka em Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Adicionar propriedades de configuração para o Kafka `server.properties` ficheiro para anunciar os endereços IP em vez do nome completamente qualificado domínio (FQDN).

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Para verificar se foram efetuadas as alterações anteriores corretamente, opcionalmente, pode verificar se as seguintes linhas estão presentes no Kafka `server.properties` ficheiro.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. Reinicie todos os mediadores de Kafka.

## <a name="client-setup-with-authentication"></a>Configuração do cliente (com a autenticação)

> [!Note]
> Os seguintes passos são necessários apenas se estiver a configurar a encriptação SSL ambas **e** autenticação. Se estiver a configurar apenas encriptação, prossiga para [configuração do cliente sem autenticação](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Siga os passos abaixo para concluir a configuração de cliente:

1. Inicie sessão computador do cliente (hn1).
1. Exporte a palavra-passe de cliente. Substitua `<client_password>` com a palavra-passe de administrador real no computador de cliente do Kafka.
1. Crie um java KeyStore se e obtenha um certificado assinado para o mediador. Em seguida, copie o certificado para a VM onde a AC está em execução.
1. Mude para a máquina de AC (wn0) para assinar os certificados de cliente.
1. Vá para a máquina de cliente (hn1) e navegue para o `~/ssl` pasta. Copie o certificado autoassinado para o computador cliente.

```bash
export CLIPASS=<client_password>
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

# Copy the cert to the vm where the CA is
scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request

# Switch to the CA machine (wn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

Por último, ver o ficheiro `client-ssl-auth.properties` com o comando `cat client-ssl-auth.properties`. Ele deve ter as seguintes linhas:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=<client_password>
ssl.key.password=<client_password>
```

## <a name="client-setup-without-authentication"></a>Configuração do cliente (sem autenticação)

Se não precisar de autenticação, são os passos para configurar apenas a criptografia SSL:

1. Inicie sessão no computador cliente (hn1) e navegue para o `~/ssl` pasta
1. Exporte a palavra-passe de cliente. Substitua `<client_password>` com a palavra-passe de administrador real no computador de cliente do Kafka.
1. Copie o certificado autoassinado para o computador cliente da máquina de AC (wn0).
1. Importar o certificado de AC para o truststore
1. Importar o certificado de AC para o keystore

Essas etapas são mostradas no seguinte fragmento de código.

```bash
export CLIPASS=<client_password>
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

Por fim, ver o ficheiro `client-ssl-auth.properties` com o comando `cat client-ssl-auth.properties`. Ele deve ter as seguintes linhas:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
```

## <a name="next-steps"></a>Passos Seguintes

* [O que é o Apache Kafka no HDInsight?](apache-kafka-introduction.md)
