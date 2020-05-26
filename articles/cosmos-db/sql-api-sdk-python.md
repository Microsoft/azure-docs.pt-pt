---
title: Azure Cosmos DB SQL Python API, SDK & recursos
description: Saiba tudo sobre o SQL Python API e SDK, incluindo datas de lançamento, datas de reforma e alterações feitas entre cada versão do Azure Cosmos DB Python SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: 1610ba173f31ecee05b2816758eab2d7c6da98f9
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798453"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK para SQL API: Notas de lançamento e recursos

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Baixar SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Documentação da API**|[Documentação de referência da API python](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Instruções de instalação SDK**|[Instruções de instalação Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Introdução**|[Começar com o Python SDK](create-sql-api-python.md)|
|**Plataforma suportada atual**|[Python 2.7](https://www.python.org/downloads/) e [Python 3.5.3+](https://www.python.org/downloads/)|

## <a name="release-history"></a>História de lançamento

### <a name="400"></a>4.0.0

* Libertação estável.
* Adicionou httploggingPolicy ao oleoduto para permitir a passagem de um logger personalizado para cabeçalhos de pedido e resposta.

### <a name="400b6"></a>4.0.0b6

* Bug fixo em synchronized_request para APIs de mídia.
* Removido MediaReadMode e MediaRequestTimeout da ConnectionPolicy uma vez que os pedidos de mídia não são suportados.

### <a name="400b5"></a>4.0.0b5

* azure.cosmos.errors módulo depreciado e substituído por azure.cosmos.exceções
* Os parâmetros da condição de acesso foram depreciados a `access_condition` favor `if_match` de `if_none_match` parâmetros separados `match_condition` e `etag` parâmetros.
* Bug fixo no fornecedor de mapas de encaminhamento.
* Acrescentou consulta distinta, offset e suporte limit.
* Padrão documento de execução de consulta agora usado para

  * Consultas changeFeed
  * consultas únicas de partição (partitionkey, partitionKeyRangeId está presente em opções)
  * Consultas não documentais

* Erros para agregados em várias divisórias, com a consulta de divisória cruzada definida como verdadeira, mas sem palavra-chave de "valor" presente
* Atinge o ponto final do plano de consulta para outros cenários para buscar plano de consulta
* Suporte adicional `__repr__` para objetos de entidade cosmos.
* Documentação atualizada.

### <a name="400b4"></a>4.0.0b4

* Suporte adicional para um argumento de `timeout` palavra-chave a todas as operações para especificar um tempo de tempo absoluto em segundos em que a operação deve ser concluída. Se o valor do tempo for excedido, `azure.cosmos.errors.CosmosClientTimeoutError` um será aumentado.

* Adicioneum novo para gerir o comportamento de retry durante os erros de `ConnectionRetryPolicy` ligação HTTP.

* Adicione novos argumentos de palavra-chave de construção e de configuração por operação:

  * `retry_total`- Tentativas máximas de reprovação.
  * `retry_backoff_max`- Tempo máximo de espera em segundos.
  * `retry_fixed_interval`- Intervalo de repetição fixo em milissegundos.
  * `retry_read`- O número máximo de tomadas lê tentativas de retry.
  * `retry_connect`- Número máximo de tentativas de retry de erro de ligação.
  * `retry_status`- Número máximo de tentativas de reprovação dos códigos de estado de erro.
  * `retry_on_status_codes`- Uma lista de códigos de estado específicos para voltar a tentar.
  * `retry_backoff_factor`- Fator para calcular o tempo de espera entre tentativas de retry.

### <a name="400b3"></a>4.0.0b3

* Adicionadas `create_database_if_not_exists()` e `create_container_if_not_exists` funcionalidades à CosmosClient e à Base de Dados, respectivamente.

### <a name="400b2"></a>4.0.0b2

* A versão 4.0.0b2 é a segunda iteração nos nossos esforços para construir uma biblioteca de clientes que se adequa às melhores práticas da língua Python.

**Alterações interruptivas**

* A ligação do cliente foi adaptada para consumir o gasoduto HTTP definido em `azure.core.pipeline` .

* Os objetos interativos foram agora renomeados como proxies. Isto inclui:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* O construtor de `CosmosClient` foi atualizado:

  * O `auth` parâmetro foi renomeado e irá agora ter um tipo de `credential` autenticação diretamente. Isto significa que o valor-chave principal, um dicionário de fichas de recursos, ou uma lista de permissões pode ser passado. No entanto, o antigo formato do dicionário ainda é suportado.

  * O `connection_policy` parâmetro foi feito como um parâmetro de palavra-chave apenas, e enquanto ainda é suportado, cada um dos atributos individuais da política pode agora ser passado como argumentos explícitos de palavra-chave:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* Um novo construtor foi adicionado `CosmosClient` para permitir a criação através de uma cadeia de ligação recuperada do portal Azure.

* Algumas `read_all` operações foram renomeadas para `list` operações:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Todas as operações que tomam `request_options` ou `feed_options` parâmetros, estas foram movidas para apenas parâmetros de palavra-chave. Além disso, embora estas opções ainda sejam suportadas, cada uma das opções individuais dentro do dicionário são agora suportadas como argumentos explícitos de palavra-chave.

* A hierarquia de erros é agora herdada de `azure.core.AzureError` vez de, que foi removida.

  * `HTTPFailure` mudou de nome para `CosmosHttpResponseError`
  * `JSONParseFailure`foi removido e substituído por`azure.core.DecodeError`
  * Adicionaram erros adicionais para códigos de resposta específicos:
    * `CosmosResourceNotFoundError`para o estatuto 404
    * `CosmosResourceExistsError`para o estatuto 409
    * `CosmosAccessConditionFailedError`para o estado 412

* `CosmosClient`pode agora ser executado em um gestor de contexto para lidar com o fecho da ligação do cliente.

* As respostas iteráveis (por exemplo, respostas de consulta e respostas de listas) são agora de tipo `azure.core.paging.ItemPaged` . O método `fetch_next_block` foi substituído por um iterdor secundário, acessado pelo `by_page` método.

### <a name="400b1"></a>4.0.0b1

A versão 4.0.0b1 é a primeira pré-visualização dos nossos esforços para criar uma biblioteca de clientes que se adapte às melhores práticas linguísticas python. Para mais informações sobre este tipo, e pré-visualização de outras bibliotecas Azure SDK, visite https://aka.ms/azure-sdk-preview1-python .

**Mudanças de rutura: Novo design API**

* As operações são agora de um determinado cliente:

  * `CosmosClient`: Este cliente trata de operações ao nível da conta. Isto inclui gerir propriedades de serviço e listar as bases de dados dentro de uma conta.
  * `Database`: Este cliente trata de operações ao nível da base de dados. Isto inclui a criação e a eliminar contentores, utilizadores e procedimentos armazenados. Pode ser acedido a partir de um cosmosClient por nome.
  * `Container`: Este cliente trata das operações de um determinado contentor. Isto inclui consulta e inserção de itens e gestão de propriedades.
  * `User`: Este cliente trata de operações para um determinado utilizador. Isto inclui adicionar e apagar permissões e gerir as propriedades dos utilizadores.

    Estes clientes podem ser acedidos navegando pela hierarquia do cliente utilizando o `get_<child>_client` método. Para mais detalhes sobre a nova API, consulte a [documentação](https://aka.ms/azsdk-python-cosmos-ref)de referência .

* Os clientes são acedidos pelo nome e não pelo ID. Não há necessidade de concatenar cordas para criar ligações.

* Não é mais necessário importar tipos e métodos a partir de módulos individuais. A área de superfície da API pública está disponível diretamente no `azure.cosmos` pacote.

* As propriedades de pedido individuais podem ser fornecidas como argumentos de palavra-chave em vez de construir uma `RequestOptions` instância separada.

### <a name="302"></a>3.0.2

* Suporte adicional para datatype multiPolygon
* Correção de bugs na política de retry de leitura de sessão
* Correção de bugs para problemas de enchimento incorretos enquanto descodifica a base 64 cordas

### <a name="301"></a>3.0.1

* Correção de bugs em LocationCache
* Lógica de retry de ponto final de bug fix
* Documentação fixa

### <a name="300"></a>3.0.0

* Várias regiões escrevem apoio adicionado
* Alterações de nomeação
  * DocumentClient to CosmosClient
  * Recolha em Contentor
  * Documento ao Item
  * Nome do pacote atualizado para "azure-cosmos"
  * Espaço de nome atualizado para "azure.cosmos"

### <a name="233"></a>2.3.3

* Apoio adicional para procuração
* Suporte adicional para a leitura de alimentos para mudança
* Suporte adicional para cabeçalhos de quotas de recolha
* Bugfix para grande sessão tokens edição
* Bugfix para ReadMedia API
* Bugfix em cache de gama de chaves de divisória

### <a name="232"></a>2.3.2

* Suporte adicional para repetições por defeito em questões de ligação.

### <a name="231"></a>2.3.1

* Documentação atualizada para referência Azure Cosmos DB em vez de Azure DocumentDB.

### <a name="230"></a>2.3.0

* Esta versão SDK requer a versão mais recente do Emulador De DB Do Azure Cosmos disponível para download a partir de https://aka.ms/cosmosdb-emulator .

### <a name="221"></a>2.2.1

* bugfix para ditado agregado
* bugfix para cortar cortes na ligação de recursos
* testes para codificação unicódigo

### <a name="220"></a>2.2.0

* Suporte adicional para unidade de pedido por função Minute (RU/m).
* Suporte adicional para um novo nível de consistência chamado ConsistentPrefix.

### <a name="210"></a>2.1.0

* Suporte adicional para consultas de agregação (COUNT, MIN, MAX, SUM e AVG).
* Adicioneuma opção para desativar a verificação SSL ao concorrer contra o Emulador DocumentDB.
* Removeu a restrição do módulo de pedidos dependentes para ser exatamente 2.10.0.
* Baixada a entrada mínima em coleções divididas de 10.100 RU/s para 2500 RU/s.
* Suporte adicional para permitir a exploração de scripts durante a execução do procedimento armazenado.
* A versão REST API subiu para '2017-01-19' com esta versão.

### <a name="201"></a>2.0.1

* Fez alterações editoriais em comentários de documentação.

### <a name="200"></a>2.0.0

* Suporte adicional para Python 3.5.
* Suporte adicional para o agrupamento de ligação utilizando o módulo de pedidos.
* Suporte adicional para a consistência da sessão.
* Suporte adicional para consultas TOP/ORDERBY para coleções divididas.

### <a name="190"></a>1.9.0

* Acrescentou o apoio político de novo para pedidos acelerados. (Os pedidos de aceleração recebem uma taxa de pedido demasiado grande, código de erro 429.) Por predefinição, o DocumentDB retenta nove vezes por cada pedido quando o código de erro 429 é encontrado, honrando a retentativa Após o tempo no cabeçalho de resposta.
  Um intervalo de repetição fixo pode agora ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy se pretender ignorar o tempo de repetição devolvido pelo servidor entre as repetições.
  DocumentDB aguarda agora um máximo de 30 segundos por cada pedido que está a ser estrangulado (independentemente da contagem de tentativas) e devolve a resposta com o código de erro 429.
  Este tempo também pode ser ultrapassado na propriedade RetryOptions no objeto ConnectionPolicy.

* DocumentDB devolve agora x-ms-throttle-retry-count e x-ms-throttle-try-try-wait-ms como os cabeçalhos de resposta em cada pedido para denotar a contagem de aceleração e o tempo acumulado que o pedido esperou entre as tentativas.

* Removeu a classe RetryPolicy e a propriedade correspondente (retry_policy) exposta na classe document_client e introduziu uma classe RetryOptions expondo a propriedade RetryOptions na classe ConnectionPolicy que pode ser usada para anular algumas das opções de retry predefinidos.

### <a name="180"></a>1.8.0

* Acrescentou o suporte para contas de base de dados geo-replicadas.
* Correções de teste para mover o anfitrião global e masterKey para as classes individuais de teste.

### <a name="170"></a>1.7.0

* Acrescentou o suporte para a funcionalidade Time To Live (TTL) para documentos.

### <a name="161"></a>1.6.1

* Correções de bugs relacionadas com divisórias do lado do servidor para permitir caracteres especiais no caminho da chave da partilha.

### <a name="160"></a>1.6.0

* Adicione o suporte para a funcionalidade de coleções divididas do lado do servidor.

### <a name="150"></a>1.5.0

* Acrescentou a estrutura de sharding do lado do cliente ao SDK. Aulas de HashPartionResolver e RangePartitionResolver implementadas.

### <a name="142"></a>1.4.2

* Implementar Upsert. Novos métodos UpsertXXX adicionados para apoiar a funcionalidade Upsert.
* Implemente o encaminhamento baseado em ID. Sem alterações públicas da API, tudo muda internamente.

### <a name="130"></a>1.3.0

* Lançamento ignorado para trazer número de versão em alinhamento com outros SDKs

### <a name="120"></a>1.2.0

* Suporta o índice GeoSpatial.
* Valida a propriedade de ID para todos os recursos. Ids para recursos não podem conter `?, /, #, \\` caracteres ou terminar com um espaço.
* Adiciona novo cabeçalho "progresso de transformação de índice" à Resposta de Recursos.

### <a name="110"></a>1.1.0

* Implementa a política de indexação V2

### <a name="101"></a>1.0.1

* Suporta a ligação proxy

## <a name="release--retirement-dates"></a>Datas de & de reforma

A Microsoft fornece notificação com pelo menos **12 meses** de antecedência para a reforma de um SDK de forma a suavizar a transição para uma versão mais recente/suportada. Novas funcionalidades e funcionalidades e otimizações só são adicionadas ao SDK atual, como tal é recomendado que você sempre atual ize para a versão Mais recente SDK o mais cedo possível.

Qualquer pedido à Azure Cosmos DB utilizando um SDK reformado é rejeitado pelo serviço.

> [!WARNING]
> Todas as versões do Python SDK para SQL API antes da versão **1.0.0** foram retiradas em 29 de fevereiro de **2016**. E todas as versões 1.x e 2.x do Python SDK para SQL API serão retiradas a 30 de agosto de **2020**.

| Versão | Data de Lançamento | Data de Aposentadoria |
| --- | --- | --- |
| [4.0.0](#400) |20 de maio de 2020 |--- |
| [3.0.2](#302) |Nov 15, 2018 |--- |
| [3.0.1](#301) |04 out, 2018 |--- |
| [2.3.3](#233) |08 de setembro de 2018 |30 de agosto de 2020 |
| [2.3.2](#232) |08 de maio de 2018 |30 de agosto de 2020 |
| [2.3.1](#231) |21 de dezembro de 2017 |30 de agosto de 2020 |
| [2.3.0](#230) |10 de novembro de 2017 |30 de agosto de 2020 |
| [2.2.1](#221) |29 set, 2017 |30 de agosto de 2020 |
| [2.2.0](#220) |10 de maio de 2017 |30 de agosto de 2020 |
| [2.1.0](#210) |01 de maio de 2017 |30 de agosto de 2020 |
| [2.0.1](#201) |30 de outubro de 2016 |30 de agosto de 2020 |
| [2.0.0](#200) |29 de setembro de 2016 |30 de agosto de 2020 |
| [1.9.0](#190) |07 de julho de 2016 |30 de agosto de 2020 |
| [1.8.0](#180) |14 de junho de 2016 |30 de agosto de 2020 |
| [1.7.0](#170) |26 de abril de 2016 |30 de agosto de 2020 |
| [1.6.1](#161) |08 de abril de 2016 |30 de agosto de 2020 |
| [1.6.0](#160) |29 de março de 2016 |30 de agosto de 2020 |
| [1.5.0](#150) |03 de janeiro de 2016 |30 de agosto de 2020 |
| [1.4.2](#142) |06 de outubro de 2015 |30 de agosto de 2020 |
| 1.4.1 |06 de outubro de 2015 |30 de agosto de 2020 |
| [1.2.0](#120) |06 de agosto de 2015 |30 de agosto de 2020 |
| [1.1.0](#110) |09 de julho de 2015 |30 de agosto de 2020 |
| [1.0.1](#101) |25 de maio de 2015 |30 de agosto de 2020 |
| 1.0.0 |07 de abril de 2015 |30 de agosto de 2020 |
| 0.9.4-prelease |14 de janeiro de 2015 |29 de fevereiro de 2016 |
| 0.9.3-prelease |09 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.2-prelease |25 de novembro de 2014 |29 de fevereiro de 2016 |
| 0.9.1-prelease |23 de setembro de 2014 |29 de fevereiro de 2016 |
| 0.9.0-prelease |21 de agosto de 2014 |29 de fevereiro de 2016 |

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre cosmos DB, consulte a página de serviço [do Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/) 
