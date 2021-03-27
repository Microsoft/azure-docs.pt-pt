---
ms.openlocfilehash: 06033f13b1f63849408646835a844c2eafa8d64f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629418"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) versão 8 ou superior.
- [Apache Maven.-](https://maven.apache.org/download.cgi)Um recurso de serviços de comunicação implantados e uma cadeia de conexão. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-java-application"></a>Criar uma nova aplicação Java

Abra o terminal ou a janela de comando. Navegue para o diretório onde gostaria de criar a sua aplicação Java. Executar o comando abaixo para gerar o projeto Java a partir do modelo maven-arqueotype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Você vai notar que a tarefa 'gerar' criou um diretório com o mesmo nome que o `artifactId` . Sob este diretório, o diretório src/main/java contém o código fonte do projeto, `src/test/java directory` contém a fonte de teste, e o ficheiro é o Project Object Model do `pom.xml` projeto, ou POM.

### <a name="install-the-package"></a>Instale o pacote

Abra o ficheiro **pom.xml** no seu editor de texto. Adicione os seguintes elementos de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-phonenumbers</artifactId>
    <version>1.0.0-beta.6</version>
</dependency>

<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Navegue para o *diretório /src/main/java/com/communication/quickstart*
1. Abra o ficheiro *.java App* no seu editor
1. Substitua a `System.out.println("Hello world!");` declaração
1. Adicionar `import` diretivas

Utilize o seguinte código para começar:

```java
import com.azure.communication.phonenumbers.*;
import com.azure.communication.phonenumbers.models.*;
import java.io.*;
import com.azure.core.http.*;
import com.azure.core.http.netty.*;
import com.azure.core.util.Context;
import com.azure.core.util.polling.PollResponse;
import com.azure.identity.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Phone Numbers Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-phone-numbers-client"></a>Autenticar o Cliente números de telefone

O PhoneNumberClientBuilder está habilitado a utilizar a autenticação do Diretório Ativo Azure
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L52-L62 -->
```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .endpoint(endpoint)
    .credential(new DefaultAzureCredentialBuilder().build())
    .httpClient(httpClient)
    .buildClient();
```

Em alternativa, é também posível utilizar o ponto final e a chave de acesso do recurso de comunicação para autenticar.
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L30-L41 -->
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<ACCESS_KEY>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="manage-phone-numbers"></a>Gerir números de telefone

### <a name="search-for-available-phone-numbers"></a>Pesquisa rumo a números de telefone disponíveis

Para comprar números de telefone, primeiro deve procurar números de telefone disponíveis. Para procurar números de telefone, forneça o código de área, o tipo de atribuição, [as capacidades de número de telefone,](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services) [o tipo de número de telefone](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)e a quantidade. Note que para o tipo de número de telefone gratuito, desde que o código de área seja opcional.

```java
 PhoneNumberCapabilities capabilities = new PhoneNumberCapabilities()
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND_OUTBOUND);
PhoneNumberSearchOptions searchOptions = new PhoneNumberSearchOptions().setAreaCode("833").setQuantity(1);

PhoneNumberSearchResult searchResult = phoneNumberClient
    .beginSearchAvailablePhoneNumbers("US", PhoneNumberType.TOLL_FREE, PhoneNumberAssignmentType.APPLICATION, capabilities,  searchOptions, Context.NONE)
    .getFinalResult();

System.out.println("Searched phone numbers: " + searchResult.getPhoneNumbers());
System.out.println("Search expires by: " + searchResult.getSearchExpiresBy());
System.out.println("Phone number costs:" + searchResult.getCost().getAmount());
```

### <a name="purchase-phone-numbers"></a>Comprar Números de Telefone

O resultado da procura de números de telefone é um PhoneNumberSearchResult. Isto contém um `searchId` que pode ser passado para os números de compra API para adquirir os números na pesquisa. Note que ligar para os números de telefone de compra API resultará num encargo para a sua Conta Azure.

```java
PollResponse<PhoneNumberOperation> purchaseResponse = phoneNumberClient.beginPurchasePhoneNumbers(searchResult.getSearchId(), Context.NONE).waitForCompletion();
System.out.println("Purchase phone numbers operation is: " + purchaseResponse.getStatus());
```

### <a name="get-phone-numbers"></a>Obter números de telefone(s)

Depois de um número de compra, pode recuperá-lo do cliente.
```java
AcquiredPhoneNumber phoneNumber = phoneNumberClient.getPhoneNumber("+14255550123");
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

Também pode recuperar todos os números de telefone adquiridos.
``` java
PagedIterable<AcquiredPhoneNumber> phoneNumbers = createPhoneNumberClient().listPhoneNumbers(Context.NONE);
AcquiredPhoneNumber phoneNumber = phoneNumbers.iterator().next();
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

### <a name="update-phone-number-capabilities"></a>Atualizar capacidades de número de telefone

Com um número adquirido, pode atualizar as capacidades.
```java
PhoneNumberCapabilitiesRequest capabilitiesRequest = new PhoneNumberCapabilitiesRequest();
capabilitiesRequest
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND);
AcquiredPhoneNumber phoneNumber = phoneNumberClient.beginUpdatePhoneNumberCapabilities("+18001234567", capabilitiesRequest, Context.NONE).getFinalResult();

System.out.println("Phone Number Calling capabilities: " + phoneNumber.getCapabilities().getCalling());
System.out.println("Phone Number SMS capabilities: " + phoneNumber.getCapabilities().getSms());
```

### <a name="release-phone-number"></a>Número de telefone de lançamento

Pode libertar um número de telefone comprado.
```java
PollResponse<PhoneNumberOperation> releaseResponse =
    phoneNumberClient.beginReleasePhoneNumber("+18001234567", Context.NONE).waitForCompletion();
System.out.println("Release phone number operation is: " + releaseResponse.getStatus());
```

## <a name="run-the-code"></a>Executar o código

Navegue para o diretório que contém o ficheiro *pom.xml* e compile o projeto utilizando o seguinte `mvn` comando.

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

A saída da aplicação descreve cada ação que é concluída:
<!---cSpell:disable --->
```console
Azure Communication Services - Phone Numbers Quickstart

Searched phone numbers: [+18001234567]

Purchase phone numbers operation is: SUCCESSFULLY_COMPLETED

Phone Number Country Code: US

Phone Number Calling capabilities: inbound

Phone Number SMS capabilities: inbound

Release phone number operation is: SUCCESSFULLY_COMPLETED

```
<!---cSpell:enable --->