---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: cb8e6934125630590a337ed7bf7f4c81b2b73bb3
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915506"
---
Inicie-se com os Serviços de Comunicação Azure utilizando a biblioteca de clientes de SMS java para enviar mensagens SMS.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) versão 8 ou superior.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um número de telefone por SMS habilitado. [Obter um número de telefone.](../get-phone-number.md)

### <a name="prerequisite-check"></a>Verificação pré-requisito

- Numa janela de terminal ou de comando, corra `mvn -v` para verificar se o maven está instalado.
- Para visualizar os números de telefone associados ao seu recurso serviços de comunicação, inscreva-se no [portal Azure,](https://portal.azure.com/)localize o seu recurso de Serviços de Comunicação e abra o separador **números** de telefone a partir do painel de navegação à esquerda.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-java-application"></a>Criar uma nova aplicação Java

Abra a sua janela de terminal ou comando e navegue para o diretório onde pretende criar a sua aplicação Java. Executar o comando abaixo para gerar o projeto Java a partir do modelo maven-arqueotype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

O objetivo de 'gerar' criará um diretório com o mesmo nome que o artefactoId. Sob este diretório, o **diretório src/main/java** contém o código fonte do projeto, o **diretório src/teste/java** contém a fonte de teste, e o ficheiro **pom.xml** é o Project Object Model do projeto, ou POM.

### <a name="install-the-package"></a>Instale o pacote

Abra o ficheiro **pom.xml** no seu editor de texto. Adicione o seguinte elemento de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.3</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Adicione a `azure-core-http-netty` dependência ao seu ficheiro **pom.xml.**

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

Aberto **/src/main/java/com/communication/quickstart/App.java** num editor de texto, adicione diretivas de importação e remova a `System.out.println("Hello world!");` declaração:

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas e interfaces lidam com algumas das principais características da biblioteca de clientes dos Serviços de Comunicação Azure para Java.

| Nome                                                             | Descrição                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Esta classe cria o SmsClient. Fornece-lhe um ponto final, credencial e um cliente http. |
| SmsClient                    | Esta classe é necessária para toda a funcionalidade SMS. Usa-o para enviar mensagens SMS.                |
| Enviar MensagensResponse               | Esta classe contém a resposta do serviço SMS.                                          |
| PhoneNumber                   | Esta classe contém informações sobre números de telefone

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiizar um `SmsClient` com a sua cadeia de ligação. O código abaixo recupera o ponto final e as cordas credenciais para o recurso a partir de variáveis ambientais nomeadas `COMMUNICATION_SERVICES_ENDPOINT_STRING` e `COMMUNICATION_SERVICES_CREDENTIAL_STRING` (Credencial é `Key` o do portal Azure. Saiba como gerir a [cadeia de ligação dos recursos.](../../create-communication-resource.md#store-your-connection-string)

Adicione o seguinte código ao método `main`:

```java
// Your can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .accessKey(accessKey)
    .httpClient(httpClient)
    .buildClient();
```

Pode inicializar o cliente com qualquer cliente HTTP personalizado que implementa a `com.azure.core.http.HttpClient` interface. O código acima demonstra a utilização do [cliente Azure Core Netty HTTP](/java/api/overview/azure/core-http-netty-readme?preserve-view=true&view=azure-java-stable) que é fornecido por `azure-core` .

Também pode fornecer toda a cadeia de ligação utilizando a função connectionString() em vez de fornecer o ponto final e a chave de acesso. 
```java
// Your can find your connection string from your resource in the Azure Portal
String connectionString = "<connection_string>";
SmsClient client = new SmsClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="send-an-sms-message"></a>Enviar uma mensagem SMS

Envie uma mensagem SMS ligando para o método de envio de mensagens. Adicione este código ao fim do `main` método:

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

Deverá substituir `<leased-phone-number>` por um número de telefone por SMS associado ao seu recurso de Serviços de Comunicação e `<to-phone-number>` pelo número de telefone a que deseja enviar uma mensagem.

O `enableDeliveryReport` parâmetro é um parâmetro opcional que pode utilizar para configurar relatórios de entrega. Isto é útil para cenários em que pretende emitir eventos quando as mensagens SMS são entregues. Consulte o [Punho SMS Events](../handle-sms-events.md) quickstart para configurar relatórios de entrega para as suas mensagens SMS.

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

## <a name="run-the-code"></a>Executar o código

Navegue para o diretório que contém o ficheiro **pom.xml** e compile o projeto usando o `mvn` comando.

```console

mvn compile

```

Então, construa o pacote.

```console

mvn package

```

Execute o seguinte `mvn` comando para executar a aplicação.

```console

mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false

```