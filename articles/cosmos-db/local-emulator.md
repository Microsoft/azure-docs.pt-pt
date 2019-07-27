---
title: Desenvolver localmente com o emulador Cosmos do Azure
description: Usando o emulador Cosmos do Azure, você pode desenvolver e testar seu aplicativo localmente gratuitamente, sem criar uma assinatura do Azure.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 07/26/2019
ms.openlocfilehash: 626f71c3938b944cb705dfea4a964c6c33c42164
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565445"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Usar o emulador Cosmos do Azure para desenvolvimento e teste local

O emulador Cosmos do Azure fornece um ambiente local que emula o serviço Azure Cosmos DB para fins de desenvolvimento. Usando o emulador Cosmos do Azure, você pode desenvolver e testar seu aplicativo localmente, sem criar uma assinatura do Azure ou incorrer em custos. Quando estiver satisfeito com o funcionamento do seu aplicativo no emulador Cosmos do Azure, você poderá alternar para o uso de uma conta do Azure Cosmos na nuvem.

Você pode desenvolver usando o emulador Cosmos do Azure com as contas [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api)e API de [tabela](local-emulator.md#table-api) . No entanto, no momento, a exibição Data Explorer no emulador dá suporte total a clientes somente para a API do SQL. 

## <a name="how-the-emulator-works"></a>Como funciona o emulador

O emulador Cosmos do Azure fornece uma emulação de alta fidelidade do serviço Azure Cosmos DB. Ele dá suporte à funcionalidade idêntica à Azure Cosmos DB, incluindo suporte para criar e consultar dados, provisionar e dimensionar contêineres e executar procedimentos armazenados e gatilhos. Você pode desenvolver e testar aplicativos usando o emulador Cosmos do Azure e implantá-los no Azure em escala global fazendo apenas uma única alteração de configuração no ponto de extremidade de conexão para Azure Cosmos DB.

Apesar de a emulação do serviço do Azure Cosmos DB ser fiel, a implementação do emulador é diferente do serviço. Por exemplo, o emulador utiliza componentes standard do SO, como o sistema de ficheiros local para persistência e a pilha de protocolo HTTPS para conectividade. A funcionalidade que depende da infraestrutura do Azure, como replicação global, latência de milissegundos de dígito único para leituras/gravações e níveis de consistência ajustáveis, não é aplicável.

Você pode migrar dados entre o emulador Cosmos do Azure e o serviço de Azure Cosmos DB usando a [ferramenta de migração de dados Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

Você pode executar o emulador Cosmos do Azure no contêiner do Docker do Windows, consulte o [Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) do Docker para o comando Docker pull e o [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) para o código-fonte do emulador.

## <a name="differences-between-the-emulator-and-the-service"></a>Diferenças entre o emulador e o serviço

Como o emulador Cosmos do Azure fornece um ambiente emulado em execução na estação de trabalho do desenvolvedor local, há algumas diferenças na funcionalidade entre o emulador e uma conta do Azure Cosmos na nuvem:

* Atualmente Data Explorer no emulador dá suporte a clientes para a API do SQL. A exibição de Data Explorer e as operações para APIs de Azure Cosmos DB como MongoDB, tabela, grafo e APIs Cassandra não são totalmente suportadas.
* O emulador Cosmos do Azure dá suporte a apenas uma única conta fixa e uma chave mestra conhecida. A regeneração de chave não é possível no emulador Cosmos do Azure, no entanto, a chave padrão pode ser alterada usando a opção de linha de comando.
* O emulador Cosmos do Azure não é um serviço escalonável e não dará suporte a um grande número de contêineres.
* O emulador Cosmos do Azure não oferece diferentes [níveis de consistência de Azure Cosmos DB](consistency-levels.md).
* O emulador Cosmos do Azure não oferece [replicação de várias regiões](distribute-data-globally.md).
* Como sua cópia do emulador Cosmos do Azure pode não estar sempre atualizada com as alterações mais recentes no serviço Azure Cosmos DB, você deve consultar o [Azure Cosmos DB planejador de capacidade](https://www.documentdb.com/capacityplanner) para estimar com precisão as necessidades de transferência de produção (RUs) do seu aplicativo.
* Ao usar o emulador Cosmos do Azure, por padrão, você pode criar até 25 contêineres de tamanho fixo (somente com suporte usando SDKs Azure Cosmos DB) ou 5 contêineres ilimitados usando o emulador Cosmos do Azure. Para obter mais informações sobre como alterar este valor, veja [Definir o valor de PartitionCount](#set-partitioncount).

## <a name="system-requirements"></a>Requisitos de sistema

O emulador Cosmos do Azure tem os seguintes requisitos de hardware e software:

* Requisitos de software
  * Windows Server 2012 R2, Windows Server 2016 ou Windows 10
  * sistema operacional de 64 bits
* Requisitos Mínimos de Hardware
  * 2 GB de RAM
  * 10 GB de espaço disponível no disco rígido

## <a name="installation"></a>Instalação

Você pode baixar e instalar o emulador Cosmos do Azure no [centro de download da Microsoft](https://aka.ms/cosmosdb-emulator) ou pode executar o emulador em Docker for Windows. Para obter instruções sobre como utilizar o emulador do Docker para Windows, veja [Executar no Docker](#running-on-docker).

> [!NOTE]
> Para instalar, configurar e executar o emulador Cosmos do Azure, você deve ter privilégios administrativos no computador. O emulador criará/adicionará um certificado e também definirá as regras de firewall para executar seus serviços; Portanto, é necessário que o emulador seja capaz de executar essas operações.

## <a name="running-on-windows"></a>Executar no Windows

Para iniciar o emulador Cosmos do Azure, selecione o botão iniciar ou pressione a tecla Windows. Comece digitando o **emulador Cosmos do Azure**e selecione o emulador na lista de aplicativos.

![Selecione o botão iniciar ou pressione a tecla Windows, comece digitando * * emulador Cosmos do Azure * * e selecione o emulador na lista de aplicativos](./media/local-emulator/database-local-emulator-start.png)

Quando o emulador estiver em execução, verá um ícone na área de notificação da barra de tarefas do Windows. ![Azure Cosmos DB notificação da barra de tarefas do emulador local](./media/local-emulator/database-local-emulator-taskbar.png)

Por padrão, o emulador Cosmos do Azure é executado no computador local ("localhost") escutando na porta 8081.

O emulador Cosmos do Azure é `C:\Program Files\Azure Cosmos DB Emulator` instalado por padrão. Também pode iniciar e parar o emulador a partir da linha de comandos. Para obter mais informações, veja a [referência da ferramenta de linha de comandos](#command-line).

## <a name="start-data-explorer"></a>Iniciar o Data Explorer

Quando o emulador Cosmos do Azure é iniciado, ele abre automaticamente o Azure Cosmos Data Explorer no navegador. O endereço aparece como `https://localhost:8081/_explorer/index.html`. Se você fechar o Explorer e quiser reabri-lo mais tarde, poderá abrir a URL no navegador ou iniciá-lo no emulador Cosmos do Azure no ícone de bandeja do Windows, conforme mostrado abaixo.

![Iniciador do Gerenciador de dados do emulador local do Azure Cosmos](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Verificar atualizações

O Data Explorer indica se está disponível uma nova atualização para transferência.

> [!NOTE]
> Os dados criados em uma versão do emulador Cosmos do Azure (consulte%LOCALAPPDATA%\CosmosDBEmulator ou configurações opcionais de caminho de dados) não têm garantia de serem acessíveis ao usar uma versão diferente. Se você precisar manter seus dados por um longo prazo, é recomendável armazená-los em uma conta do Azure Cosmos, em vez de no emulador Cosmos do Azure.

## <a name="authenticating-requests"></a>Autenticar pedidos

Assim como ocorre com Azure Cosmos DB na nuvem, cada solicitação feita no emulador Cosmos do Azure deve ser autenticada. O emulador Cosmos do Azure dá suporte a uma única conta fixa e uma chave de autenticação conhecida para autenticação de chave mestra. Essa conta e a chave são as únicas credenciais permitidas para uso com o emulador Cosmos do Azure. São:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> A chave mestra com suporte do emulador Cosmos do Azure destina-se ao uso somente com o emulador. Você não pode usar sua conta de Azure Cosmos DB de produção e a chave com o emulador Cosmos do Azure.

> [!NOTE]
> Se você tiver iniciado o emulador com a opção/Key, use a chave gerada em vez `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`de. Para obter mais informações sobre a opção/Key, consulte [referência de ferramenta de linha de comando.](#command-line-syntax)

Assim como ocorre com o Azure Cosmos DB, o emulador Cosmos do Azure dá suporte apenas à comunicação segura via SSL.

## <a name="running-on-a-local-network"></a>Executar numa rede local

Pode executar o emulador numa rede local. Para habilitar o acesso à rede, `/AllowNetworkAccess` especifique a opção na [linha de comando](#command-line-syntax), que também requer que você `/Key=key_string` especifique `/KeyFile=file_name`ou. Você pode usar `/GenKeyFile=file_name` para gerar um arquivo com uma chave aleatória antecipada. Em seguida, você pode passá `/KeyFile=file_name` - `/Key=contents_of_file`lo para ou.

Para habilitar o acesso à rede pela primeira vez, o usuário deve desligar o emulador e excluir o diretório de dados do emulador (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Desenvolver com o emulador

### <a name="sql-api"></a>SQL API

Depois que o emulador Cosmos do Azure estiver em execução na área de trabalho, você poderá usar qualquer [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) com suporte ou a [API REST Azure Cosmos DB](/rest/api/cosmos-db/) para interagir com o emulador. O emulador Cosmos do Azure também inclui um Data Explorer interno que permite criar contêineres para a API do SQL ou Cosmos DB para a API do Mongo DB e exibir e editar itens sem escrever nenhum código.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

Depois que o emulador Cosmos do Azure estiver em execução na área de trabalho, você poderá usar a [API do Azure Cosmos DB para MongoDB](mongodb-introduction.md) para interagir com o emulador. Inicie o emulador do prompt de comando como administrador com "/EnableMongoDbEndpoint". Em seguida, use a seguinte cadeia de conexão para se conectar à conta da API do MongoDB:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>API de Tabela

Depois que o emulador Cosmos do Azure estiver em execução na área de trabalho, você poderá usar o [Azure Cosmos DB API de tabela SDK](table-storage-how-to-use-dotnet.md) para interagir com o emulador. Inicie o emulador do prompt de comando como administrador com "/EnableTableEndpoint". Em seguida, execute o seguinte código para se conectar à conta da API de tabela:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>API de Cassandra

Inicie o emulador de um prompt de comando de administrador com "/EnableCassandraEndpoint". Como alternativa, você também pode definir a variável `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`de ambiente.

* [Instalar o Python 2,7](https://www.python.org/downloads/release/python-2716/)

* [Instalar a CLI/CQLSH do Cassandra](https://cassandra.apache.org/download/)

* Execute os seguintes comandos em uma janela de prompt de comando regular:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* No Shell CQLSH, execute os seguintes comandos para se conectar ao ponto de extremidade Cassandra:

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>API do Gremlin

Inicie o emulador de um prompt de comando de administrador com "/EnableGremlinEndpoint". Como alternativa, você também pode definir a variável de ambiente`AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Instalar o Apache-tinkerpop-Gremlin-console-3.3.4](https://tinkerpop.apache.org/downloads.html)

* No Data Explorer do emulador, crie um banco de dados "DB1" e uma coleção "coll1"; para a chave de partição, escolha "/Name"

* Execute os seguintes comandos em uma janela de prompt de comando regular:

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* No Shell Gremlin, execute os seguintes comandos para se conectar ao ponto de extremidade Gremlin:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-ssl-certificate"></a>Exportar o certificado SSL

As linguagens .NET e o runtime utilizam o Arquivo de Certificados do Windows para ligar de forma segura ao emulador local do Azure Cosmos DB. Outras linguagens têm o seu próprio método de gerir e utilizar certificados. O Java utiliza o seu próprio [arquivo de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), enquanto o Python utiliza [wrappers de sockets](https://docs.python.org/2/library/ssl.html).

Para obter um certificado a utilizar com linguagens e runtimes que não se integram com o Arquivo de Certificados do Windows, terá de exportá-lo com o Gestor de Certificados do Windows. Você pode iniciá-lo executando certlm. msc ou seguir as instruções passo a passo em [exportar os certificados do emulador Cosmos do Azure](./local-emulator-export-ssl-certificates.md). Assim que o gestor de certificados estiver em execução, abra os Certificados Pessoais, conforme mostrado abaixo, e exporte o certificado com o nome amigável "DocumentDBEmulatorCertificate" como um ficheiro X.509 codificado com BASE-64 (.cer).

![Certificado SSL do emulador local do Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

O certificado X.509 pode ser importado para o arquivo de certificados do Java, seguindo as instruções em [Adicionar um Certificado ao Arquivo de Certificados de AC do Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Depois que o certificado for importado para o repositório de certificados, os clientes para SQL e a API do Azure Cosmos DB para MongoDB poderão se conectar ao emulador Cosmos do Azure.

Quando ligar ao emulador a partir dos SDKs de Python e Node.js, a verificação de SSL é desativada.

## <a id="command-line"></a>Referência da ferramenta de linha de comandos
No local de instalação, você pode usar a linha de comando para iniciar e parar o emulador, configurar opções e executar outras operações.

### <a name="command-line-syntax"></a>Sintaxe da linha de comandos

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

Para ver a lista de opções, escreva `CosmosDB.Emulator.exe /?` na linha de comandos.

|**Opção** | **Descrição** | **Comando**| **Argumentos**|
|---|---|---|---|
|[Sem argumentos] | Inicia o emulador Cosmos do Azure com as configurações padrão. |CosmosDB.Emulator.exe| |
|[Ajuda] |Mostra a lista dos argumentos da linha de comandos suportados.|CosmosDB.Emulator.exe /? | |
| GetStatus |Obtém o status do emulador Cosmos do Azure. O status é indicado pelo código de saída: 1 = Iniciando, 2 = em execução, 3 = parado. Um código de saída negativo indica que ocorreu um erro. Não é produzido outro resultado. | CosmosDB.Emulator.exe /GetStatus| |
| Encerrar| Desliga o emulador Cosmos do Azure.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Especifica o caminho onde pretende armazenar os ficheiros de dados. O valor padrão é%LocalAppdata%\CosmosDBEmulator. | CosmosDB.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: Um caminho acessível |
|Port | Especifica o número de porta a utilizar para o emulador. O valor padrão é 8081. |CosmosDB.Emulator.exe /Port=\<port\> | \<porta\>: Número da porta única |
| ComputePort | Especificado o número da porta a ser usado para o serviço de gateway de interoperabilidade de computação. A porta de investigação do ponto de extremidade HTTP do gateway é calculada como ComputePort + 79. Portanto, ComputePort e ComputePort + 79 devem estar abertos e disponíveis. Os valores padrão são 8900, 8979. | CosmosDB.Emulator.exe /ComputePort = \<computeport\> | \<computeport\>: Número da porta única |
| EnableMongoDbEndpoint | Habilita a API do MongoDB | CosmosDB. Emulator. exe/EnableMongoDbEndpoint | |
| MongoPort | Especifica o número de porta a utilizar para a API de compatibilidade do MongoDB. O valor padrão é 10255. |CosmosDB. Emulator. exe/MongoPort = \<MongoPort\>|\<mongoport\>: Número da porta única|
| EnableCassandraEndpoint | Habilita API do Cassandra | CosmosDB.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Especifica o número da porta a ser usada para o ponto de extremidade Cassandra. O valor padrão é 10350. | CosmosDB.Emulator.exe /CassandraPort = \<cassandraport\> | \<cassandraport\>: Número da porta única |
| EnableGremlinEndpoint | Habilita a API do Gremlin | CosmosDB. Emulator. exe/EnableGremlinEndpoint | |
| GremlinPort | Número da porta a ser usada para o ponto de extremidade Gremlin. O valor padrão é 8901. | CosmosDB.Emulator.exe /GremlinPort=\<port\> | \<porta\>: Número da porta única |
|EnableTableEndpoint | Habilita o Azure API de Tabela | CosmosDB.Emulator.exe /EnableTableEndpoint | |
|TablePort | Número da porta a ser usada para o ponto de extremidade de tabela do Azure. O valor padrão é 8902. | CosmosDB. Emulator. exe/TablePort =\<Port\> | \<porta\>: Número da porta única|
| KeyFile | Ler a chave de autorização do arquivo especificado. Use a opção/GenKeyFile para gerar um keyfile | CosmosDB.Emulator.exe /KeyFile=\<file_name\> | \<nome_do_arquivo\>: Caminho para o arquivo |
| ResetDataPath | Remove recursivamente todos os arquivos no caminho especificado. Se você não especificar um caminho, o padrão será%LOCALAPPDATA%\CosmosDbEmulator | CosmosDB.Emulator.exe /ResetDataPath[=\<path>] | \<path\>: Caminho do ficheiro  |
| StartTraces  |  Comece coletando logs de rastreamento de depuração. | CosmosDB.Emulator.exe /StartTraces | |
| StopTraces     | Parar coleta de logs de rastreamento de depuração. | CosmosDB.Emulator.exe /StopTraces  | |
|FailOnSslCertificateNameMismatch | Por padrão, o emulador regenera seu certificado SSL autoassinado, se o SAN do certificado não incluir o nome de domínio do host do emulador, endereço IPv4 local, ' localhost ' e ' 127.0.0.1 '. Com essa opção, o emulador falhará na inicialização em vez disso. Em seguida, você deve usar a opção/GenCert para criar e instalar um novo certificado SSL autoassinado. | CosmosDB.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Gere e instale um novo certificado SSL autoassinado. Opcionalmente, incluindo uma lista separada por vírgulas de nomes DNS adicionais para acessar o emulador pela rede. | CosmosDB. Emulator. exe/GenCert [ \<lista separada por vírgulas de nomes\>DNS adicionais] | |
| DirectPorts |Especifica as portas a utilizar para conectividade direta. As predefinições são 10251,10252,10253,10254. | CosmosDB.Emulator.exe /DirectPorts:\<directports\> | \<portasdiretas\>: Lista delimitada por vírgula de 4 portas |
| Chave |Chave de autorização para o emulador. A chave tem de ser a codificação base 64 de um vetor de 64 bytes. | CosmosDB.Emulator.exe /Key:\<key\> | \<chave\>: A chave deve ser a codificação base-64 de um vetor de 64 bytes|
| EnableRateLimiting | Especifica que o comportamento de limitação da taxa de pedidos está ativado. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Especifica que o comportamento de limitação da taxa de pedidos está desativado. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | Não mostrar a interface de utilizador do emulador. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | Não mostrar o explorador de dados no arranque. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | Especifica o número máximo de contêineres particionados. Consulte [alterar o número de contêineres](#set-partitioncount) para obter mais informações. | CosmosDB.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitionCount\>: Número máximo de contêineres de partição única permitidos. O valor predefinido é 25. O máximo permitido é 250.|
| DefaultPartitionCount| Especifica o número padrão de partições para um contêiner particionado. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<o\> valor padrão de defaultpartitioncount é 25.|
| AllowNetworkAccess | Permite o acesso ao emulador através de uma rede. Tem de passar também /Key=\<key_string\> oru/KeyFile=\<file_name\> para ativar o acesso à rede. | CosmosDB. Emulator. exe/AllowNetworkAccess/Key =\<key_string\> ou CosmosDB. Emulator. exe/AllowNetworkAccess/keyfile =\<file_name\>| |
| NoFirewall | Não ajuste as regras de firewall quando a opção/AllowNetworkAccess for usada. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Gerar uma nova chave de autorização e guardá-la no ficheiro especificado. A chave gerada pode ser utilizada com as opções /Key ou /KeyFile. | CosmosDB. Emulator. exe/GenKeyFile =\<caminho para o arquivo de chave\> | |
| Consistência | Defina o nível de consistência predefinida para a conta. | CosmosDB.Emulator.exe /Consistency=\<consistency\> | \<consistência\>: O valor deve ser um dos seguintes [níveis de consistência](consistency-levels.md): Sessão, forte, eventual ou BoundedStaleness. O valor predefinido é Session. |
| ? | Mostrar a mensagem de ajuda.| | |

## <a id="set-partitioncount"></a>Alterar o número de contêineres

Por padrão, você pode criar até 25 contêineres de tamanho fixo (somente com suporte usando SDKs de Azure Cosmos DB) ou 5 contêineres ilimitados usando o emulador Cosmos do Azure. Ao modificar o valor de **PartitionCount** , você pode criar até 250 contêineres de tamanho fixo ou 50 contêineres ilimitados ou qualquer combinação dos dois que não exceda 250 contêineres de tamanho fixo (em que um contêiner ilimitado = 5 contêineres de tamanho fixo). No entanto, não é recomendável configurar o emulador para ser executado com mais de 200 contêineres de tamanho fixo. Devido à sobrecarga que ele adiciona às operações de e/s de disco, o que resulta em tempos limite imprevisíveis ao usar as APIs de ponto de extremidade.

Se você tentar criar um contêiner depois que a contagem de partições atual tiver sido excedida, o emulador lançará uma exceção ServiceUnavailable, com a seguinte mensagem.

"No momento, estamos experimentando alta demanda nesta região e não podemos atender à sua solicitação no momento. Trabalhamos continuamente para colocar cada vez mais capacidade online e incentivar você a tentar novamente.
Não hesite em enviar emails askcosmosdb@microsoft.com a qualquer momento ou por qualquer motivo.
ActivityId 12345678-1234-1234-1234-123456789abc"

Para alterar o número de contêineres disponíveis no emulador Cosmos do Azure, execute as seguintes etapas:

1. Exclua todos os dados locais do emulador Cosmos do Azure clicando com o botão direito do mouse no ícone do **emulador Azure Cosmos DB** na bandeja do sistema e, em seguida, clicando em **Redefinir dados...** .
2. Exclua todos os dados do emulador nesta pasta `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Saia de todas as instâncias abertas, ao clicar com o botão direito do rato no ícone do**Emulador do Azure Cosmos DB** no tabuleiro do sistema e, em seguida, clique em **Sair**. Pode demorar um minuto para que todas as instâncias possam sair.
4. Instale a versão mais recente do [emulador Cosmos do Azure](https://aka.ms/cosmosdb-emulator).
5. Inicie o emulador com o sinalizador de PartitionCount, ao definir um valor <= 250. Por exemplo: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Controlar o emulador

O emulador vem com um módulo do PowerShell para iniciar, parar, desinstalar e recuperar o status do serviço. Execute o seguinte cmdlet para usar o módulo do PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

ou coloque o `PSModules` diretório em seu `PSModulesPath` e importe-o conforme mostrado no seguinte comando:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Eis um resumo dos comandos para controlar o emulador a partir do PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Sintaxe**

`Get-CosmosDbEmulatorStatus`

**Observações**

Retorna um destes valores de ServiceControllerStatus: ServiceControllerStatus. StartPending, ServiceControllerStatus. running ou ServiceControllerStatus. Stopped.

### `Start-CosmosDbEmulator`

**Sintaxe**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Observações**

Inicia o emulador. Por predefinição, o comando aguarda até que o emulador esteja pronto para aceitar pedidos. Utilize a opção -NoWait, se quiser que o cmdlet seja devolvido assim que inicia o emulador.

### `Stop-CosmosDbEmulator`

**Sintaxe**

 `Stop-CosmosDbEmulator [-NoWait]`

**Observações**

Para o emulador. Por predefinição, este comando aguarda até que o emulador seja totalmente encerrado. Utilize a opção -NoWait, se quiser que o cmdlet seja devolvido assim que o emulador começar a encerrar.

### `Uninstall-CosmosDbEmulator`

**Sintaxe**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Observações**

Desinstala o emulador e, opcionalmente, remove os conteúdos de $env:LOCALAPPDATA\CosmosDbEmulator.
O cmdlet assegura que o emulador está parado antes de desinstalá-lo.

## <a name="running-on-docker"></a>Em execução no Docker

O emulador Cosmos do Azure pode ser executado em Docker for Windows. O emulador não funciona no Docker para Oracle Linux.

Assim que tiver instalado o [Docker para Windows](https://www.docker.com/docker-windows), mude para os contentores do Windows, ao clicar com o botão direito do rato no ícone do Docker na barra de ferramentas e selecionar **Mudar para os contentores do Windows**.

Em seguida, extraia a imagem do emulador do Hub do Docker, executando o seguinte comando a partir da sua shell favorita.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Para iniciar a imagem, execute os seguintes comandos.

Na linha de comandos:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator --rm
```

> [!NOTE]
> Se você vir um erro de conflito de porta (a porta especificada já está em uso) ao executar o comando Docker execute, você poderá passar uma porta personalizada alterando os números de porta. Por exemplo, você pode alterar "-p 8081:8081" para "-p 443:8081"

Do PowerShell:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

```

A resposta é semelhante à seguinte:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Agora, utilize o ponto final e a chave mestra da resposta no cliente e importe o certificado SSL para o anfitrião. Para importar o certificado SSL, efetue o seguinte a partir de uma linha de comandos de administrador:

Na linha de comandos:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

Do PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Fechar a shell interativa assim que o emulador tiver sido iniciado encerrará o contentor do emulador.

Para abrir o Data Explorer, navegue para o seguinte URL no browser. O ponto final do emulador é fornecido na mensagem de resposta mostrada acima.

    https://<emulator endpoint provided in response>/_explorer/index.html

## Em execução no Mac ou Linux<a id="mac"></a>

Atualmente, o emulador Cosmos só pode ser executado no Windows. Os usuários que executam o Mac ou Linux podem executar o emulador em uma máquina virtual do Windows que hospedava um hipervisor, como paralelos ou VirtualBox. Abaixo estão as etapas para habilitar isso.

Na VM do Windows, execute o comando a seguir e anote o endereço IPv4.

```cmd
ipconfig.exe
```

Em seu aplicativo, você precisa alterar o URI do objeto DocumentClient para usar o endereço IPv4 retornado por `ipconfig.exe`. A próxima etapa é contornar a validação da autoridade de certificação ao construir o objeto DocumentClient. Para isso, você precisará fornecer um HttpClientHandler para o Construtor DocumentClient, que tem sua própria implementação para o ServerCertificateCustomValidationCallback.

Veja abaixo um exemplo de como deve ser o código.

```csharp
using System;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using System.Net.Http;

namespace emulator
{
    class Program
    {
        static async void Main(string[] args)
        {
            string strEndpoint = "https://10.135.16.197:8081/";  //IPv4 address from ipconfig.exe
            string strKey = "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==";

            //Work around the CA validation
            var httpHandler = new HttpClientHandler()
            {
                ServerCertificateCustomValidationCallback = (req,cert,chain,errors) => true
            };

            //Pass http handler to document client
            using (DocumentClient client = new DocumentClient(new Uri(strEndpoint), strKey, httpHandler))
            {
                Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "myDatabase" });
                Console.WriteLine($"Created Database: id - {database.Id} and selfLink - {database.SelfLink}");
            }
        }
    }
}
```

Por fim, de dentro da VM do Windows, inicie o emulador Cosmos na linha de comando usando as opções a seguir.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

## <a name="troubleshooting"></a>Resolução de problemas

Use as dicas a seguir para ajudar a solucionar problemas encontrados com o emulador Cosmos do Azure:

- Se instalou uma nova versão do emulador e surgirem erros, certifique-se de que repõe os dados. Você pode redefinir seus dados clicando com o botão direito do mouse no ícone do emulador Cosmos do Azure na bandeja do sistema e, em seguida, clicando em Redefinir dados.... Se isso não corrigir os erros, você poderá desinstalar o emulador e qualquer versão mais antiga do emulador, se for encontrado, remover o diretório "C:\Program Programas\azure Cosmos DB Emulator" e reinstalar o emulador. Veja [Desinstalar o emulador local](#uninstall) para obter instruções.

- Se o emulador Cosmos do Azure falhar, colete os arquivos de despejo da pasta '%LOCALAPPDATA%\CrashDumps ', compacte-os e anexe [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)-os a um email para.

- Se você tiver falhas no `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, isso pode ser um sintoma em que os contadores de desempenho estão em um estado corrompido. Geralmente, executar o seguinte comando em um prompt de comando de administrador corrige o problema:

  ```cmd
  lodctr /R
   ```

- Se ocorrer um problema de conectividade, [recolha os ficheiros de rastreio](#trace-files), comprima-os e anexe-os a uma mensagem de e-mail para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Se receber uma mensagem de **serviço indisponível**, o emulador poderá estar a falhar ao inicializar a pilha de rede. Verifique se tem as redes de cliente seguro Pulse ou Juniper instaladas, dado que os respetivos controladores de filtro de rede poderão causar o problema. Desinstalar os controladores de filtro de rede de terceiros normalmente corrige o problema. Como alternativa, inicie o emulador com/DisableRIO, que mudará a comunicação de rede do emulador para o Winsock normal. 

- Enquanto o emulador estiver em execução, se o computador entrar no modo de suspensão ou executar quaisquer atualizações do SO, poderá ver a mensagem **O serviço está indisponível neste momento**. Redefina os dados do emulador clicando com o botão direito do mouse no ícone que aparece na bandeja de notificação do Windows e selecione **Redefinir dados**.

### <a id="trace-files"></a>Recolher ficheiros de rastreio

Para recolher rastreios de depuração, execute os seguintes comandos a partir de uma linha de comandos administrativa:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Observe o tabuleiro do sistema para certificar-se de que o programa foi encerrado; poderá demorar um minuto. Você também pode simplesmente clicar em **sair** na interface do usuário do emulador Cosmos do Azure.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reproduza o problema. Se o Data Explorer não estiver a funcionar, apenas terá de aguardar que o browser abra por alguns segundos para apanhar o erro.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Navegue para `%ProgramFiles%\Azure Cosmos DB Emulator` e localize o ficheiro docdbemulator_000001.etl.
7. Envie o ficheiro.etl juntamente com os passos de reprodução para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com), para depuração.

### <a id="uninstall"></a>Desinstalar o emulador local

1. Saia de todas as instâncias abertas do emulador local clicando com o botão direito do mouse no ícone do emulador Cosmos do Azure na bandeja do sistema e clicando em sair. Pode demorar um minuto para que todas as instâncias possam sair.
2. Na caixa de pesquisa do Windows, escreva **Aplicações e funcionalidades** e clique no resultado **Aplicações e funcionalidades (Definições do sistema)** .
3. Na lista de aplicações, desloque para **Emulador do Azure Cosmos DB**, selecione-o, clique em **Desinstalar** e, em seguida, confirme e clique em **Desinstalar** novamente.
4. Quando a aplicação estiver desinstalada, navegue para `%LOCALAPPDATA%\CosmosDBEmulator` e elimine a pasta.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar o emulador local para desenvolvimento local gratuito. Agora pode avançar para o tutorial seguinte e ficar a saber como exportar certificados SSL do emulador.

> [!div class="nextstepaction"]
> [Exportar os certificados do emulador Cosmos do Azure](local-emulator-export-ssl-certificates.md)
