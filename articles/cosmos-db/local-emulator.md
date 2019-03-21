---
title: Desenvolver localmente com o emulador do Cosmos do Azure
description: Utilizar o emulador do Cosmos do Azure, pode desenvolver e testar a sua aplicação localmente para obter gratuitamente, sem criar uma subscrição do Azure.
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 04/20/2018
author: deborahc
ms.author: dech
ms.openlocfilehash: 0adb24458f718511c7134fc3bf36dd0b03173e30
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011505"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Utilizar o emulador do Cosmos para o local de desenvolvimento e teste

O emulador do Cosmos do Azure fornece um ambiente local que emula o serviço do Azure Cosmos DB para fins de desenvolvimento. Utilizar o emulador do Cosmos do Azure, pode desenvolver e testar a aplicação localmente, sem criar uma subscrição do Azure ou incorrer em custos. Quando estiver satisfeito com o funcionamento da sua aplicação no emulador do Cosmos do Azure, pode mudar para utilizar uma conta do Cosmos do Azure na cloud.

Pode desenvolver com o emulador do Cosmos do Azure com [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api), e [tabela](local-emulator.md#table-api) Contas da API. No entanto neste momento a vista de Explorador de dados no emulador totalmente compatível com os clientes para a API de SQL apenas. 

## <a name="how-the-emulator-works"></a>Como funciona o emulador

O emulador do Cosmos do Azure fornece uma emulação de alta fidelidade do serviço do Azure Cosmos DB. Suporta a funcionalidade idêntica, como o Azure Cosmos DB, incluindo suporte para criar e consultar dados, aprovisionamento e dimensionar contentores e a execução de procedimentos armazenados e acionadores. Pode desenvolver e testar aplicações com o emulador do Cosmos do Azure e implementá-las para o Azure à escala global, apenas fazendo uma única alteração de configuração para o ponto final de ligação para o Azure Cosmos DB.

Apesar de a emulação do serviço do Azure Cosmos DB ser fiel, a implementação do emulador é diferente do serviço. Por exemplo, o emulador utiliza componentes standard do SO, como o sistema de ficheiros local para persistência e a pilha de protocolo HTTPS para conectividade. A funcionalidade que se baseia numa infraestrutura do Azure, como replicação global, a latência de milissegundo de dígito para leituras e gravações e níveis de consistência sincronizáveis não são aplicáveis.

Pode migrar dados entre o emulador do Cosmos do Azure e o serviço do Azure Cosmos DB ao utilizar o [ferramenta de migração de dados do Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

Pode executar o emulador do Cosmos do Azure no contentor de Docker do Windows, consulte a [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) para o comando de pull do docker e [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) para o código de origem do emulador.

## <a name="differences-between-the-emulator-and-the-service"></a>Diferenças entre o emulador e o serviço

Uma vez que o emulador do Cosmos do Azure fornece um ambiente emulado em execução na estação de trabalho do desenvolvedor local, existem algumas diferenças de funcionalidade entre o emulador e uma conta do Cosmos do Azure na cloud:

* Data Explorer no emulador suporta atualmente clientes para a API de SQL. A vista do Explorador de dados e as operações de APIs do Azure Cosmos DB, como o MongoDB, tabela, gráfico e APIs de Cassandra não são totalmente suportadas.
* O emulador do Cosmos do Azure suporta apenas uma única conta fixa e uma chave mestra bem conhecida. Regeneração da chave não é possível no emulador do Cosmos do Azure, no entanto, a chave predefinida pode ser alterada usando a opção da linha de comandos.
* O emulador do Cosmos do Azure não é um serviço dimensionável e não suportará um grande número de contentores.
* O emulador do Cosmos do Azure não oferece diferentes [níveis de consistência do Azure Cosmos DB](consistency-levels.md).
* O emulador do Cosmos do Azure não oferece [replicação de multirregião](distribute-data-globally.md).
* Como a sua cópia do emulador do Cosmos do Azure pode não estar sempre atualizada com as alterações mais recentes no serviço do Azure Cosmos DB, consulte a [Planeador de capacidade do Azure Cosmos DB](https://www.documentdb.com/capacityplanner) fazer estimativas precisas de produção necessidades de débito (RUs) da sua aplicação.
* Ao utilizar o emulador do Cosmos do Azure, por predefinição, pode criar até 25 contentores de tamanho fixo (apenas suportados através de SDKs do Azure Cosmos DB) ou 5 contentores ilimitados utilizando o emulador do Cosmos do Azure. Para obter mais informações sobre como alterar este valor, veja [Definir o valor de PartitionCount](#set-partitioncount).

## <a name="system-requirements"></a>Requisitos de sistema

O emulador do Cosmos do Azure tem os seguintes requisitos de hardware e software:

* Requisitos de software
  * Windows Server 2012 R2, Windows Server 2016 ou Windows 10
  * sistema operativo de 64 bits
* Requisitos Mínimos de Hardware
  * 2 GB de RAM
  * 10 GB de espaço disponível no disco rígido

## <a name="installation"></a>Instalação

Pode transferir e instalar o emulador do Cosmos do Azure da [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) ou pode executar o emulador no Docker para Windows. Para obter instruções sobre como utilizar o emulador do Docker para Windows, veja [Executar no Docker](#running-on-docker).

> [!NOTE]
> Para instalar, configurar e executar o emulador do Cosmos do Azure, tem de ter privilégios administrativos no computador. O emulador será criar/adicionar um certificado e também definir as regras de firewall para executar seus serviços; por isso é necessário para o emulador ser capaz de executar essas operações.

## <a name="running-on-windows"></a>Executar no Windows

Para iniciar o emulador do Cosmos do Azure, selecione o botão Iniciar ou prima a tecla do Windows. Comece a escrever **emulador do Azure Cosmos**e selecione o emulador na lista de aplicações.

![Selecione o botão Iniciar ou prima a tecla Windows, comece a escrever * * do Azure Cosmos emulador * * e selecione o emulador na lista de aplicações](./media/local-emulator/database-local-emulator-start.png)

Quando o emulador estiver em execução, verá um ícone na área de notificação da barra de tarefas do Windows. ![Notificação do barra de tarefas de emulador local do Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

O emulador do Cosmos do Azure por predefinição, é executado na máquina local ("localhost") à escuta na porta 8081.

O emulador do Cosmos do Azure está instalado para `C:\Program Files\Azure Cosmos DB Emulator` por predefinição. Também pode iniciar e parar o emulador a partir da linha de comandos. Para obter mais informações, veja a [referência da ferramenta de linha de comandos](#command-line).

## <a name="start-data-explorer"></a>Iniciar o Data Explorer

Quando inicia o emulador do Cosmos do Azure, abre automaticamente o Explorador de dados do Cosmos do Azure no seu browser. O endereço é apresentado como `https://localhost:8081/_explorer/index.html`. Se fechar o explorer e gostaria de abri-lo novamente mais tarde, pode abrir o URL no seu browser ou iniciá-lo a partir do emulador do Cosmos do Azure no ícone de Bandeja de Windows, conforme mostrado abaixo.

![Iniciador do Explorador de dados de emulador local do Azure Cosmos](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Verificar atualizações

O Data Explorer indica se está disponível uma nova atualização para transferência.

> [!NOTE]
> Os dados criados em uma versão do emulador do Cosmos do Azure (veja definições opcionais de caminho %LOCALAPPDATA%\CosmosDBEmulator ou dados) não é garantido de estar acessíveis ao utilizar uma versão diferente. Se precisar de manter os seus dados a longo prazo, recomenda-se que esses dados são armazenados numa conta do Cosmos do Azure, e não no emulador do Cosmos do Azure.

## <a name="authenticating-requests"></a>Autenticar pedidos

Como com o Azure Cosmos DB na cloud, todos os pedidos que fizer contra o emulador do Cosmos do Azure tem de ser autenticado. O emulador do Cosmos do Azure suporta uma única conta fixa e uma chave de autenticação bem conhecidos para a autenticação de chave mestra. Esta conta e chave são as credenciais apenas permitidas para utilização com o emulador do Cosmos do Azure. São:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> A chave mestra suportada pelo emulador do Cosmos do Azure destina-se a utilização apenas com o emulador. Não é possível utilizar a conta do Azure Cosmos DB de produção e a chave com o emulador do Cosmos do Azure.

> [!NOTE]
> Se tiver iniciado o emulador com a opção de /Key, em seguida, utilize a chave gerada em vez de `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`. Para obter mais informações sobre a opção de /Key, consulte [referência à ferramenta da linha de comandos.](#command-line-syntax)

Como com o Azure Cosmos DB, o emulador do Cosmos do Azure suporta apenas uma comunicação segura através de SSL.

## <a name="running-on-a-local-network"></a>Executar numa rede local

Pode executar o emulador numa rede local. Para ativar o acesso de rede, especifique a `/AllowNetworkAccess` opção no [da linha de comandos](#command-line-syntax), que também requer que especifique `/Key=key_string` ou `/KeyFile=file_name`. Pode usar `/GenKeyFile=file_name` para gerar um ficheiro com uma chave aleatória de antemão. Em seguida, pode passá-lo para `/KeyFile=file_name` ou `/Key=contents_of_file`.

Para ativar o acesso de rede pela primeira vez, o utilizador deve encerrar o emulador e eliminar o diretório de dados do emulador (% LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Desenvolver com o emulador

### <a name="sql-api"></a>SQL API

Depois de ter o emulador do Cosmos do Azure em execução no seu ambiente de trabalho, pode usar qualquer suportado [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md) ou o [API de REST do Azure Cosmos DB](/rest/api/cosmos-db/) para interagir com o emulador. O emulador do Cosmos do Azure também inclui um Explorador de dados incorporados que lhe permite criar contentores para a API de SQL ou Cosmos DB para API do Mongo DB e ver e editar itens sem escrever nenhum código.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

Se estiver a utilizar [Azure Cosmos DB para o MongoDB](mongodb-introduction.md), utilize a seguinte cadeia de ligação:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>API de Tabela

Depois de ter o emulador do Cosmos do Azure em execução no seu ambiente de trabalho, pode utilizar o [SDK de API de tabela do Azure Cosmos DB](table-storage-how-to-use-dotnet.md) para interagir com o emulador. Inicie o emulador de linha de comandos como administrador com "/ EnableTableEndpoint". Em seguida, execute o seguinte código para ligar à conta de API de tabela:

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

Iniciar o emulador a partir de uma linha de comandos de administrador com "/ EnableCassandraEndpoint". Como alternativa também pode definir a variável de ambiente `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

* [Instalar o Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [Instalar a CLI/CQLSH de Cassandra](http://cassandra.apache.org/download/)

* Execute os seguintes comandos numa janela de linha de comando normal:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* No CQLSH shell, execute os seguintes comandos para ligar ao ponto final de Cassandra:

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

Iniciar o emulador a partir de uma linha de comandos de administrador com "/ EnableGremlinEndpoint". Como alternativa também pode definir a variável de ambiente `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Instalar o apache-tinkerpop-gremlin-consola-3.3.4](http://tinkerpop.apache.org/downloads.html)

* No Explorador de dados do emulador criar uma base de dados "db1" e uma coleção "coll1"; para a chave de partição, escolha "/ nome"

* Execute os seguintes comandos numa janela de linha de comando normal:

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

* Na shell do Gremlin execute os seguintes comandos para ligar ao ponto final do Gremlin:

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

Para obter um certificado a utilizar com linguagens e runtimes que não se integram com o Arquivo de Certificados do Windows, terá de exportá-lo com o Gestor de Certificados do Windows. Pode iniciá-lo ao executar certlm ou siga as instruções passo a passo [exportar os certificados de emulador do Cosmos do Azure](./local-emulator-export-ssl-certificates.md). Assim que o gestor de certificados estiver em execução, abra os Certificados Pessoais, conforme mostrado abaixo, e exporte o certificado com o nome amigável "DocumentDBEmulatorCertificate" como um ficheiro X.509 codificado com BASE-64 (.cer).

![Certificado SSL do emulador local do Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

O certificado X.509 pode ser importado para o arquivo de certificados do Java, seguindo as instruções em [Adicionar um Certificado ao Arquivo de Certificados de AC do Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Depois do certificado é importado para o arquivo de certificados, os clientes para a API de SQL e o Azure Cosmos DB para o MongoDB será possível estabelecer ligação com o emulador do Cosmos do Azure.

Quando ligar ao emulador a partir dos SDKs de Python e Node.js, a verificação de SSL é desativada.

## <a id="command-line"></a>Referência da ferramenta de linha de comandos
Da localização de instalação, pode utilizar a linha de comandos para iniciar e parar o emulador, configure as opções e executar outras operações.

### <a name="command-line-syntax"></a>Sintaxe da linha de comandos

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Para ver a lista de opções, escreva `CosmosDB.Emulator.exe /?` na linha de comandos.

|**Opção** | **Descrição** | **Comando**| **Argumentos**|
|---|---|---|---|
|[Sem argumentos] | Inicia o emulador do Cosmos do Azure com as predefinições. |CosmosDB.Emulator.exe| |
|[Ajuda] |Mostra a lista dos argumentos da linha de comandos suportados.|CosmosDB.Emulator.exe /? | |
| GetStatus |Obtém o estado do emulador do Cosmos do Azure. O estado é indicado pelo código de saída: 1 = a partir de, 2 = em execução, 3 = parado. Um código de saída negativo indica que ocorreu um erro. Não é produzido outro resultado. | CosmosDB.Emulator.exe /GetStatus| |
| Encerrar| Encerra o emulador do Cosmos do Azure.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Especifica o caminho onde pretende armazenar os ficheiros de dados. Valor predefinido é de % LocalAppdata%\CosmosDBEmulator. | CosmosDB.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: Um caminho acessível |
|Porta | Especifica o número de porta a utilizar para o emulador. Valor predefinido é 8081. |CosmosDB.Emulator.exe /Port=\<port\> | \<Porta\>: Número de porta única |
| MongoPort | Especifica o número de porta a utilizar para a API de compatibilidade do MongoDB. Valor predefinido é 10255. |CosmosDB.Emulator.exe /MongoPort= \<mongoport\>|\<mongoport\>: Número de porta única|
| CassandraPort | Especifica o número de porta a utilizar para o ponto final de Cassandra. Valor predefinido é 10350. | CosmosDB.Emulator.exe /CassandraPort = \<cassandraport\> | \<cassandraport\>: Número de porta única |
| ComputePort | Especificar o número de porta a utilizar para o serviço de Gateway de interoperabilidade de computação. Porta de sonda de ponto final HTTP do Gateway é calculada como ComputePort + 79. Por conseguinte, ComputePort e ComputePort + 79 tem de ser aberta e disponível. Os valores predefinidos são 8900, 8979. | CosmosDB.Emulator.exe /ComputePort = \<computeport\> | \<computeport\>: Número de porta única |
| EnableCassandraEndpoint | Permite que a API para Cassandra | CosmosDB.Emulator.exe /EnableCassandraEndpoint | |
| EnableGremlinEndpoint | Permite que a API do Gremlin | CosmosDB.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Número de porta a utilizar para o ponto final de Gremlin. Valor predefinido é 8901. | CosmosDB.Emulator.exe /GremlinPort=\<port\> | \<Porta\>: Número de porta única |
|TablePort | Número de porta a utilizar para o ponto de final de tabela do Azure. Valor predefinido é 8902. | CosmosDB.Emulator.exe /TablePort=\<port\> | \<Porta\>: Número de porta única|
| KeyFile | Chave de autorização de leitura do ficheiro especificado. Utilize a opção de /GenKeyFile para gerar um ficheiro de chave | CosmosDB.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: Caminho do ficheiro |
| ResetDataPath | Recursivamente remove todos os ficheiros no caminho especificado. Se não especificar um caminho, é assumida como predefinição para %LOCALAPPDATA%\CosmosDbEmulator | CosmosDB.Emulator.exe /ResetDataPath[=<path>] | \<path\>: Caminho do ficheiro  |
| StartTraces  |  Iniciar a recolha de registos de rastreio de depuração. | CosmosDB.Emulator.exe /StartTraces | |
| StopTraces     | Pare a recolha de registos de rastreio de depuração. | CosmosDB.Emulator.exe /StopTraces  | |
|EnableTableEndpoint | Permite que a API de tabela do Azure | CosmosDB.Emulator.exe /EnableTableEndpoint | |
|FailOnSslCertificateNameMismatch | Por predefinição, o emulador regenera seu certificado SSL autoassinado, se a SAN do certificado não incluir o anfitrião de emulador nome de domínio local IPv4 endereço, 'localhost' e '127.0.0.1'. Com esta opção, o emulador irá falhar no arranque em vez disso. Em seguida, deve utilizar a opção de /GenCert para criar e instalar um novo certificado SSL autoassinado. | CosmosDB.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Gerar e instalar um novo certificado SSL autoassinado. Opcionalmente, incluindo uma lista separada por vírgulas de nomes DNS adicionais para acessar o emulador através da rede. | CosmosDB.Emulator.exe /GenCert [ \<lista separada por vírgulas de nomes de dns adicionais\>] | |
| DirectPorts |Especifica as portas a utilizar para conectividade direta. As predefinições são 10251,10252,10253,10254. | CosmosDB.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: Lista delimitada por vírgulas de 4 portas |
| Chave |Chave de autorização para o emulador. A chave tem de ser a codificação base 64 de um vetor de 64 bytes. | CosmosDB.Emulator.exe /Key:\<key\> | \<key\>: Chave tem de ser a codificação base 64 de um vetor de 64 bytes|
| EnableRateLimiting | Especifica que o comportamento de limitação da taxa de pedidos está ativado. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Especifica que o comportamento de limitação da taxa de pedidos está desativado. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | Não mostrar a interface de utilizador do emulador. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | Não mostrar o explorador de dados no arranque. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | Especifica o número máximo de contentores particionadas. Ver [alterar o número de contentores](#set-partitioncount) para obter mais informações. | CosmosDB.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>: Número máximo de contentores de partição única permitidos. O valor predefinido é 25. O máximo permitido é 250.|
| DefaultPartitionCount| Especifica o número predefinido de partições para um contentor particionado. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> valor predefinido é 25.|
| AllowNetworkAccess | Permite o acesso ao emulador através de uma rede. Tem de passar também /Key=\<key_string\> oru/KeyFile=\<file_name\> para ativar o acesso à rede. | CosmosDB.Emulator.exe AllowNetworkAccess /Key =\<key_string\> ou CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile =\<nome_ficheiro\>| |
| NoFirewall | Não ajuste a regras de firewall quando é utilizada a opção de /AllowNetworkAccess. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Gerar uma nova chave de autorização e guardá-la no ficheiro especificado. A chave gerada pode ser utilizada com as opções /Key ou /KeyFile. | CosmosDB.Emulator.exe /GenKeyFile =\<caminho para o ficheiro de chave\> | |
| Consistência | Defina o nível de consistência predefinida para a conta. | CosmosDB.Emulator.exe /Consistency=\<consistency\> | \<Consistência\>: Valor tem de ser um dos seguintes [níveis de consistência](consistency-levels.md): Sessão, forte, Eventual, ou BoundedStaleness. O valor predefinido é Session. |
| ? | Mostrar a mensagem de ajuda.| | |

## <a id="set-partitioncount"></a>Alterar o número de contentores

Por predefinição, pode criar até 25 contentores de tamanho fixo (apenas suportados através de SDKs do Azure Cosmos DB) ou 5 contentores ilimitados utilizando o emulador do Cosmos do Azure. Modificando o **PartitionCount** valor, pode criar até 250 contentores de tamanho fixo ou contentores ilimitados 50 ou qualquer combinação das duas que não excedam 250 contentores de tamanho fixo (em que um contentor ilimitado = 5 de tamanho fixo contentores). No entanto não é recomendado para configurar o emulador para executar com mais de 200 contentores de tamanho fixo. Devido à sobrecarga que ele adiciona para as operações de e/s de disco, que resulta em tempos limite imprevisível ao utilizar o ponto final de APIs.


Se está tentando criar um contentor depois foi excedido o número atual de partições, emulador lança uma exceção de ServiceUnavailable, com a seguinte mensagem.

"Lamentamos, mas estamos atualmente a ter elevada procura nesta região e não é possível satisfazer o seu pedido neste momento. Trabalhamos continuamente para colocar online mais e mais capacidade e recomendo que tente novamente.
Não hesite em e-mail askcosmosdb@microsoft.com em qualquer altura ou por qualquer motivo. ActivityId: 12345678-1234-1234-1234-123456789ABC"

Para alterar o número de contentores disponíveis no emulador do Cosmos do Azure, execute os seguintes passos:

1. Eliminar todos os dados locais do emulador do Azure Cosmos clicando com o **emulador do Azure Cosmos DB** ícone na Bandeja do sistema e, em seguida, clicar **de reposição de dados...** .
2. Eliminar todos os dados de emulador nesta pasta `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Saia de todas as instâncias abertas, ao clicar com o botão direito do rato no ícone do**Emulador do Azure Cosmos DB** no tabuleiro do sistema e, em seguida, clique em **Sair**. Pode demorar um minuto para que todas as instâncias possam sair.
4. Instale a versão mais recente dos [emulador do Azure Cosmos](https://aka.ms/cosmosdb-emulator).
5. Inicie o emulador com o sinalizador de PartitionCount, ao definir um valor <= 250. Por exemplo: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Controlar o emulador

O emulador é fornecido com um módulo do PowerShell para iniciar, parar, desinstale e obter o estado do serviço. Execute o cmdlet seguinte para utilizar o módulo do PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

ou coloque a `PSModules` diretório no seu `PSModulesPath` e importá-la, conforme mostrado no comando seguinte:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Eis um resumo dos comandos para controlar o emulador a partir do PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Sintaxe**

`Get-CosmosDbEmulatorStatus`

**Observações**

Devolve um dos seguintes valores de ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running ou ServiceControllerStatus.Stopped.

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

O emulador do Cosmos do Azure podem ser executado no Docker para Windows. O emulador não funciona no Docker para Oracle Linux.

Assim que tiver instalado o [Docker para Windows](https://www.docker.com/docker-windows), mude para os contentores do Windows, ao clicar com o botão direito do rato no ícone do Docker na barra de ferramentas e selecionar **Mudar para os contentores do Windows**.

Em seguida, extraia a imagem do emulador do Hub do Docker, executando o seguinte comando a partir da sua shell favorita.

```bash
docker pull microsoft/azure-cosmosdb-emulator
```
Para iniciar a imagem, execute os seguintes comandos.

Na linha de comandos:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 microsoft/azure-cosmosdb-emulator
```

Do PowerShell:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 microsoft/azure-cosmosdb-emulator

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


## <a name="troubleshooting"></a>Resolução de problemas

Utilize as sugestões seguintes para ajudar a resolver problemas que encontrar com o emulador do Cosmos do Azure:

- Se instalou uma nova versão do emulador e surgirem erros, certifique-se de que repõe os dados. Pode repor os dados ao clicar com o botão direito no ícone do emulador do Cosmos do Azure na Bandeja do sistema e, em seguida, clicando em Redefinir dados... Se não corrigir os erros, pode desinstalar o emulador e quaisquer versões mais antigas do emulador de se encontrar, remover o diretório de "C:\Program c:\programas\azure emulador do Cosmos DB" e reinstalar o emulador. Veja [Desinstalar o emulador local](#uninstall) para obter instruções.

- No caso de falha de emulador do Cosmos do Azure, recolher ficheiros de informação da pasta "% LOCALAPPDATA%\CrashDumps", compactá-los e anexe-os a um e-mail para [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Se ocorrerem falhas no `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, isso pode ser um sintoma onde os contadores de desempenho estão num estado danificado. Normalmente, executando o seguinte comando a partir de uma linha de comando de administrador corrige o problema:

  ```cmd
  lodctr /R
   ```

- Se ocorrer um problema de conectividade, [recolha os ficheiros de rastreio](#trace-files), comprima-os e anexe-os a uma mensagem de e-mail para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Se receber uma mensagem de **serviço indisponível**, o emulador poderá estar a falhar ao inicializar a pilha de rede. Verifique se tem as redes de cliente seguro Pulse ou Juniper instaladas, dado que os respetivos controladores de filtro de rede poderão causar o problema. Desinstalar os controladores de filtro de rede de terceiros normalmente corrige o problema. Em alternativa, inicie o emulador com /DisableRIO, que muda a comunicação de rede do emulador para regular Winsock. 

- Enquanto o emulador estiver em execução, se o computador entrar no modo de suspensão ou executar quaisquer atualizações do SO, poderá ver a mensagem **O serviço está indisponível neste momento**. Reposição de dados do emulador, clicando no ícone de que é apresentado no tabuleiro de notificação do windows e selecione **redefinir dados**.

### <a id="trace-files"></a>Recolher ficheiros de rastreio

Para recolher rastreios de depuração, execute os seguintes comandos a partir de uma linha de comandos administrativa:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Observe o tabuleiro do sistema para certificar-se de que o programa foi encerrado; poderá demorar um minuto. Pode também simplesmente clicar **saída** na interface do usuário de emulador do Cosmos do Azure.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reproduza o problema. Se o Data Explorer não estiver a funcionar, apenas terá de aguardar que o browser abra por alguns segundos para apanhar o erro.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Navegue para `%ProgramFiles%\Azure Cosmos DB Emulator` e localize o ficheiro docdbemulator_000001.etl.
7. Envie o ficheiro.etl juntamente com os passos de reprodução para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com), para depuração.

### <a id="uninstall"></a>Desinstalar o emulador local

1. Saia do open todas as instâncias do emulador local clicando com o botão direito no ícone do emulador do Cosmos do Azure na Bandeja do sistema e, em seguida, clicando em sair. Pode demorar um minuto para que todas as instâncias possam sair.
2. Na caixa de pesquisa do Windows, escreva **Aplicações e funcionalidades** e clique no resultado **Aplicações e funcionalidades (Definições do sistema)**.
3. Na lista de aplicações, desloque para **Emulador do Azure Cosmos DB**, selecione-o, clique em **Desinstalar** e, em seguida, confirme e clique em **Desinstalar** novamente.
4. Quando a aplicação estiver desinstalada, navegue para `%LOCALAPPDATA%\CosmosDBEmulator` e elimine a pasta.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar o emulador local para desenvolvimento local gratuito. Agora pode avançar para o tutorial seguinte e ficar a saber como exportar certificados SSL do emulador.

> [!div class="nextstepaction"]
> [Exportar os certificados de emulador do Cosmos do Azure](local-emulator-export-ssl-certificates.md)
