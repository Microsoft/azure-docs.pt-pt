---
title: Através de perfis de versão de API com Java no Azure Stack | Documentos da Microsoft
description: Saiba como utilizar perfis de versão de API com Java no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2018
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 09/28/2018
ms.openlocfilehash: eef9e45d71dd5a8c29112f74deaf8342dc0d1406
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58101504"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Utilizar perfis de versão de API com Java no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O SDK de Java para o Azure Stack Resource Manager fornece ferramentas para ajudar a criar e gerir a sua infraestrutura. Fornecedores de recursos no SDK incluem computação, rede, armazenamento, serviços de aplicações, e [KeyVault](../../key-vault/key-vault-whatis.md). O SDK de Java incorpora os perfis de API, incluindo dependências no ficheiro pom. XML que carrega os módulos corretos no ficheiro Java. No entanto, pode adicionar vários perfis como dependências, tais como o **2018-03-01-híbrida**, ou **mais recente** como o perfil do Azure. Usar essas dependências carrega o módulo correto para que quando cria seu tipo de recurso, pode selecionar qual versão de API desses perfis que pretende utilizar. Isto permite-lhe utilizar as versões mais recentes no Azure, ao desenvolver contra as mais recentes versões de API para o Azure Stack. Com o SDK de Java permite uma experiência de programadores de cloud híbrida verdadeiro. Perfis de API no SDK do Java permitem o desenvolvimento de cloud híbrida com a ajuda para alternar entre recursos do Azure global e recursos no Azure Stack.

## <a name="java-and-api-version-profiles"></a>Perfis de versão de API e Java

Um perfil de API é uma combinação de fornecedores de recursos e as versões de API. Pode utilizar um perfil de API para obter a versão mais recente, mais estável de cada tipo de recurso num pacote de fornecedor de recursos.

- Para utilizar as versões mais recentes de todos os serviços, utilize o **mais recente** perfil como a dependência.
    
   - Para utilizar o perfil mais recente, a dependência é **com.microsoft.azure**.

   - Para utilizar os serviços compatíveis com o Azure Stack, utilize o **com.microsoft.azure.profile\_2018\_03\_01\_híbrida** perfil.
    
      - Isso é para ser especificada no ficheiro pom. XML como uma dependência, que carrega os módulos automaticamente se escolher a classe correta na lista pendente, tal como faria com o .NET.
        
      - Parte superior de cada módulo será exibida da seguinte forma:         
           `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`
             

  - As dependências são exibidos da seguinte forma:
     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Para utilizar versões de API específicas para um tipo de recurso de um fornecedor de recursos específico, utilize as versões de API específicas definidas por meio do intellisense.

Tenha em atenção que pode combinar todas as opções no mesmo aplicativo.

## <a name="install-the-azure-java-sdk"></a>Instalar o Azure Java SDK

Utilize os seguintes passos para instalar o SDK Java:

1. Siga as instruções oficiais para instalar o Git. Para obter instruções, consulte [introdução - instalar o Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Siga as instruções oficiais para instalar o [SDK de Java](https://zulu.org/download/) e [Maven](https://maven.apache.org/). A versão correta é a versão 8 do Java Development Kit. O Apache Maven correto é a versão 3.0 ou superior. A variável de ambiente do JAVA_HOME tem de ser definida para a localização de instalação do Kit de desenvolvimento Java para concluir o início rápido. Para obter mais informações, consulte [criar a primeira função com o Java e Maven](../../azure-functions/functions-create-first-java-maven.md).

3. Para instalar os pacotes de dependência correto, abra o ficheiro pom. xml na sua aplicação Java. Adicione uma dependência, conforme mostrado no seguinte código:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. Os pacotes que devem ser instaladas depende da versão de perfil que pretende utilizar. Os nomes de pacote para as versões de perfil são:
    
   - **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **latest**

5. Se não está disponível, criar uma subscrição e guarde o ID de subscrição para utilização posterior. Para obter instruções sobre como criar uma subscrição, veja [criar subscrições de ofertas no Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

6. Criar um principal de serviço e guarde o ID de cliente e o segredo do cliente. Para obter instruções sobre como criar um principal de serviço para o Azure Stack, veja [fornecer acesso de aplicações para o Azure Stack](../azure-stack-create-service-principals.md). Tenha em atenção que o ID de cliente também é conhecido como o ID da aplicação ao criar um principal de serviço.

7. Certifique-se de que o seu principal de serviço tem a função de proprietário/Contribuidor na sua subscrição. Para obter instruções sobre como atribuir uma função ao principal de serviço, consulte [fornecer acesso de aplicações para o Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o SDK de Java do Azure com o Azure Stack, tem de indicar os valores seguintes e, em seguida, definir valores com variáveis de ambiente. Para definir as variáveis de ambiente, consulte as instruções a seguir à tabela para o seu sistema operativo.

| Valor                     | Variáveis de ambiente | Descrição                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ID do inquilino                 | AZURE_TENANT_ID            | O valor do seu Azure Stack [ <span class="underline">ID de inquilino</span>](../azure-stack-identity-overview.md).                                                          |
| ID de Cliente                 | AZURE_CLIENT_ID             | O serviço de ID da aplicação principal guardado quando principal de serviço foi criado na secção anterior deste documento.                                                                                              |
| ID da subscrição           | AZURE_SUBSCRIPTION_ID      | O [ <span class="underline">ID da subscrição</span> ](../azure-stack-plan-offer-quota-overview.md#subscriptions) é como acessa ofertas no Azure Stack.                |
| Segredo do Cliente             | AZURE_CLIENT_SECRET        | A aplicação do principal de serviço segredo guardado quando principal de serviço foi criado.                                                                                                                                   |
| Ponto final do Gestor de recursos | ARM_ENDPOINT              | Ver [ <span class="underline">o ponto de final do Gestor de recursos do Azure Stack</span>](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Localização                  | RESOURCE_LOCATION    | Local para o Azure Stack                                                                                                                                                                                                |

Para localizar o ID de inquilino para o Azure Stack, siga as instruções presentes [aqui](../azure-stack-csp-ref-operations.md). Para definir as variáveis de ambiente, efetue o seguinte:

### <a name="microsoft-windows"></a>Microsoft Windows

Para definir as variáveis de ambiente num prompt de comando do Windows, utilize o seguinte formato:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>sistemas baseados em Unix, Linux e macOS

Em sistemas Unix com base, pode usar o seguinte comando:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz de AC do Azure Stack

Se estiver a utilizar o ASDK, terá de confiar no certificado de raiz da AC no seu computador remoto. Não terá de fazer isso com os sistemas integrados.

#### <a name="windows"></a>Windows

1. Exportar o certificado autoassinado do Azure Stack para o seu ambiente de trabalho

1. Numa cmd shell, altere o diretório para %JAVA_HOME%\bin

1. Execute este cmd:

```shell
      .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>O ponto de final do Gestor de recursos do Azure Stack

O Gestor de recursos do Microsoft Azure é uma estrutura de gestão que permite aos administradores implementar, gerir e monitorizar recursos do Azure. O Azure Resource Manager pode lidar com essas tarefas, como um grupo, em vez de individualmente, numa única operação.

Pode obter as informações de metadados do ponto final do Resource Manager. O ponto final devolve um ficheiro JSON com as informações necessárias para executar o seu código.

Tenha em atenção as seguintes considerações:

- O **ResourceManagerUrl** no Azure Stack Development Kit (ASDK) é: https://management.local.azurestack.external/

- O **ResourceManagerUrl** em sistemas integrados é: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`

Para obter os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

Ficheiro JSON de exemplo:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Perfis de API existentes

1.  **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**: Perfil mais recentes, criado para o Azure Stack. Utilize este perfil para os serviços para ser mais compatível com o Azure Stack, desde que estiver a utilizar o carimbo de data / 1808 ou mais.

2.  **com.microsoft.azure**: Perfil consistindo em versões mais recentes de todos os serviços. Utilize as versões mais recentes de todos os serviços.

Para obter mais informações sobre os perfis do Azure Stack e a API, consulte a [perfis de resumo de API](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Utilização de perfil de API do SDK Java do Azure

O código a seguir autentica o principal de serviço no Azure Stack. Cria um token pelo inquilino ID e a base de autenticação, o que é específica para o Azure Stack:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

Isto permite-lhe utilizar as dependências do perfil de API para implementar a sua aplicação com êxito para o Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definir funções de definição de ambiente do Azure Stack

Para registar a cloud do Azure Stack com os pontos finais corretos, utilize o seguinte código:

```java
AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceID", settings.get("audience"));
                    put("activeDirectoryGraphResourceID", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

O `getActiveDirectorySettings` chamada no código a seguir recupera os pontos finais dos pontos de extremidade de metadados. Ele declara as variáveis de ambiente da chamada que é efetuada:

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>Exemplos de utilização de perfis de API

Pode utilizar os seguintes exemplos do GitHub como referência para a criação de soluções com perfis .NET e a API do Azure Stack:

  - [Gerir grupos de recursos](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

  - [Gerir contas de armazenamento](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

  - [Gerir uma Máquina Virtual](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm)

### <a name="sample-unit-test-project"></a>Projeto de teste de unidade de exemplo 

1. Clone o repositório utilizando o seguinte comando:
    
   `git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`

2. Criar um Azure principal de serviço e atribuir uma função aceda à subscrição. Para obter instruções sobre como criar um principal de serviço, consulte [utilize o Azure PowerShell para criar um principal de serviço com um certificado](../azure-stack-create-service-principals.md).

3. Obtenha os seguintes valores de variáveis de ambiente necessário:
    
   -  AZURE_TENANT_ID
   -  AZURE_CLIENT_ID
   -  AZURE_CLIENT_SECRET
   -  AZURE_SUBSCRIPTION_ID
   -  ARM_ENDPOINT
   -  RESOURCE_LOCATION

4. Defina as seguintes variáveis de ambiente com as informações que obteve a partir do Principal de serviço que criou utilizando a linha de comandos:
    
   - Exportar AZURE_TENANT_ID = {o seu ID de inquilino}
   - Exportar AZURE_CLIENT_ID = {o seu ID de cliente}
   - Exportar AZURE_CLIENT_SECRET = {o seu segredo de cliente}
   - Exportar AZURE_SUBSCRIPTION_ID = {o seu ID de subscrição}
   - Exportar ARM_ENDPOINT = {seu URL do Azure Stack Resource Manager}
   - Exportar RESOURCE_LOCATION = {localização do Azure Stack}

   No Windows, utilize **definir** em vez de **exportar**.

5. Utilizar o `getactivedirectorysettings` código para recuperar o ponto final de metadados de arm e utilize o cliente HTTP para definir as informações de ponto final.

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

6. No ficheiro pom. XML, adicione a dependência abaixo para utilizar o perfil de 2018-03-01-híbrida para o Azure Stack. Esta dependência instalará os módulos associados a este perfil para os fornecedores de recursos de computação, rede, armazenamento, Cofre de chaves e dos serviços de aplicações.
      
   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

8. No prompt de comando que foi aberta para definir as variáveis de ambiente, introduza a seguinte linha:
    
   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os perfis de API, consulte:

- [Gerir perfis de versão de API no Azure Stack](azure-stack-version-profiles.md)
- [Versões de API do fornecedor de recursos suportadas por perfis](azure-stack-profiles-azure-resource-manager-versions.md)
