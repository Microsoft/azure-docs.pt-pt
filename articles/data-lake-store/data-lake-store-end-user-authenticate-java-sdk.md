---
title: Autenticação de utilizador final - Java com Data Lake Storage Gen1 - Azure
description: Saiba como conseguir a autenticação de utilizador final com o Azure Data Lake Storage Gen1 utilizando o Diretório Ativo Azure com Java
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1e03ad657fd40dce22a17f2fff5b67a65eb3eb52
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691768"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Autenticação de utilizador final com Azure Data Lake Storage Gen1 usando Java
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Com a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

Neste artigo, aprende-se a usar o Java SDK para fazer a autenticação de utilizador final com o Azure Data Lake Storage Gen1. Para autenticação de serviço a serviço com Data Lake Storage Gen1 utilizando Java SDK, consulte [a autenticação serviço-a-serviço com data Lake Storage Gen1 usando Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de Diretório Ativo Azure "Nativa".** Deve ter concluído os passos de [autenticação do utilizador final com data Lake Storage Gen1 utilizando o Diretório Ativo Azure](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven.](https://maven.apache.org/install.html) Este tutorial utiliza o Maven para dependências de compilação e do projeto. Embora seja possível compilar sem utilizar um sistema de compilação como Maven ou Gradle, estes sistemas facilitam muito a gestão das dependências.

* (Opcional) E IDE como [IntelliJ IDEIA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) ou semelhante.

## <a name="end-user-authentication"></a>Autenticação de utilizador final
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
   
    A primeira dependência é usar o Data Lake Storage`azure-data-lake-store-sdk`Gen1 SDK () do repositório maven. A segunda dependência é para especificar a arquitetura de registo (`slf4j-nop`) a utilizar para esta aplicação. O Data Lake Storage Gen1 SDK utiliza fachada de exploração madeireira [SLF4J,](https://www.slf4j.org/) que permite escolher entre uma série de estruturas de exploração madeireira populares, como Log4j, Java logging, Logback, etc., ou sem registo. Neste exemplo, vamos desativar o registo e, por conseguinte, utilizar o enlace **slf4j-nop**. Para utilizar outras opções de registo na sua aplicação, clique [aqui](https://www.slf4j.org/manual.html#projectDep).

3. Adicione as seguintes declarações de importação à aplicação.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Utilize o seguinte corte na sua aplicação Java para obter um símbolo para `DeviceCodeTokenProvider`a aplicação nativa do Diretório Ativo que criou anteriormente usando o . Substitua o **FILL-IN-HERE** pelos valores reais da aplicação nativa do Diretório Ativo Azure.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

O Data Lake Storage Gen1 SDK fornece métodos convenientes que permitem gerir as fichas de segurança necessárias para falar com a conta Data Lake Storage Gen1. No entanto, o SDK não impõe que sejam utilizados apenas estes métodos. Pode utilizar também qualquer outro meio para obter um token, como utilizar o [SDK do Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) ou o seu próprio código personalizado.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a usar a autenticação de utilizador final para autenticar com o Azure Data Lake Storage Gen1 usando java SDK. Pode agora ver os seguintes artigos que falam sobre como usar o Java SDK para trabalhar com o Azure Data Lake Storage Gen1.

* [Operações de dados em Data Lake Storage Gen1 usando Java SDK](data-lake-store-get-started-java-sdk.md)


