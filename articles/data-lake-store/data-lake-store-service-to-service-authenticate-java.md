---
title: Autenticação serviço-a-serviço - Data Lake Storage Gen1 – Java SDK
description: Saiba como obter a autenticação de serviço-a-serviço com o Azure Data Lake Storage Gen1 usando o Azure Ative Directory com Java
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f355da7cd9c035b4ed0845bbd374a93bfb4a7350
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73904542"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Autenticação serviço-a-serviço com Azure Data Lake Storage Gen1 usando Java

> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Com a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

Neste artigo, você aprende sobre como usar o Java SDK para fazer a autenticação de serviço-a-serviço com Azure Data Lake Storage Gen1. A autenticação do utilizador final com data Lake Storage Gen1 utilizando java SDK não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de Diretório Ativo Azure .** Deve ter concluído os passos de [autenticação serviço-a-serviço com data Lake Storage Gen1 utilizando o Diretório Ativo Azure](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven.](https://maven.apache.org/install.html) Este tutorial utiliza o Maven para dependências de compilação e do projeto. Embora seja possível construir sem usar um sistema de construção como Maven ou Gradle, estes sistemas tornam muito mais fácil gerir dependências.

* (Opcional) Um IDE como [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou similar.

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

1. Crie um projeto Maven com o [arquétipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) a partir da linha de comandos ou com um IDE. Para obter instruções sobre como criar um projeto Java com IntelliJ, veja [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto com Eclipse, clique [aqui](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Adicione as seguintes dependências ao ficheiro **pom.xml** do Maven. Adicione o seguinte corte antes da ** \<etiqueta>/projeto:**

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

    A primeira dependência é usar o Data Lake Storage`azure-data-lake-store-sdk`Gen1 SDK () do repositório maven. A segunda dependência é para especificar a arquitetura de registo (`slf4j-nop`) a utilizar para esta aplicação. O Data Lake Storage Gen1 SDK utiliza fachada de exploração madeireira [slf4j,](https://www.slf4j.org/) que permite escolher entre uma série de estruturas de exploração madeireira populares, como log4j, java logging, logback, etc., ou sem registo. Neste exemplo, vamos desativar o registo e, por conseguinte, utilizar o enlace **slf4j-nop**. Para utilizar outras opções de registo na sua aplicação, clique [aqui](https://www.slf4j.org/manual.html#projectDep).

3. Adicione as seguintes declarações de importação à aplicação.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Utilize o seguinte corte na sua aplicação Java para obter um símbolo para a aplicação `AccessTokenProvider` Ative Directory `ClientCredsTokenProvider`Web que criou anteriormente utilizando uma das subclasses de (o exemplo seguinte utiliza). O fornecedor do token guarda as credenciais utilizadas para obter o token na memória e renova-o automaticamente se estiver prestes a expirar. É possível criar as suas `AccessTokenProvider` próprias subclasses de fichas, pelo que o seu código de cliente é obtido. Por enquanto, vamos usar o fornecido no SDK.

    Substitua **FILL-IN-HERE** pelos valores reais da aplicação Web do Azure Active Directory.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

O Data Lake Storage Gen1 SDK fornece métodos convenientes que permitem gerir as fichas de segurança necessárias para falar com a conta Data Lake Storage Gen1. No entanto, o SDK não impõe que sejam utilizados apenas estes métodos. Pode utilizar também qualquer outro meio para obter um token, como utilizar o [SDK do Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) ou o seu próprio código personalizado.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar a autenticação de utilizador final para autenticar com data Lake Storage Gen1 usando Java SDK. Pode agora ver os seguintes artigos que falam sobre como usar o Java SDK para trabalhar com data lake storage Gen1.

* [Operações de dados em Data Lake Storage Gen1 usando Java SDK](data-lake-store-get-started-java-sdk.md)
