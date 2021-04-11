---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/12/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: 30451f237f4a6d42fee018d5e6c5adb3bbf022b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958026"
---
Inicie-se com os Serviços de Comunicação Azure utilizando os Serviços de Comunicação Java SMS SDK para enviar mensagens SMS.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](/java/azure/jdk/) versão 8 ou superior.
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
    <version>1.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Adicione a `azure-core-http-netty` dependência ao seu ficheiro **pom.xml.**

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.8.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core</artifactId>
    <version>1.13.0</version> <!-- {x-version-update;com.azure:azure-core;dependency} -->
</dependency>
```

Aberto **/src/main/java/com/communication/quickstart/App.java** num editor de texto, adicione diretivas de importação e remova a `System.out.println("Hello world!");` declaração:

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Azure Communication Services SMS SDK para Java.

| Nome                                                             | Descrição                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Esta classe cria o SmsClient. Fornece-lhe um ponto final, credencial e um cliente http. |
| SmsClient                    | Esta classe é necessária para toda a funcionalidade SMS. Usa-o para enviar mensagens SMS.                |
| Opções SmsSend               | Esta classe oferece opções para adicionar tags personalizadas e configurar relatórios de entrega. Se a entregaReportEnabled for definida como verdadeira, então um evento será emitido quando a entrega foi bem sucedida|                           |
| SmsSendResult                | Esta classe contém o resultado do serviço SMS.                                          |

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiizar um `SmsClient` com a sua cadeia de ligação. (Credencial é `Key` do portal Azure. Saiba como gerir a [cadeia de ligação do seu recurso.](../../create-communication-resource.md#store-your-connection-string)

Adicione o seguinte código ao método `main`:

```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .httpClient(httpClient)
                .buildClient();
```

Pode inicializar o cliente com qualquer cliente HTTP personalizado que implementa a `com.azure.core.http.HttpClient` interface. O código acima demonstra a utilização do [cliente Azure Core Netty HTTP](/java/api/overview/azure/core-http-netty-readme) que é fornecido por `azure-core` .

Também pode fornecer toda a cadeia de ligação utilizando a função connectionString() em vez de fornecer o ponto final e a chave de acesso. 
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .httpClient(httpClient)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>Envie uma mensagem SMS 1:1

Para enviar uma mensagem SMS a um único destinatário, ligue para o `send` método do SmsClient com um único número de telefone do destinatário. Pode também passar em parâmetros opcionais para especificar se o relatório de entrega deve ser ativado e definir etiquetas personalizadas.

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```

Deverá substituir `<from-phone-number>` por um número de telefone por SMS associado ao seu recurso de Serviços de Comunicação e `<to-phone-number>` por um número de telefone para o quais deseja enviar uma mensagem.

> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Envie uma mensagem SMS 1:N com opções
Para enviar uma mensagem SMS para uma lista de destinatários, ligue para o `send` método com uma lista de números de telefone do destinatário. Pode também passar em parâmetros opcionais para especificar se o relatório de entrega deve ser ativado e definir etiquetas personalizadas.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.send(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE);

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

Deverá substituir `<from-phone-number>` por um número de telefone por SMS associado ao seu recurso de Serviços de Comunicação `<to-phone-number-1>` e por `<to-phone-number-2>` números de telefone para o quais deseja enviar uma mensagem.

> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +14255550123).

O `setDeliveryReportEnabled` método é utilizado para configurar relatórios de entrega. Isto é útil para cenários em que pretende emitir eventos quando as mensagens SMS são entregues. Consulte o [Punho SMS Events](../handle-sms-events.md) quickstart para configurar relatórios de entrega para as suas mensagens SMS.

O `setTag` método é utilizado para aplicar uma etiqueta no Relatório de Entrega.

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