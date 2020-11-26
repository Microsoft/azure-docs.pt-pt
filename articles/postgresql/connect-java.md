---
title: 'Quickstart: Use Java e JDBC com Base de Dados Azure para PostgreSQL'
description: Neste arranque rápido, aprende-se a usar Java e JDBC com uma Base de Dados Azure para PostgreSQL.
author: jdubois
ms.author: judubois
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-azurecli
ms.topic: quickstart
ms.devlang: java
ms.date: 08/17/2020
ms.openlocfilehash: 42547338c0f5f2f3105833b12e499d40b6209b05
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184710"
---
# <a name="quickstart-use-java-and-jdbc-with-azure-database-for-postgresql"></a>Quickstart: Use Java e JDBC com Base de Dados Azure para PostgreSQL

Este tópico demonstra a criação de uma aplicação de amostra que utiliza Java e [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) para armazenar e recuperar informações na [Base de Dados Azure para PostgreSQL](./index.yml).

JDBC é a API padrão de Java para ligar às bases de dados relacionais tradicionais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver um, [faça um julgamento gratuito.](https://azure.microsoft.com/free/)
- [Azure Cloud Shell](../cloud-shell/quickstart.md) ou [Azure CLI](/cli/azure/install-azure-cli). Recomendamos a Azure Cloud Shell para que faça login automaticamente e tenha acesso a todas as ferramentas de que necessita.
- Um Kit de [Desenvolvimento java](/azure/developer/java/fundamentals/java-jdk-long-term-support)suportado, versão 8 (incluído na Azure Cloud Shell).
- A ferramenta de construção [Apache Maven.](https://maven.apache.org/)

## <a name="prepare-the-working-environment"></a>Preparar o ambiente de trabalho

Vamos usar variáveis ambientais para limitar erros de dactilografia e para facilitar a personalização da seguinte configuração para as suas necessidades específicas.

Configurar essas variáveis ambientais utilizando os seguintes comandos:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_POSTGRESQL_USERNAME=demo
AZ_POSTGRESQL_PASSWORD=<YOUR_POSTGRESQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Substitua os marcadores de posição pelos seguintes valores, que são utilizados ao longo deste artigo:

- `<YOUR_DATABASE_NAME>`: O nome do seu servidor PostgreSQL. Deve ser um nome exclusivo no Azure.
- `<YOUR_AZURE_REGION>`: A região de Azure que você vai usar. Pode utilizar a região `eastus` por predefinição, mas recomendamos que configure uma região mais próxima do local onde vive. Pode ter a lista completa das regiões disponíveis através da `az account list-locations` entrada.
- `<YOUR_POSTGRESQL_PASSWORD>`: A palavra-passe do seu servidor de base de dados PostgreSQL. Essa palavra-passe deve ter pelo menos oito carateres. Os carateres devem enquadrar-se em três das seguintes categorias: letras maiúsculas, letras minúsculas, números (0 a 9) e carateres alfanuméricos (!, $, #, %, entre outros).
- `<YOUR_LOCAL_IP_ADDRESS>`: O endereço IP do seu computador local, a partir do qual executará a sua aplicação Java. Uma maneira conveniente de encontrá-lo é apontar o seu navegador para [whatismyip.akamai.com.](http://whatismyip.akamai.com/)

Em seguida, crie um grupo de recursos utilizando o seguinte comando:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> Utilizamos o `jq` utilitário para exibir dados JSON e torná-lo mais legível. Este utilitário é instalado por padrão na [Azure Cloud Shell](https://shell.azure.com/). Se não quiser utilizar esse utilitário, pode remover sem problemas a parte `| jq` de todos os comandos que utilizamos.

## <a name="create-an-azure-database-for-postgresql-instance"></a>Criar uma base de dados de Azure para o caso PostgreSQL

A primeira coisa que vamos criar é um servidor PósgreSQL gerido.

> [!NOTE]
> Pode ler informações mais detalhadas sobre a criação de servidores PostgreSQL na [Criação de uma Base de Dados Azure para servidor PostgreSQL utilizando o portal Azure](./quickstart-create-server-database-portal.md).

Em [Azure Cloud Shell](https://shell.azure.com/), executar o seguinte comando:

```azurecli
az postgres server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_POSTGRESQL_USERNAME \
    --admin-password $AZ_POSTGRESQL_PASSWORD \
  	| jq
```

Este comando cria um pequeno servidor PostgreSQL.

### <a name="configure-a-firewall-rule-for-your-postgresql-server"></a>Configure uma regra de firewall para o seu servidor PostgreSQL

A base de dados Azure para casos postgreSQL são protegidos por padrão. O serviço tem uma firewall que não permite ligações de entrada. Para poder utilizar a sua base de dados, é necessário adicionar uma regra de firewall que permita ao endereço IP local aceder ao servidor de base de dados.

Uma vez que configura o seu endereço IP local no início deste artigo, pode abrir a firewall do servidor executando o seguinte comando:

```azurecli
az postgres server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
  	| jq
```

### <a name="configure-a-postgresql-database"></a>Configurar uma base de dados PostgreSQL

O servidor PostgreSQL que criou anteriormente está vazio. Não tem nenhuma base de dados que possa usar com a aplicação Java. Criar uma nova base de dados chamada `demo` utilizando o seguinte comando:

```azurecli
az postgres db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server-name $AZ_DATABASE_NAME \
  	| jq
```

### <a name="create-a-new-java-project"></a>Criar um novo projeto Java

Utilizando o seu IDE favorito, crie um novo projeto Java e adicione um `pom.xml` ficheiro no seu diretório de raiz:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <version>42.2.12</version>
        </dependency>
    </dependencies>
</project>
```

Este ficheiro é um [Apache Maven](https://maven.apache.org/) que configura o nosso projeto para usar:

- Java 8
- Um recente condutor postgresQL para Java

### <a name="prepare-a-configuration-file-to-connect-to-azure-database-for-postgresql"></a>Preparar um ficheiro de configuração para ligar à Base de Dados do Azure para o PostgreSQL

Crie um ficheiro *src/main/resources/application.properties* e adicione:

```properties
url=jdbc:postgresql://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo?ssl=true&sslmode=require
user=demo@$AZ_DATABASE_NAME
password=$AZ_POSTGRESQL_PASSWORD
```

- Substitua as `$AZ_DATABASE_NAME` duas variáveis pelo valor que configuraste no início deste artigo.
- Substitua a `$AZ_POSTGRESQL_PASSWORD` variável pelo valor que configurado no início deste artigo.

> [!NOTE]
> Anexamos `?ssl=true&sslmode=require` à propriedade de `url` configuração, para dizer ao controlador JDBC para usar TLS[(Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security)ao ligar à base de dados. É obrigatório utilizar TLS com Base de Dados Azure para PostgreSQL, e é uma boa prática de segurança.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>Criar um ficheiro SQL para gerar o esquema da base de dados

Utilizaremos um *ficheiro src/main/resources/file `schema.sql`* para criar um esquema de base de dados. Crie este ficheiro, com o seguinte conteúdo:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

## <a name="code-the-application"></a>Codificar a aplicação

### <a name="connect-to-the-database"></a>Ligue-se à base de dados

Em seguida, adicione o código Java que utilizará jDBC para armazenar e obter dados do seu servidor PostgreSQL.

Crie um ficheiro *src/main/java/DemoApplication.java* que contenha:

```java
package com.example.demo;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
    }
}
```

Este código Java utilizará o *ficheiro.propriedades* e o *esquema.sql* ficheiros que criámos anteriormente, de forma a ligar ao servidor PostgreSQL e criar um esquema que armazenará os nossos dados.

Neste ficheiro, pode ver que comentámos métodos para inserir, ler, atualizar e apagar dados: codificaremos esses métodos no resto deste artigo, e poderá descomprê-los um após o outro.

> [!NOTE]
> As credenciais de base de dados são armazenadas nas propriedades do *utilizador* e *palavra-passe* do ficheiro *application.properties.* Essas credenciais são usadas na `DriverManager.getConnection(properties.getProperty("url"), properties);` execução, uma vez que o ficheiro de propriedades é passado como argumento.

Agora pode executar esta classe principal com a sua ferramenta preferida:

- Utilizando o seu IDE, deverá ser capaz de clicar à direita na classe *DemoApplication* e executá-la.
- Utilizando o Maven, pode executar a aplicação executando: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"` .

A aplicação deve ligar-se à Base de Dados Azure para PostgreSQL, criar um esquema de base de dados e, em seguida, fechar a ligação, como deve ver nos registos da consola:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Closing database connection 
```

### <a name="create-a-domain-class"></a>Criar uma classe de domínio

Crie uma nova `Todo` classe Java, junto à `DemoApplication` classe, e adicione o seguinte código:

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

Esta classe é um modelo de domínio mapeado na `todo` tabela que criou ao executar o script .sql *esquema.*

### <a name="insert-data-into-azure-database-for-postgresql"></a>Inserir dados na Base de Dados do Azure para PostgreSQL

No ficheiro *src/main/java/DemoApplication.java,* após o método principal, adicione o seguinte método para inserir dados na base de dados:

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

Pode agora descodir as duas seguintes linhas no `main` método:

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

A execução da classe principal deverá agora produzir a seguinte saída:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-database-for-postgresql"></a>Ler dados da Base de Dados Azure para PostgreSQL

Vamos ler os dados anteriormente inseridos, para validar que o nosso código funciona corretamente.

No ficheiro *src/main/java/DemoApplication.java,* após o `insertData` método, adicione o seguinte método para ler os dados a partir da base de dados:

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

Pode agora descodir a seguinte linha no `main` método:

```java
todo = readData(connection);
```

A execução da classe principal deverá agora produzir a seguinte saída:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="updating-data-in-azure-database-for-postgresql"></a>Atualizar dados na Base de Dados Azure para PostgreSQL

Vamos atualizar os dados que inserimos anteriormente.

Ainda no ficheiro *src/main/java/DemoApplication.java,* após o `readData` método, adicione o seguinte método para atualizar os dados dentro da base de dados:

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

Pode agora descodir as duas seguintes linhas no `main` método:

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

A execução da classe principal deverá agora produzir a seguinte saída:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="deleting-data-in-azure-database-for-postgresql"></a>Eliminação de dados na Base de Dados Azure para PostgreSQL

Finalmente, vamos apagar os dados que inserimos anteriormente.

Ainda no ficheiro *src/main/java/DemoApplication.java,* após o `updateData` método, adicione o seguinte método para eliminar dados dentro da base de dados:

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

Pode agora descodir a seguinte linha no `main` método:

```java
deleteData(todo, connection);
```

A execução da classe principal deverá agora produzir a seguinte saída:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Delete data 
[INFO   ] Read data 
[INFO   ] There is no data in the database! 
[INFO   ] Closing database connection 
```

## <a name="clean-up-resources"></a>Limpar recursos

Parabéns! Criou uma aplicação Java que utiliza o JDBC para armazenar e recuperar dados da Base de Dados Azure para PostgreSQL.

Para limpar todos os recursos utilizados durante este arranque rápido, elimine o grupo de recursos utilizando o seguinte comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./howto-migrate-using-export-and-import.md)
