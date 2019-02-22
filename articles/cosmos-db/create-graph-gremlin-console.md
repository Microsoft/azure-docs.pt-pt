---
title: 'Tutorial do Azure Cosmos DB: Criar, consultar e percorrer na consola Apache TinkerPops Gremlin'
description: Um início rápido do Azure Cosmos DB para criar vértices, margens e consultas com a Gremlin API do Azure Cosmos DB.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lbosq
ms.openlocfilehash: b431d1b739342c54cbc218efdfded1ee516ecaa7
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586397"
---
# <a name="quickstart-create-query-and-traverse-a-azure-cosmos-db-graph-database-using-the-gremlin-console"></a>Início rápido: Criar, consultar e percorrer uma base de dados de gráfico do Azure Cosmos DB com a consola Gremlin

> [!div class="op_single_selector"]
> * [Consola do Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este início rápido demonstra como criar uma conta [Gremlin API](graph-introduction.md), uma base de dados e um gráfico (contentor) do Azure Cosmos DB com o portal do Azure e, depois, utilizar a [Consola Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) do [Apache TinkerPop](https://tinkerpop.apache.org) para trabalhar com dados da Gremlin API. Neste tutorial, vai criar e consultar vértices e margens, atualizar uma propriedade de vértice, consultar vértices, percorrer o gráfico e eliminar um vértice.

![Azure Cosmos DB a partir da consola Apache Gremlin](./media/create-graph-gremlin-console/gremlin-console.png)

A consola Gremlin é baseada em Groovy/Java e é executada em Linux, Mac e Windows. Pode transferi-la no [site Apache TinkerPop](https://tinkerpop.apache.org/downloads.html).

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma subscrição do Azure para criar uma conta do Azure Cosmos DB neste guia de introdução.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Também tem de instalar a [consola Gremlin](https://tinkerpop.apache.org/). Utilize a versão 3.2.5 ou superior. (Para utilizar a consola Gremlin no Windows, tem de instalar [Java Runtime](https://www.oracle.com/technetwork/java/javase/overview/index.html).)

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a id="ConnectAppService"></a>Ligar ao seu serviço de aplicações
1. Antes de iniciar a Consola Gremlin, crie ou modifique o ficheiro de configuração remote-secure.yaml no diretório `apache-tinkerpop-gremlin-console-3.2.5/conf`.
2. Preencha as configurações de *anfitrião*, *porta*, *nome de utilizador*, *palavra-passe*, *connectionPool* e *serializador*, conforme definido na seguinte tabela:

    Definição|Valor sugerido|Descrição
    ---|---|---
    anfitriões|[*account-name*.gremlin.cosmosdb.azure.com] ou [*account-name*.graphs.azure.com] para contas criadas antes de 20 de dezembro de 2017|Veja a captura de ecrã abaixo. Este é o valor de Gremlin URI na página Descrição Geral do portal do Azure, entre parênteses, com :443/ à direita removido.
    porta|443|Defina como 443.
    o nome de utilizador|*O seu nome de utilizador*|O recurso do formulário `/dbs/<db>/colls/<coll>`, em que `<db>` é o nome da base de dados e `<coll>` é o nome da coleção.
    palavra-passe|*A chave primária*| Veja a segunda captura de ecrã abaixo. Esta é a chave primária, que pode ser obtida na página Chaves do portal do Azure, na caixa Chave Primária. Utilize o botão de copiar, no lado esquerdo da caixa, para copiar o valor.
    connectionPool|{enableSsl: true}|A definição do conjunto de ligações para SSL.
    serializador|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Defina como este valor e elimine eventuais quebras de linha `\n` quando colar o valor.

    No valor anfitriões, copie os **URI de Gremlin** partir dos **descrição geral** página: ![Ver e copiar o valor do URI do Gremlin na página Descrição Geral no portal do Azure](./media/create-graph-gremlin-console/gremlin-uri.png)

    O valor de palavra-passe, copie o **chave primária** partir a **chaves** página: ![Ver e copiar a chave primária no portal, página chaves do Azure](./media/create-graph-gremlin-console/keys.png)

O ficheiro remote-secure.yaml deve ter o seguinte aspeto:

```
hosts: [your_database_server.gremlin.cosmosdb.azure.com]
port: 443
username: /dbs/your_database_account/colls/your_collection
password: your_primary_key
connectionPool: {
  enableSsl: true
}
serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0, config: { serializeResultToString: true }}
```

Certifique-se colocar o valor do parâmetro de anfitriões dentro de parênteses Retos []. 

3. No seu terminal, execute `bin/gremlin.bat` ou `bin/gremlin.sh` para iniciar a [consola Gremlin](https://tinkerpop.apache.org/docs/3.2.5/tutorials/getting-started/).
4. No seu terminal, execute `:remote connect tinkerpop.server conf/remote-secure.yaml` para ligar ao seu serviço de aplicações.

    > [!TIP]
    > Se receber o erro `No appenders could be found for logger`, certifique-se de que atualizou o valor do serializador no ficheiro remote-secure.yaml, conforme descrito no passo 2. 

5. Em seguida, execute `:remote console` para redirecionar todos os comandos da consola para o servidor remoto.

   > [!NOTE]
   > Se não executar o comando `:remote console`, mas quiser redirecionar todos os comandos da consola para o servidor remoto, deve incluir o prefixo `:>` no comando. Por exemplo, deve executar o comando como `:> g.V().count()`. Este prefixo faz parte do comando e é importante ao utilizar a consola do Gremlin com o Azure Cosmos DB. Omitir este prefixo ensina a consola a executar o comando localmente, muitas vezes num grafo dentro da memória. A utilização deste prefixo `:>` diz à consola para executar um comando remoto, neste caso no Azure Cosmos DB (o emulador do localhost ou uma instância do Azure).

Ótimo! Agora que concluímos a configuração, comecemos a executar alguns comandos da consola.

Vamos tentar um comando de contagem() simples. Escreva o seguinte na consola, na linha de comandos:

```
g.V().count()
```

## <a name="create-vertices-and-edges"></a>Criar vértices e margens

Vamos começar por adicionar cinco vértices de pessoas, para *Thomas*, *Mary Kay*, *Robin*, *Ben* e *Jack*.

Entrada (Thomas):

```
g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44).property('userid', 1)
```

Saída:

```
==>[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d,label:person,type:vertex,properties:[firstName:[[id:f02a749f-b67c-4016-850e-910242d68953,value:Thomas]],lastName:[[id:f5fa3126-8818-4fda-88b0-9bb55145ce5c,value:Andersen]],age:[[id:f6390f9c-e563-433e-acbf-25627628016e,value:44]],userid:[[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d|userid,value:1]]]]
```
Entrada (Mary Kay):

```
g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39).property('userid', 2)

```

Saída:

```
==>[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e,label:person,type:vertex,properties:[firstName:[[id:ea0604f8-14ee-4513-a48a-1734a1f28dc0,value:Mary Kay]],lastName:[[id:86d3bba5-fd60-4856-9396-c195ef7d7f4b,value:Andersen]],age:[[id:bc81b78d-30c4-4e03-8f40-50f72eb5f6da,value:39]],userid:[[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e|userid,value:2]]]]

```

Entrada (Robin):

```
g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield').property('userid', 3)
```

Saída:

```
==>[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e,label:person,type:vertex,properties:[firstName:[[id:ec65f078-7a43-4cbe-bc06-e50f2640dc4e,value:Robin]],lastName:[[id:a3937d07-0e88-45d3-a442-26fcdfb042ce,value:Wakefield]],userid:[[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e|userid,value:3]]]]
```

Entrada (Ben):

```
g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller').property('userid', 4)

```

Saída:

```
==>[id:ee86b670-4d24-4966-9a39-30529284b66f,label:person,type:vertex,properties:[firstName:[[id:a632469b-30fc-4157-840c-b80260871e9a,value:Ben]],lastName:[[id:4a08d307-0719-47c6-84ae-1b0b06630928,value:Miller]],userid:[[id:ee86b670-4d24-4966-9a39-30529284b66f|userid,value:4]]]]
```

Entrada (Jack):

```
g.addV('person').property('firstName', 'Jack').property('lastName', 'Connor').property('userid', 5)
```

Saída:

```
==>[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469,label:person,type:vertex,properties:[firstName:[[id:4250824e-4b72-417f-af98-8034aa15559f,value:Jack]],lastName:[[id:44c1d5e1-a831-480a-bf94-5167d133549e,value:Connor]],userid:[[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469|userid,value:5]]]]
```


Em seguida, vamos adicionar margens para as relações entre estas quatro pessoas.

Entrada (Thomas -> Mary Kay):

```
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Saída:

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Entrada (Thomas -> Robin):

```
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Saída:

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Entrada (Robin -> Ben):

```
g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Saída:

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Atualizar um vértice

Vamos atualizar o vértice *Thomas* com uma nova idade de *45*.

Entrada:
```
g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Saída:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Consultar o gráfico

Agora, vamos executar uma variedade de consultas no seu gráfico.

Em primeiro lugar, vamos tentar uma consulta com um filtro para devolver apenas as pessoas com mais de 40 anos.

Entrada (consulta de filtro):

```
g.V().hasLabel('person').has('age', gt(40))
```

Saída:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Depois, vamos projetor o nome próprio das pessoas que têm mais de 40 anos.

Entrada (consulta de filtro + projeção):

```
g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Saída:

```
==>Thomas
```

## <a name="traverse-your-graph"></a>Percorrer o gráfico

Vamos percorrer o gráfico para devolver todos os amigos do Thomas.

Entrada (amigos do Thomas):

```
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Saída: 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

Em seguida, vamos obter a próxima camada de vértices. Percorra o gráfico para devolver todos os amigos dos amigos do Thomas.

Entrada (amigos dos amigos do Thomas):

```
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Saída:

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Eliminar um vértice

Vamos agora eliminar um vértice da base de dados do gráfico.

Entrada (eliminar vértice do Jack):

```
g.V().hasLabel('person').has('firstName', 'Jack').drop()
```

## <a name="clear-your-graph"></a>Limpar o gráfico

Por fim, vamos limpar todos os vértices e margens da base de dados.

Entrada:

```
g.E().drop()
g.V().drop()
```

Parabéns! Concluiu este Azure Cosmos DB: Tutorial de API do gremlin!

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

No guia de introdução, aprendeu a criar uma conta do Azure Cosmos DB, a criar um gráfico com o Data Explorer, a criar vértices e margens e a percorrer o gráfico com a consola Gremlin. Agora, pode criar consultas mais complexas e implementar lógica poderosa para percorrer gráficos com Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)
