---
title: 'Quickstart: Biblioteca de clientes de gestão Azure para Java'
description: Neste quickstart, começa com a biblioteca de clientes de gestão Azure para a Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 346854d5990ac6861bd4eb93914bb1745b90bfa5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321604"
---
[Documentação de referência](https://docs.microsoft.com/java/api/com.microsoft.azure.management.cognitiveservices?view=azure-java-stable)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/mgmt-v2017_04_18/src/main/java/com/microsoft/azure/management/cognitiveservices/v2017_04_18)  |  [Pacote (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-mgmt-cognitiveservices)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição válida da Azure - [Crie uma gratuitamente.](https://azure.microsoft.com/free/)
* A versão atual do Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de construção Gradle,](https://gradle.org/install/)ou outro gestor de dependência.


[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-java-application"></a>Criar uma nova aplicação Java

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o comando do seu diretório de `gradle init` trabalho. Este comando criará ficheiros de construção essenciais para Gradle, incluindo *build.gradle.kts* que é usado em tempo de execução para criar e configurar a sua aplicação.

```console
gradle init --type basic
```

Quando solicitado para escolher um **DSL,** selecione **Kotlin**.

Do seu diretório de trabalho, executar o seguinte comando:

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Este quickstart usa o gestor de dependência gradle. Pode encontrar a biblioteca do cliente e informações para outros gestores de dependência no [Repositório Central de Maven.](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)

No ficheiro *build.gradle.kts* do seu projeto, inclua a biblioteca do cliente como `implementation` declaração, juntamente com os plugins e configurações necessários.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure", name = "azure-mgmt-cognitiveservices", version = "1.10.0-beta")
}
```

### <a name="import-libraries"></a>Importar bibliotecas

Navegue para a nova pasta **src/main/java** e crie um ficheiro chamado *Management.java*. Abra-o no seu editor preferido ou IDE e adicione as `import` seguintes declarações:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione uma classe em *Management.java*e, em seguida, adicione os seguintes campos e seus valores dentro dela. Povoar os seus valores, utilizando o principal serviço que criou e as suas outras informações da conta Azure.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_constants)]

Em seguida, no seu método **principal,** use estes valores para construir um objeto **CognitiveServicesManager.** Este objeto é necessário para todas as suas operações de gestão Azure.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_auth)]

## <a name="call-management-methods"></a>Métodos de gestão de chamadas

Adicione o seguinte código ao seu método **Principal** para listar os recursos disponíveis, criar um recurso de amostra, listar os seus recursos próprios e, em seguida, eliminar o recurso da amostra. Definirá estes métodos nos próximos passos.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

### <a name="choose-a-service-and-pricing-tier"></a>Escolha um nível de serviço e preços

Quando criar um novo recurso, terá de conhecer o "tipo" de serviço que pretende utilizar, juntamente com o [nível de preços](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou SKU) que pretende. Usará esta e outras informações como parâmetros ao criar o recurso. Pode encontrar uma lista de "tipos" disponíveis do Serviço Cognitivo, chamando o seguinte método:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

Para criar e subscrever um novo recurso de Serviços Cognitivos, utilize o método **de criação.** Este método adiciona um novo recurso faturado ao grupo de recursos em que passa. Ao criar o seu novo recurso, terá de conhecer o "tipo" de serviço que pretende utilizar, juntamente com o seu nível de preços (ou SKU) e uma localização Azure. O seguinte método toma todos estes como argumentos e cria um recurso.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_create)]

## <a name="view-your-resources"></a>Ver os seus recursos

Para visualizar todos os recursos na sua conta Azure (em todos os grupos de recursos), utilize o seguinte método:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list)]

## <a name="delete-a-resource"></a>Eliminar um recurso

O seguinte método elimina o recurso especificado do grupo de recursos dado.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_delete)]

## <a name="see-also"></a>Veja também

* [Documentação de referência da Azure Management SDK](https://docs.microsoft.com/java/api/com.microsoft.azure.management.cognitiveservices?view=azure-java-stable)
* [O que são os Serviços Cognitivos Azure?](../../Welcome.md)
* [Autenticar pedidos aos Serviços Cognitivos da Azure](../../authentication.md)
* [Criar um novo recurso utilizando o portal do Azure](../../cognitive-services-apis-create-account.md)