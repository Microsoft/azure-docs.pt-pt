---
title: Azure Cosmos DB SQL Python API, SDK & recursos
description: Saiba tudo sobre o SQL Python API e SDK incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB Python SDK.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 08/12/2020
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: 470ae497acab4c75e83a13e485d1bcb118485ab9
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799518"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>SDK Python do Azure Cosmos DB para a API SQL: Notas de versão e recursos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 de .NET Core](sql-api-sdk-dotnet-core.md)
> * [SDK Feed de Alterações .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK v4 de Java](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Executor a granel - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa - Java](sql-api-sdk-bulk-executor-java.md)

| Página| Ligação |
|---|---|
|**Baixar SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Documentação da API**|[Documentação de referência da Python API](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos?view=azure-python&preserve-view=true)|
|**Instruções de instalação da SDK**|[Instruções de instalação python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Introdução**|[Começa com o Python SDK](create-sql-api-python.md)|
|**Plataforma suportada atual**|[Python 2.7](https://www.python.org/downloads/) e [Python 3.6+](https://www.python.org/downloads/)|

## <a name="release-history"></a>História do lançamento

## <a name="420"></a>4.2.0

**Correções de bugs**
- Bug fixo onde o token de continuação não é honrado quando query_iterable é usado para obter resultados por página.
- Bug fixo onde os tokens de recursos não são honrados para leituras e eliminações de documentos. 

**Novas funcionalidades**
- Suporte adicional para passar `partitionKey` durante a consulta do Change-Feed.

## <a name="410"></a>4.1.0

- Aviso de depreciação adicionado para o modo de indexação "preguiçoso". O backend já não permite criar recipientes com este modo e irá defini-los de forma consistente.

**Novas funcionalidades**
- Adicionou a capacidade de definir o TTL de armazenamento analítico ao criar um novo recipiente.

**Correções de bugs**
- Suporte fixo `dicts` para como entradas para get_client APIs.
- Compatibilidade python fixa 2/3 em iteradores de consultas.
- Erro de sugestão de tipo fixo.
- Bug fixo onde os cabeçalhos de opções não foram adicionados à função upsert_item. 
- Erro fixo levantado quando um ID não-string é usado num item. Agora levanta o TypeError em vez do AttributeError.


## <a name="400"></a>4.0.0

* Libertação estável.
* HttpLoggingPolicy adicionado ao pipeline para permitir a passagem de um madeireiro personalizado para cabeçalhos de pedido e resposta.

### <a name="400b6"></a>4.0.0b6

* Bug fixo em synchronized_request para APIs dos meios de comunicação.
* Removido MediaReadMode e MediaRequestTimeout da ConnectionPolicy, uma vez que os pedidos dos meios de comunicação não são suportados.

### <a name="400b5"></a>4.0.0b5

* módulo azure.cosmos.errors deprecado e substituído por azure.cosmos.exceções
* Os parâmetros da condição de acesso foram `access_condition` `if_match` `if_none_match` depreciados a favor de `match_condition` parâmetros e `etag` parâmetros separados.
* Bug fixo no provedor do mapa de encaminhamento.
* Consulta adicional Suporte Distinto, Offset e Limit.
* Contexto de execução de consulta de documento padrão agora usado para

  * Alterar consultas de alimentação
  * consultas de partição únicas `partitionkey` (, `partitionKeyRangeId` está presente em opções)
  * Consultas não documentais

* Erros para agregados em várias divisórias, com permitir consulta de partição cruzada definida para verdadeiro, mas nenhuma palavra-chave de "valor" presente
* Atinge o ponto final do plano de consulta para outros cenários para obter plano de consulta
* Apoio adicional `__repr__` a objetos de entidade cosmos.
* Documentação atualizada.

### <a name="400b4"></a>4.0.0b4

* Suporte adicionado para um `timeout` argumento de palavra-chave a todas as operações para especificar um tempo limite absoluto em segundos em que a operação deve ser concluída. Se o valor do tempo limite for excedido, a `azure.cosmos.errors.CosmosClientTimeoutError` será aumentada.

* Adicionei um novo `ConnectionRetryPolicy` para gerir o comportamento de retripdurar durante os erros de ligação HTTP.

* Adicionados novos argumentos de palavras-chave de configuração de construtor e por operação:

  * `retry_total` - Tentativas máximas de repetição.
  * `retry_backoff_max` - O tempo máximo de espera em segundos.
  * `retry_fixed_interval` - Intervalo de retíria fixo em milissegundos.
  * `retry_read` - Número máximo de tomadas que lêem tentativas de repetição.
  * `retry_connect` - Número máximo de tentativas de relagem de erros de ligação.
  * `retry_status` - Número máximo de tentativas de repetição de códigos de estado de erro.
  * `retry_on_status_codes` - Uma lista de códigos de estado específicos para voltar a tentar.
  * `retry_backoff_factor` - Fator para calcular o tempo de espera entre tentativas de repetição.

### <a name="400b3"></a>4.0.0b3

* Adicionado `create_database_if_not_exists()` e `create_container_if_not_exists` funcionalidades à CosmosClient e à Base de Dados, respectivamente.

### <a name="400b2"></a>4.0.0b2

* A versão 4.0.0b2 é a segunda iteração nos nossos esforços para construir uma biblioteca de clientes que se adapte às melhores práticas linguísticas python.

**Alterações interruptivas**

* A ligação ao cliente foi adaptada para consumir o gasoduto HTTP definido em `azure.core.pipeline` .

* Os objetos interativos foram agora renomeados como proxies. O que está incluído:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* O construtor foi `CosmosClient` atualizado:

  * O `auth` parâmetro foi renomeado e irá agora tomar `credential` um tipo de autenticação diretamente. Isto significa que o valor principal da chave, um dicionário de fichas de recursos, ou uma lista de permissões podem ser passados. No entanto, o antigo formato do dicionário ainda é suportado.

  * O `connection_policy` parâmetro foi feito apenas um parâmetro de palavra-chave, e enquanto ainda é suportado, cada um dos atributos individuais da política pode agora ser passado como argumentos explícitos de palavra-chave:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* Um novo construtor foi adicionado `CosmosClient` para permitir a criação através de uma cadeia de conexão recuperada a partir do portal Azure.

* Algumas `read_all` operações foram renomeadas para `list` operações:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Todas as operações que tomem `request_options` ou `feed_options` parâmetros, estas foram movidas apenas para parâmetros de palavras-chave. Além disso, embora estes dicionários de opções ainda sejam suportados, cada uma das opções individuais dentro do dicionário são agora suportadas como argumentos de palavra-chave explícitos.

* A hierarquia do erro é agora herdada `azure.core.AzureError` de:

  * `HTTPFailure` mudou de nome para `CosmosHttpResponseError`
  * `JSONParseFailure` foi removido e substituído por `azure.core.DecodeError`
  * Erros adicionais adicionados para códigos de resposta específicos:
    * `CosmosResourceNotFoundError` para o estatuto 404
    * `CosmosResourceExistsError` para o estatuto 409
    * `CosmosAccessConditionFailedError` para o estatuto 412

* `CosmosClient` pode agora ser executado em um gestor de contexto para lidar com o fecho da ligação ao cliente.

* As respostas iteráveis (por exemplo, respostas de consulta e respostas de lista) são agora de tipo `azure.core.paging.ItemPaged` . O método `fetch_next_block` foi substituído por um iterador secundário, acedido pelo `by_page` método.

### <a name="400b1"></a>4.0.0b1

A versão 4.0.0b1 é a primeira pré-visualização dos nossos esforços para criar uma biblioteca de clientes fácil de usar que se adapte às melhores práticas linguísticas da python. Para mais informações sobre isto, e pré-visualizar lançamentos de outras bibliotecas Azure SDK, https://aka.ms/azure-sdk-preview1-python visite.

**Mudanças de rutura: novo design da API**

* As operações são agora limitadas a um cliente em particular:

  * `CosmosClient`: Este cliente trata de operações de nível de conta. Isto inclui gerir propriedades de serviço e listar as bases de dados dentro de uma conta.
  * `Database`: Este cliente trata de operações de nível de base de dados. Isto inclui a criação e eliminação de contentores, utilizadores e procedimentos armazenados. Pode ser acedido a partir de um cosmosClient por exemplo.
  * `Container`: Este cliente trata das operações de um determinado contentor. Isto inclui consulta e inserção de itens e gestão de propriedades.
  * `User`: Este cliente trata das operações de um determinado utilizador. Isto inclui adicionar e eliminar permissões e gerir propriedades do utilizador.

    Estes clientes podem ser acedidos navegando pela hierarquia do cliente utilizando o `get_<child>_client` método. Para mais detalhes sobre a nova API, consulte a [documentação de referência.](https://aka.ms/azsdk-python-cosmos-ref)

* Os clientes são acedidos pelo nome e não pela ID. Não há necessidade de concatenar cordas para criar ligações.

* Não há mais necessidade de importar tipos e métodos de módulos individuais. A área de superfície pública da API está disponível diretamente no `azure.cosmos` pacote.

* As propriedades de pedido individual podem ser fornecidas como argumentos de palavra-chave em vez de construir uma `RequestOptions` instância separada.

### <a name="302"></a>3.0.2

* Suporte adicionado para o tipo de dados multiPolygon
* Correção de erro na sessão Leia a Política de Retíduo
* Correção de erro para problemas de enchimento incorretos ao descodindo as cordas base 64

### <a name="301"></a>3.0.1

* Correção de bugs na LocalizaçãoCache
* Correção de erro de erro de erro lógica
* Documentação fixa

### <a name="300"></a>3.0.0

* Multi-regiões escrevem apoio adicionado
* Alterações de nomeação
  * DocumentoClient para CosmosClient
  * Coleção para Contentor
  * Documento para Item
  * Nome do pacote atualizado para "azure-cosmos"
  * Espaço de nome atualizado para "azure.cosmos"

### <a name="233"></a>2.3.3

* Apoio adicional à procuração
* Apoio adicional para o feed de mudança de leitura
* Apoio adicional aos cabeçalhos de quotas de recolha
* Bugfix para emissão de tokens de sessão grande
* Bugfix para ReadMedia API
* Bugfix na cache da chave de partição

### <a name="232"></a>2.3.2

* Suporte adicional para reações padrão em questões de conexão.

### <a name="231"></a>2.3.1

* Documentação atualizada para referência Azure Cosmos DB em vez de Azure DocumentDB.

### <a name="230"></a>2.3.0

* Esta versão SDK requer a versão mais recente do Azure Cosmos DB Emulator disponível para download a partir de https://aka.ms/cosmosdb-emulator .

### <a name="221"></a>2.2.1

* bugfix para dict agregado
* bugfix para aparar cortes na ligação de recursos
* testes para codificação unicode

### <a name="220"></a>2.2.0

* Suporte adicional para unidade de pedido por minuto (RU/m).
* Apoio adicional para um novo nível de consistência chamado ConsistentPrefix.

### <a name="210"></a>2.1.0

* Suporte adicional para consultas de agregação (COUNT, MIN, MAX, SUM e AVG).
* Adicionou uma opção para desativar a verificação SSL ao correr contra o Emulador DocumentDB.
* Removido a restrição do módulo de pedidos dependentes para ser exatamente 2.10.0.
* Redução da produção mínima nas coleções divididas de 10.100 RU/s para 2500 RU/s.
* Suporte adicional para permitir a registo de scripts durante a execução do procedimento armazenado.
* A versão REST API saltou para '2017-01-19' com este lançamento.

### <a name="201"></a>2.0.1

* Fez alterações editoriais aos comentários documentais.

### <a name="200"></a>2.0.0

* Apoio adicional para Python 3.5.
* Suporte adicional para agrupamento de ligação utilizando o módulo de pedidos.
* Apoio adicional à consistência da sessão.
* Suporte adicional para consultas TOP/ORDERBY para coleções divididas.

### <a name="190"></a>1.9.0

* Acrescentou o apoio político de novo para pedidos acelerados. (Os pedidos de aceleração recebem uma taxa de pedido demasiado grande exceção, código de erro 429.) Por predefinição, o DocumentDB retriga nove vezes para cada pedido quando o código de erro 429 é encontrado, honrando a repetição após o tempo no cabeçalho de resposta.
  Um intervalo de repetição fixo pode agora ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy se quiser ignorar a retriptura após o tempo devolvido pelo servidor entre as retrações.
  O DocumentDB aguarda agora um máximo de 30 segundos por cada pedido que esteja a ser estrangulado (independentemente da contagem de novo) e devolve a resposta com o código de erro 429.
  Este tempo também pode ser ultrapassado na propriedade RetryOptions no objeto ConnectionPolicy.

* DocumentDB retorna agora x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms como os cabeçalhos de resposta em cada pedido para denotar a contagem de repetição do acelerador e o tempo cumulativo que o pedido esperou entre as retrações.

* Removido a classe RetryPolicy e a propriedade correspondente (retry_policy) exposta na classe document_client e introduziu uma classe RetryOptions expondo a propriedade RetryOptions na classe ConnectionPolicy que pode ser usada para anular algumas das opções de reagem padrão.

### <a name="180"></a>1.8.0

* Adicionou o suporte para contas de base de dados geo-replicadas.
* Teste correções para mover o anfitrião global e masterKey para as classes de teste individuais.

### <a name="170"></a>1.7.0

* Adicionou o suporte para a funcionalidade Time To Live (TTL) para documentos.

### <a name="161"></a>1.6.1

* Correções de bugs relacionadas com a partição do lado do servidor para permitir caracteres especiais no caminho da chave de partição.

### <a name="160"></a>1.6.0

* Adicionou o suporte para a funcionalidade de coleções partidárias do lado do servidor.

### <a name="150"></a>1.5.0

* Adicione uma estrutura de fragmento do lado do cliente ao SDK. Implementou as aulas de HashPartionResolver e RangePartitionResolver.

### <a name="142"></a>1.4.2

* Implementar Upsert. Novos métodos UpsertXXX adicionados para suportar a funcionalidade Upsert.
* Implementar ID-Based Encaminhamento. Sem alterações públicas da API, tudo muda internamente.

### <a name="130"></a>1.3.0

* Lançamento ignorado para alinhar o número da versão com outros SDKs

### <a name="120"></a>1.2.0

* Suporta o índice GeoSpatial.
* Valida a propriedade de ID para todos os recursos. As identificações para recursos não podem conter `?, /, #, \\` caracteres ou terminar com um espaço.
* Adiciona novo cabeçalho "progresso de transformação de índice" à ResourceResponse.

### <a name="110"></a>1.1.0

* Implementa a política de indexação V2

### <a name="101"></a>1.0.1

* Suporta a ligação proxy

## <a name="release--retirement-dates"></a>Liberação & datas de reforma

A Microsoft fornece a notificação com pelo menos **12 meses** de antecedência para retirar um SDK de forma a suavizar a transição para uma versão mais recente/suportada. As funcionalidades, características e otimizações novas só são adicionadas ao SDK atual, pelo que se recomenda que atualize sempre para a última versão dos SDKs o mais cedo possível.

> [!WARNING]
> Depois de 31 de agosto de 2022, a Azure Cosmos DB deixará de fazer correções de bugs ou fornecerá suporte às versões 1.x e 2.x do Azure Cosmos DB Python SDK para a SQL API. Se preferir não fazer upgrade, os pedidos enviados das versões 1.x e 2.x do SDK continuarão a ser servidos pelo serviço DB Azure Cosmos.

| Versão | Data da versão: | Data de Extinção |
| --- | --- | --- |
| [4.2.0](#420) |09 out, 2020 |--- |
| [4.1.0](#410) |10 de agosto de 2020 |--- |
| [4.0.0](#400) |20 de maio de 2020 |--- |
| [3.0.2](#302) |15 de novembro de 2018 |--- |
| [3.0.1](#301) |04 out, 2018 |--- |
| [2.3.3](#233) |08 de setembro de 2018 |31 de agosto de 2022 |
| [2.3.2](#232) |08 de maio de 2018 |31 de agosto de 2022 |
| [2.3.1](#231) |21 de dezembro de 2017 |31 de agosto de 2022 |
| [2.3.0](#230) |10 de novembro de 2017 |31 de agosto de 2022 |
| [2.2.1](#221) |29 set 2017 |31 de agosto de 2022 |
| [2.2.0](#220) |10 de maio de 2017 |31 de agosto de 2022 |
| [2.1.0](#210) |01 de maio de 2017 |31 de agosto de 2022 |
| [2.0.1](#201) |30 de outubro de 2016 |31 de agosto de 2022 |
| [2.0.0](#200) |29 de setembro de 2016 |31 de agosto de 2022 |
| [1.9.0](#190) |07 de julho de 2016 |31 de agosto de 2022 |
| [1.8.0](#180) |14 de junho de 2016 |31 de agosto de 2022 |
| [1.7.0](#170) |26 de abril de 2016 |31 de agosto de 2022 |
| [1.6.1](#161) |08 de abril de 2016 |31 de agosto de 2022 |
| [1.6.0](#160) |29 de março de 2016 |31 de agosto de 2022 |
| [1.5.0](#150) |03 de janeiro de 2016 |31 de agosto de 2022 |
| [1.4.2](#142) |06 de outubro de 2015 |31 de agosto de 2022 |
| 1.4.1 |06 de outubro de 2015 |31 de agosto de 2022 |
| [1.2.0](#120) |06 de agosto de 2015 |31 de agosto de 2022 |
| [1.1.0](#110) |09 de julho de 2015 |31 de agosto de 2022 |
| [1.0.1](#101) |25 de maio de 2015 |31 de agosto de 2022 |
| 1.0.0 |07 de abril de 2015 |31 de agosto de 2022 |
| 0.9.4 pré-lease |14 de janeiro de 2015 |29 de fevereiro de 2016 |
| 0.9.3 pré-lease |09 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.2 pré-lease |25 de novembro de 2014 |29 de fevereiro de 2016 |
| 0.9.1 pré-lease |23 de setembro de 2014 |29 de fevereiro de 2016 |
| 0.9.0 pré-lease |21 de agosto de 2014 |29 de fevereiro de 2016 |

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Cosmos DB, veja a página do serviço [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
