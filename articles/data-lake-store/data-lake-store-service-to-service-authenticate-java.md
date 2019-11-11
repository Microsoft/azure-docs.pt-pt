---
title: Autenticação de serviço a serviço-Data Lake Storage Gen1 – SDK do Java
description: Saiba como obter a autenticação serviço a serviço com Azure Data Lake Storage Gen1 usando o Azure Active Directory com Java
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f355da7cd9c035b4ed0845bbd374a93bfb4a7350
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904542"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Autenticação serviço a serviço com Azure Data Lake Storage Gen1 usando Java

> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Com a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

Neste artigo, você aprenderá como usar o SDK do Java para fazer a autenticação serviço a serviço com o Azure Data Lake Storage Gen1. Não há suporte para a autenticação do usuário final com Data Lake Storage Gen1 usando o SDK do Java.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Crie um aplicativo Azure Active Directory "Web"** . Você deve ter concluído as etapas em [Autenticação serviço a serviço com data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Este tutorial utiliza o Maven para dependências de compilação e do projeto. Embora seja possível compilar sem usar um sistema de compilação como Maven ou gradle, esses sistemas tornam muito mais fácil gerenciar dependências.

* Adicional Um IDE como [INTELLIJ Idea](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou semelhante.

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

1. Crie um projeto Maven com o [arquétipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) a partir da linha de comandos ou com um IDE. Para obter instruções sobre como criar um projeto Java com IntelliJ, veja [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto com Eclipse, clique [aqui](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Adicione as seguintes dependências ao ficheiro **pom.xml** do Maven. Adicione o seguinte fragmento antes da etiqueta **\</project>** :

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

    A primeira dependência é usar o SDK do Data Lake Storage Gen1 (`azure-data-lake-store-sdk`) do repositório do Maven. A segunda dependência é para especificar a arquitetura de registo (`slf4j-nop`) a utilizar para esta aplicação. O SDK do Data Lake Storage Gen1 usa a fachada de log [slf4j](https://www.slf4j.org/) , que permite que você escolha entre várias estruturas de log populares, como Log4J, log Java, logback, etc., ou nenhum registro em log. Neste exemplo, vamos desativar o registo e, por conseguinte, utilizar o enlace **slf4j-nop**. Para utilizar outras opções de registo na sua aplicação, clique [aqui](https://www.slf4j.org/manual.html#projectDep).

3. Adicione as seguintes declarações de importação à aplicação.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Use o trecho a seguir em seu aplicativo Java para obter o token para o aplicativo Web Active Directory criado anteriormente usando uma das subclasses de `AccessTokenProvider` (o exemplo a seguir usa `ClientCredsTokenProvider`). O fornecedor do token guarda as credenciais utilizadas para obter o token na memória e renova-o automaticamente se estiver prestes a expirar. É possível criar suas próprias subclasses de `AccessTokenProvider` para que os tokens sejam obtidos pelo seu código de cliente. Por enquanto, vamos usar apenas o fornecido no SDK.

    Substitua **FILL-IN-HERE** pelos valores reais da aplicação Web do Azure Active Directory.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

O SDK do Data Lake Storage Gen1 fornece métodos convenientes que permitem que você gerencie os tokens de segurança necessários para se comunicar com a conta do Data Lake Storage Gen1. No entanto, o SDK não impõe que sejam utilizados apenas estes métodos. Pode utilizar também qualquer outro meio para obter um token, como utilizar o [SDK do Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) ou o seu próprio código personalizado.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a usar a autenticação do usuário final para se autenticar com Data Lake Storage Gen1 usando o SDK do Java. Agora você pode examinar os seguintes artigos que falam sobre como usar o SDK do Java para trabalhar com Data Lake Storage Gen1.

* [Operações de dados em Data Lake Storage Gen1 usando o SDK do Java](data-lake-store-get-started-java-sdk.md)
