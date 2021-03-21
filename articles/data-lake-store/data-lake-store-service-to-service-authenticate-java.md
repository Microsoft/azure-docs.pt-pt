---
title: Autenticação de serviço-a-serviço - Data Lake Storage Gen1 – Java SDK
description: Saiba como obter a autenticação de serviço-a-serviço com a Azure Data Lake Storage Gen1 utilizando o Azure Ative Directory com a Java
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: 0e320557a7372af6a41038d9b3196db23d2496c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000390"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Autenticação de serviço-a-serviço com Azure Data Lake Storage Gen1 usando Java

> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

Neste artigo, você aprende sobre como usar o Java SDK para fazer autenticação de serviço-a-serviço com Azure Data Lake Storage Gen1. A autenticação do utilizador final com data lake storage gen1 usando Java SDK não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação "Web" do Diretório Ativo Azure**. Deve ter completado os passos na [autenticação de serviço-a-serviço com a Data Lake Storage Gen1 utilizando o Azure Ative Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven.](https://maven.apache.org/install.html) Este tutorial utiliza o Maven para dependências de compilação e do projeto. Embora seja possível construir sem usar um sistema de construção como Maven ou Gradle, estes sistemas tornam muito mais fácil gerir as dependências.

* (Opcional) Um IDE como [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou similar.

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

1. Crie um projeto Maven com o [arquétipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) a partir da linha de comandos ou com um IDE. Para obter instruções sobre como criar um projeto Java com IntelliJ, veja [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto com Eclipse, clique [aqui](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Adicione as seguintes dependências ao ficheiro **pom.xml** do Maven. Adicione o seguinte corte antes da **\</project>** etiqueta:

    ```xml
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.2.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    A primeira dependência é usar o Data Lake Storage Gen1 SDK ( `azure-data-lake-store-sdk` ) do repositório de maven. A segunda dependência é para especificar a arquitetura de registo (`slf4j-nop`) a utilizar para esta aplicação. O Data Lake Storage Gen1 SDK utiliza fachada de registo [slf4j,](https://www.slf4j.org/) que permite escolher entre uma série de quadros populares de registo, como log4j, log de Java, logback, etc., ou sem registo. Neste exemplo, vamos desativar o registo e, por conseguinte, utilizar o enlace **slf4j-nop**. Para utilizar outras opções de registo na sua aplicação, clique [aqui](https://www.slf4j.org/manual.html#projectDep).

3. Adicione as seguintes declarações de importação à aplicação.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;
    ```

4. Utilize o seguinte corte na sua aplicação Java para obter ficha para a aplicação Ative Directory Web que criou anteriormente utilizando uma das subclasses de `AccessTokenProvider` (o exemplo a seguir `ClientCredsTokenProvider` utiliza). O fornecedor do token guarda as credenciais utilizadas para obter o token na memória e renova-o automaticamente se estiver prestes a expirar. É possível criar as suas próprias subclasses de `AccessTokenProvider` modo a que os tokens sejam obtidos pelo seu código de cliente. Por enquanto, vamos usar o fornecido no SDK.

    Substitua **FILL-IN-HERE** pelos valores reais da aplicação Web do Azure Active Directory.

    ```java
    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   
    ```

O Data Lake Storage Gen1 SDK fornece métodos convenientes que permitem gerir os tokens de segurança necessários para falar com a conta Gen1 de armazenamento de data lake. No entanto, o SDK não impõe que sejam utilizados apenas estes métodos. Pode utilizar também qualquer outro meio para obter um token, como utilizar o [SDK do Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) ou o seu próprio código personalizado.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar a autenticação do utilizador final para autenticar com a Data Lake Storage Gen1 utilizando o Java SDK. Agora pode olhar para os seguintes artigos que falam sobre como usar o Java SDK para trabalhar com a Data Lake Storage Gen1.

* [Operações de dados em Data Lake Storage Gen1 usando Java SDK](data-lake-store-get-started-java-sdk.md)
