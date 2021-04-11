---
title: 'Quickstart: A sua primeira consulta java'
description: Neste arranque rápido, siga os passos para ativar os pacotes De Recursos Gráfico Maven para Java e executar a sua primeira consulta.
ms.date: 03/30/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 97c04cb8b8180034bdc5109446c79deb56e457c9
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223973"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>Quickstart: Executar a sua primeira consulta de Gráfico de Recursos usando Java

O primeiro passo para a utilização do Azure Resource Graph é verificar se estão instaladas as embalagens Maven necessárias para a Java. Este quickstart acompanha-o através do processo de adição dos pacotes Maven à sua instalação Java.

No final deste processo, terá adicionado os pacotes Maven à sua instalação Java e executará a sua primeira consulta de Gráfico de Recursos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Verifique se o último Azure CLI está instalado (pelo menos **2.21.0**). Se ainda não estiver instalado, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

  > [!NOTE]
  > O Azure CLI é necessário para permitir que a Azure SDK para a Java utilize a **autenticação baseada** em CLI nos seguintes exemplos. Para obter informações sobre outras opções, consulte [a biblioteca do cliente da Identidade Azure para Java.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)

- O [Kit de Desenvolvimento java,](/azure/developer/java/fundamentals/java-jdk-long-term-support)versão
  8.

- [Apache Maven,](https://maven.apache.org/)versão 3.6 ou superior.

## <a name="create-the-resource-graph-project"></a>Criar o projeto Gráfico de Recursos

Para permitir que a Java questione o Azure Resource Graph, crie e configuga uma nova aplicação com a Maven e instale os pacotes Maven necessários.

1. Inicialize uma nova aplicação Java chamada "argQuery" com um [arquétipo Maven:](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. Mude os diretórios para a nova pasta do projeto `argQuery` e abra no seu editor `pom.xml` favorito. Adicione os `<dependency>` seguintes nóles sob o `<dependencies>` nó existente:

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
   ```

1. No `pom.xml` ficheiro, adicione o seguinte `<properties>` nó no nó base para atualizar as `<project>` versões de origem e alvo:

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. No `pom.xml` ficheiro, adicione o nó seguinte `<build>` sob o nó base para `<project>` configurar o objetivo e a classe principal para o projeto funcionar.

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. Substitua o predefinido `App.java` `\argQuery\src\main\java\com\Fabrikam` por padrão no seguinte código e guarde o ficheiro atualizado:

   ```java
   package com.Fabrikam;
   
   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;
   
   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];
   
           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));
   
           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);
           
           QueryResponse response = manager.resourceProviders().resources(queryRequest);
   
           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. Construa a aplicação da `argQuery` consola:

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Com a aplicação da consola Java construída, é hora de experimentar uma consulta simples de Gráfico de Recursos. A consulta devolve os primeiros cinco recursos Azure com o **Nome** e Tipo de **Recurso** de cada recurso.

Em cada chamada `argQuery` para, existem variáveis que são usadas que você precisa substituir por seus próprios valores:

- `{subscriptionId}` - substituir pelo ID da subscrição
- `{query}` - Substitua-se pela consulta de gráfico de recursos Azure

1. Utilize o CLI Azure para autenticar com `az login` .

1. Altere os diretórios para a `argQuery` pasta do projeto que criou com o comando `mvn -B archetype:generate` anterior.

1. Execute a sua primeira consulta de gráfico de recurso Azure usando o Maven para compilar a aplicação da consola e passar os argumentos. A `exec.args` propriedade identifica argumentos por espaços. Para identificar a consulta como um único argumento, embrulhamo-la com citações únicas `'` ().

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > Como este exemplo de consulta não fornece um tipo modificador `order by` como, executar esta consulta várias vezes é provável que produza um conjunto diferente de recursos por pedido.

1. Altere o argumento `argQuery.exe` para e altere a consulta para `order by` a propriedade **Nome:**

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, Esta ordem de comando limita primeiro os resultados da consulta e depois ordena-os.

1. Altere o parâmetro final para `argQuery.exe` e altere a consulta para primeiro `order by` a propriedade **Nome** e, em seguida, para os `limit` cinco melhores resultados:

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

Quando a consulta final é executada várias vezes, assumindo que nada no seu ambiente está a mudar, os resultados devolvidos são consistentes e encomendados pela propriedade **Name,** mas ainda limitados aos cinco melhores resultados.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender remover a aplicação da consola Java e os pacotes instalados, pode fazê-lo eliminando a pasta do `argQuery` projeto.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma aplicação de consola Java com os pacotes de Gráfico de Recursos necessários e executou a sua primeira consulta. Para saber mais sobre a linguagem Resource Graph, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre o idioma de consulta](./concepts/query-language.md)