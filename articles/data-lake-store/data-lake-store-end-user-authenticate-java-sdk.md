---
title: 'Autenticação de utilizador final: Java com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory | Documentos da Microsoft'
description: Saiba como conseguir a autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory com o Java
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8b558fca964f33d47d331e007329d1bae2626877
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878106"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake com Java
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Utilizar o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

Neste artigo, saiba como utilizar o SDK de Java para fazer a autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake. Para a autenticação serviço a serviço com a geração 1 de armazenamento do Data Lake com o SDK de Java, veja [autenticação do serviço a serviço com geração 1 de armazenamento do Data Lake com o Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de "Nativa" do Azure Active Directory**. Tem de ter concluído os passos em [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Este tutorial utiliza o Maven para dependências de compilação e do projeto. Embora seja possível compilar sem utilizar um sistema de compilação como Maven ou Gradle, estes sistemas facilitam muito a gestão das dependências.

* (Opcional) E IDE como [IntelliJ IDEIA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) ou semelhante.

## <a name="end-user-authentication"></a>Autenticação de utilizador final
1. Crie um projeto Maven com o [arquétipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) a partir da linha de comandos ou com um IDE. Para obter instruções sobre como criar um projeto Java com IntelliJ, veja [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto com Eclipse, clique [aqui](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Adicione as seguintes dependências ao ficheiro **pom.xml** do Maven. Adicione o seguinte fragmento antes da etiqueta **\</project>**:
   
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
   
    A primeira dependência é para utilizar o SDK de geração 1 do Data Lake Storage (`azure-data-lake-store-sdk`) do repositório maven. A segunda dependência é para especificar a arquitetura de registo (`slf4j-nop`) a utilizar para esta aplicação. Utiliza o SDK de geração 1 de armazenamento do Data Lake [slf4j](https://www.slf4j.org/) fachada de Registro em log, que permite que escolha a partir de um número de arquiteturas de registo mais populares, como log4j, registo Java, logback, etc., ou nenhum registo. Neste exemplo, vamos desativar o registo e, por conseguinte, utilizar o enlace **slf4j-nop**. Para utilizar outras opções de registo na sua aplicação, clique [aqui](https://www.slf4j.org/manual.html#projectDep).

3. Adicione as seguintes declarações de importação à aplicação.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Utilize o fragmento seguinte na sua aplicação Java para obter um token para o aplicativo nativo do Active Directory que criou anteriormente com o `DeviceCodeTokenProvider`. Substitua **FILL-IN-HERE** com os valores reais da aplicação nativa do Azure Active Directory.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

O SDK de geração 1 de armazenamento do Data Lake fornece métodos convenientes que lhe permitem gerir os tokens de segurança necessários para comunicar com a conta de geração 1 de armazenamento do Data Lake. No entanto, o SDK não impõe que sejam utilizados apenas estes métodos. Pode utilizar também qualquer outro meio para obter um token, como utilizar o [SDK do Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) ou o seu próprio código personalizado.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar a autenticação de utilizador final para se autenticar com a geração 1 de armazenamento do Azure Data Lake com o SDK de Java. Agora, pode ver os seguintes artigos que falam sobre como utilizar o SDK de Java para trabalhar com a geração 1 de armazenamento do Azure Data Lake.

* [Operações de dados na geração 1 de armazenamento do Data Lake com o SDK de Java](data-lake-store-get-started-java-sdk.md)


