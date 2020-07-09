---
title: Desenvolva-se localmente com o Emulador Azure Cosmos
description: Utilizando o Emulador Azure Cosmos, pode desenvolver e testar a sua aplicação localmente gratuitamente, sem criar uma subscrição do Azure.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 01/31/2020
ms.openlocfilehash: e06a2eac5387cd02e95d8252ae04edc356683ed9
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86028238"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Use o Emulador Azure Cosmos para desenvolvimento local e testes

O Azure Cosmos Emulator fornece um ambiente local que imita o serviço DB Azure Cosmos para fins de desenvolvimento. Utilizando o Emulador Azure Cosmos, pode desenvolver e testar a sua aplicação localmente, sem criar uma subscrição Azure ou incorrer em quaisquer custos. Quando estiver satisfeito com o funcionamento da sua aplicação no Emulator Azure Cosmos, pode mudar para usar uma conta Azure Cosmos na nuvem.

Você pode desenvolver usando Azure Cosmos Emulator com contas [SQL,](local-emulator.md#sql-api) [Cassandra,](local-emulator.md#cassandra-api) [MongoDB,](local-emulator.md#azure-cosmos-dbs-api-for-mongodb) [Gremlin](local-emulator.md#gremlin-api)e [Table](local-emulator.md#table-api) API. No entanto, neste momento, a vista do Data Explorer no emulador suporta totalmente os clientes apenas para a API SQL. 

## <a name="how-the-emulator-works"></a>Como funciona o emulador

O Azure Cosmos Emulator proporciona uma emulação de alta fidelidade do serviço DB Azure Cosmos. Suporta funcionalidades idênticas à Azure Cosmos DB, incluindo suporte para a criação e consulta de dados, fornecimento e dimensionamento de contentores, e execução de procedimentos e gatilhos armazenados. Pode desenvolver e testar aplicações utilizando o Emulador Azure Cosmos e implantá-las para Azure à escala global, fazendo apenas uma alteração de configuração para o ponto final de ligação para Azure Cosmos DB.

Apesar de a emulação do serviço do Azure Cosmos DB ser fiel, a implementação do emulador é diferente do serviço. Por exemplo, o emulador utiliza componentes standard do SO, como o sistema de ficheiros local para persistência e a pilha de protocolo HTTPS para conectividade. A funcionalidade que depende da infraestrutura Azure, como a replicação global, a latência milissegundo de um dígito para leituras/escritas, e os níveis de consistência incapazes não são aplicáveis.

Pode migrar dados entre o Emulator Azure Cosmos e o serviço DB Azure Cosmos utilizando a [Ferramenta de Migração de Dados Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

Você pode executar Azure Cosmos Emulator no recipiente Windows Docker, consulte o [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) para o comando de puxar o estivador e [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) para obter `Dockerfile` e mais informações.

## <a name="differences-between-the-emulator-and-the-service"></a>Diferenças entre o emulador e o serviço

Como o Emulador Azure Cosmos proporciona um ambiente emulsionado em execução na estação de trabalho do desenvolvedor local, existem algumas diferenças na funcionalidade entre o emulador e uma conta Azure Cosmos na nuvem:

* Atualmente o Data Explorer no emulador suporta clientes para a API SQL. A visão e operações do Data Explorer para APIs DB Azure Cosmos, tais como MongoDB, Tabela, Gráfico e ApIs cassandra não estão totalmente suportados.
* O Emulador Azure Cosmos suporta apenas uma única conta fixa e uma chave-mestre bem conhecida. A regeneração da chave não é possível no Emulador Azure Cosmos, no entanto a chave padrão pode ser alterada usando a opção de linha de comando.
* O Emulador Azure Cosmos não é um serviço escalável e não suporta um grande número de contentores.
* O Emulador Azure Cosmos não oferece [diferentes níveis de consistência Azure Cosmos DB](consistency-levels.md).
* O Emulador Azure Cosmos não oferece [replicação multi-região.](distribute-data-globally.md)
* Como a sua cópia do Azure Cosmos Emulator pode nem sempre estar atualizada com as mudanças mais recentes no serviço DB Azure Cosmos, deve consultar o planejador de [capacidades DB da Azure Cosmos](https://www.documentdb.com/capacityplanner) para estimar com precisão as necessidades de produção (RUs) da sua aplicação.
* Ao utilizar o Emulador Azure Cosmos, por padrão, pode criar até 25 recipientes de tamanho fixo (apenas suportados com recurso a Azure Cosmos DB SDKs), ou 5 recipientes ilimitados utilizando o Emulator Azure Cosmos. Para obter mais informações sobre como alterar este valor, veja [Definir o valor de PartitionCount](#set-partitioncount).
* O emulador suporta o tamanho máximo de propriedade de 254 caracteres.

## <a name="system-requirements"></a>Requisitos de sistema

O Azure Cosmos Emulator tem os seguintes requisitos de hardware e software:

* Requisitos de software
  * Windows Server 2012 R2, Windows Server 2016 ou Windows 10
  * Sistema operativo de 64 bits
* Requisitos Mínimos de Hardware
  * 2 GB de RAM
  * 10 GB de espaço disponível no disco rígido

## <a name="installation"></a>Instalação

Pode descarregar e instalar o Emulador Azure Cosmos a partir do [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) ou pode executar o emulador no Docker para windows. Para obter instruções sobre como utilizar o emulador do Docker para Windows, veja [Executar no Docker](#running-on-docker).

> [!NOTE]
> Para instalar, configurar e executar o Emulador Azure Cosmos, você deve ter privilégios administrativos no computador. O emulador criará/adicionará um certificado e também definirá as regras de firewall para executar os seus serviços; portanto, é necessário que o emulador seja capaz de executar tais operações.

## <a name="running-on-windows"></a>Executar no Windows

Para iniciar o Emulador Azure Cosmos, selecione o botão Iniciar ou prima a tecla Windows. Comece a escrever **Azure Cosmos Emulator**e selecione o emulador da lista de aplicações.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="Selecione o botão Iniciar ou prima a tecla Windows, comece a digitar o Emulador Azure Cosmos e selecione o emulador da lista de aplicações":::

Quando o emulador estiver em execução, verá um ícone na área de notificação da barra de tarefas do Windows. 

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Azure Cosmos DB notificação de barra de tarefa local":::

O Emulador Azure Cosmos por defeito funciona na máquina local ("localhost") ouvindo na porta 8081.

O Emulador Azure Cosmos é instalado `C:\Program Files\Azure Cosmos DB Emulator` por defeito. Também pode iniciar e parar o emulador a partir da linha de comandos. Para obter mais informações, veja a [referência da ferramenta de linha de comandos](#command-line).

## <a name="start-data-explorer"></a>Iniciar o Data Explorer

Quando o Emulador Azure Cosmos é lançado, abre automaticamente o Azure Cosmos Data Explorer no seu navegador. O endereço é apresentado como `https://localhost:8081/_explorer/index.html`. Se fechar o explorador e quiser reabri-lo mais tarde, pode abrir o URL no seu navegador ou lançá-lo a partir do Emulador Azure Cosmos no Ícone da Bandeja do Windows, como mostrado abaixo.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Lançador local de explorador de dados Azure Cosmos":::

## <a name="checking-for-updates"></a>Verificar atualizações

O Data Explorer indica se está disponível uma nova atualização para transferência.

> [!NOTE]
> Os dados criados numa versão do Emulador Azure Cosmos (ver %LOCALAPPDATA%\CosmosDBEmulator ou definições opcionais de trajetória de dados) não são garantidos para serem acessíveis quando se utiliza uma versão diferente. Se precisar de persistir os seus dados a longo prazo, recomenda-se que guarde esses dados numa conta Azure Cosmos, em vez de no Emulator Azure Cosmos.

## <a name="authenticating-requests"></a>Autenticar pedidos

Tal como acontece com a Azure Cosmos DB na nuvem, todos os pedidos que fizer contra o Emulador Azure Cosmos devem ser autenticados. O Azure Cosmos Emulator suporta uma única conta fixa e uma chave de autenticação bem conhecida para a autenticação de chaves master. Esta conta e chave são as únicas credenciais permitidas para uso com o Emulator Azure Cosmos. São:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> A chave mestra suportada pelo Emulador Azure Cosmos destina-se a ser utilizada apenas com o emulador. Não pode utilizar a sua conta Esfréstrutura Azure Cosmos e a chave com o Emulador Azure Cosmos.

> [!NOTE]
> Se tiver iniciado o emulador com a opção /Chave, utilize a chave gerada em vez de `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` . Para obter mais informações sobre a opção /Chave, consulte [a referência da ferramenta da linha de comando.](#command-line)

Tal como acontece com o Azure Cosmos DB, o Emulador Azure Cosmos suporta apenas uma comunicação segura via TLS.

## <a name="running-on-a-local-network"></a>Executar numa rede local

Pode executar o emulador numa rede local. Para permitir o acesso à rede, especifique a `/AllowNetworkAccess` opção na [linha de comando,](#command-line-syntax)que também requer que especifique `/Key=key_string` ou `/KeyFile=file_name` . Pode utilizar `/GenKeyFile=file_name` para gerar um ficheiro com uma chave aleatória. Então podes passar isso para `/KeyFile=file_name` `/Key=contents_of_file` ou.

Para permitir o acesso à rede pela primeira vez, o utilizador deve desligar o emulador e eliminar o diretório de dados do emulador (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Desenvolver com o emulador

### <a name="sql-api"></a>SQL API

Assim que tiver o Emulador Azure Cosmos a funcionar no seu ambiente de trabalho, pode utilizar qualquer [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) suportado ou a [API API AZure Cosmos DB REST](/rest/api/cosmos-db/) para interagir com o emulador. O Emulador Azure Cosmos também inclui um Data Explorer incorporado que permite criar recipientes para API SQL ou Cosmos DB para Mongo DB API, e ver e editar itens sem escrever nenhum código.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

Assim que tiver o Emulador Azure Cosmos a funcionar no seu ambiente de trabalho, pode utilizar a [API da Azure Cosmos para a MongoDB](mongodb-introduction.md) interagir com o emulador. Inicie o emulador a partir do comando como administrador com "/EnableMongoDbEndpoint". Em seguida, utilize o seguinte fio de ligação para ligar à conta API mongodb:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>API de Tabela

Assim que tiver o Emulador Azure Cosmos a funcionar no seu ambiente de trabalho, pode utilizar a [Azure Cosmos DB Table API SDK](table-storage-how-to-use-dotnet.md) para interagir com o emulador. Inicie o emulador a partir do comando como administrador com "/EnableTableEndpoint". Em seguida, executar o seguinte código para ligar à conta API tabela:

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

Inicie o emulador a partir de um pedido de comando do administrador com "/EnableCassandraEndpoint". Em alternativa, também pode definir a variável `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true` ambiente.

* [Instalar Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [Instalar Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

* Executar os seguintes comandos numa janela de pedido de comando regular:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* Na concha CQLSH, executar os seguintes comandos para ligar ao ponto final de Cassandra:

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

Inicie o emulador a partir de um pedido de comando do administrador com "/EnableGremlinEndpoint". Em alternativa, também pode definir a variável ambiental`AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Instale apache-tinkerpop-gremlin-consola-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

* No Data Explorer do emulador cria-se uma base de dados "db1" e uma coleção "coll1"; para a chave de partição, escolha "/nome"

* Executar os seguintes comandos numa janela de pedido de comando regular:

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

* Na concha de Gremlin, executar os seguintes comandos para ligar ao ponto final de Gremlin:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-tlsssl-certificate"></a>Exportar o certificado TLS/SSL

As linguagens .NET e o runtime utilizam o Arquivo de Certificados do Windows para ligar de forma segura ao emulador local do Azure Cosmos DB. Outras linguagens têm o seu próprio método de gerir e utilizar certificados. O Java utiliza o seu próprio [arquivo de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), enquanto o Python utiliza [wrappers de sockets](https://docs.python.org/2/library/ssl.html).

Para obter um certificado a utilizar com linguagens e runtimes que não se integram com o Arquivo de Certificados do Windows, terá de exportá-lo com o Gestor de Certificados do Windows. Pode iniciá-lo executando certlm.msc ou seguir as instruções passo a passo na [Exportação dos Certificados emuladores Azure Cosmos](./local-emulator-export-ssl-certificates.md). Assim que o gestor de certificados estiver em execução, abra os Certificados Pessoais, conforme mostrado abaixo, e exporte o certificado com o nome amigável "DocumentDBEmulatorCertificate" como um ficheiro X.509 codificado com BASE-64 (.cer).

:::image type="content" source="./media/local-emulator/database-local-emulator-ssl_certificate.png" alt-text="Certificado de emulador local Azure Cosmos DB TLS/SSL":::

O certificado X.509 pode ser importado para o arquivo de certificados do Java, seguindo as instruções em [Adicionar um Certificado ao Arquivo de Certificados de AC do Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Uma vez que o certificado é importado para a loja de certificados, os clientes da API da SQL e da Azure Cosmos DB para a MongoDB poderão ligar-se ao Emulator Azure Cosmos.

Ao ligar-se ao emulador de Node.js SDKs, a verificação TLS é desativada.

## <a name="command-line-tool-reference"></a><a id="command-line"></a>Referência da ferramenta de linha de comandos
A partir do local de instalação, pode utilizar a linha de comando para iniciar e parar o emulador, configurar opções e realizar outras operações.

### <a name="command-line-syntax"></a>Sintaxe da linha de comandos

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

Para ver a lista de opções, escreva `Microsoft.Azure.Cosmos.Emulator.exe /?` na linha de comandos.

|**Opção** | **Descrição** | **Comando**| **Argumentos**|
|---|---|---|---|
|[Sem argumentos] | Inicia o Emulador Azure Cosmos com definições predefinidas. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Ajuda] |Mostra a lista dos argumentos da linha de comandos suportados.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Obtém o estatuto do Emulador Azure Cosmos. O estado é indicado pelo código de saída: 1 = A iniciar, 2 = Em execução, 3 = Parado. Um código de saída negativo indica que ocorreu um erro. Não é produzido outro resultado. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Encerrar| Desliga o Emulador Azure Cosmos.| Microsoft.Azure.Cosmos.Emulator.exe /Encerramento | |
|DataPath | Especifica o caminho onde pretende armazenar os ficheiros de dados. O valor predefinido é %LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: Um caminho acessível |
|Porta | Especifica o número de porta a utilizar para o emulador. O valor predefinido é 8081. |Microsoft.Azure.Cosmos.Emulator.exe /Porto=\<port\> | \<port\>: Número único da porta |
| Porto Compute | Especificou o número da porta a utilizar para o serviço Compute Interop Gateway. A porta de sonda HTTP do Gateway é calculada como ComputePort + 79. Assim, o ComputePort e o ComputePort + 79 devem estar abertos e disponíveis. O valor predefinido é 8900. | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<computeport\>: Número único da porta |
| EnableMongoDbEndpoint=3.2 | Permite a MongoDB API 3.2 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | Permite a MongoDB API 3.6 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | Especifica o número de porta a utilizar para a API de compatibilidade do MongoDB. O valor predefinido é de 10255. |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\>: Número único da porta|
| EnableCassandraEndpoint | Permite a API de Cassandra | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Especifica o número da porta a utilizar para o ponto final de Cassandra. O valor predefinido é de 10350. | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>: Número único da porta |
| EnableGremlinEndpoint | Permite a API de Gremlin | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Número de porta para utilizar para o Ponto Final de Gremlin. O valor predefinido é 8901. | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\>: Número único da porta |
|EnableTableEndpoint | Permite a API tabela AZure | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | Número de porta a utilizar para o Ponto final da tabela Azure. O valor predefinido é 8902. | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\>: Número único da porta|
| Ficheiro chave | Leia a chave de autorização a partir do ficheiro especificado. Utilize a opção /GenKeyFile para gerar um ficheiro de teclas | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: Caminho para o arquivo |
| ResetDataPath | Remove novamente todos os ficheiros no caminho especificado. Se não especificar um caminho, ele falha em %LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<path> | \<path\>: Caminho de arquivo  |
| StartTraces  |  Comece a recolher registos de vestígios de depurador utilizando LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | Pare de recolher registos de vestígios de depurador usando LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Comece a recolher registos de vestígios de depuragem utilizando a ferramenta De gravação de desempenho do Windows. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | Pare de recolher registos de vestígios de depurar utilizando a ferramenta De gravação de desempenho do Windows. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Por predefinição, o Emulator regenera o seu certificado TLS/SSL auto-assinado, se o certificado san não incluir o nome de domínio do anfitrião Emulador, o endereço IPv4 local, "localhost" e "127.0.0.1". Com esta opção, o emulador falhará no arranque. Em seguida, deve utilizar a opção /GenCert para criar e instalar um novo certificado TLS/SSL auto-assinado. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Gere e instale um novo certificado TLS/SSL auto-assinado. opcionalmente incluindo uma lista separada por vírgula de nomes DNS adicionais para aceder ao Emulator sobre a rede. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<dns-names\>: Lista opcional separada de vírgulas de nomes dns adicionais  |
| DirectPorts |Especifica as portas a utilizar para conectividade direta. As predefinições são 10251,10252,10253,10254. | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: Lista delimitada por vírgula de 4 portos |
| Chave |Chave de autorização para o emulador. A chave tem de ser a codificação base 64 de um vetor de 64 bytes. | Microsoft.Azure.Cosmos.Emulator.exe /Chave:\<key\> | \<key\>: A chave deve ser a codificação base-64 de um vetor de 64 bytes|
| EnableRateLimiting | Especifica que o comportamento de limitação da taxa de pedidos está ativado. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Especifica que o comportamento de limitação da taxa de pedidos está desativado. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | Não mostrar a interface de utilizador do emulador. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Não mostrar o explorador de dados no arranque. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Especifica o número máximo de recipientes divididos. Consulte [alterar o número de recipientes](#set-partitioncount) para obter mais informações. | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>: Número máximo de recipientes de partição simples permitidos. O valor predefinido é 25. O máximo permitido é 250.|
| DefaultPartitionCount| Especifica o número predefinido de divisórias para um recipiente dividido. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\>O valor predefinido é de 25.|
| AllowNetworkAccess | Permite o acesso ao emulador através de uma rede. Também tem de passar /Key= \<key_string\> ou /KeyFile= \<file_name\> para permitir o acesso à rede. | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key= \<key_string\> ou Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Não ajuste as regras de firewall quando /Permitir a opção Desajuste de Internet. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Gerar uma nova chave de autorização e guardá-la no ficheiro especificado. A chave gerada pode ser utilizada com as opções /Key ou /KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<path to key file\> | |
| Consistência | Defina o nível de consistência predefinida para a conta. | Microsoft.Azure.Cosmos.Emulator.exe /Consistência=\<consistency\> | \<consistency\>: O valor deve ser um dos [seguintes níveis de consistência](consistency-levels.md): Sessão, Forte, Eventual ou LimitadaStaleness. O valor predefinido é Session. |
| ? | Mostrar a mensagem de ajuda.| | |

## <a name="change-the-number-of-containers"></a><a id="set-partitioncount"></a>Alterar o número de contentores

Por padrão, pode criar até 25 recipientes de tamanho fixo (apenas suportados com Azure Cosmos DB SDKs), ou 5 contentores ilimitados utilizando o Emulator Azure Cosmos. Ao modificar o valor **PartitionCount,** pode criar até 250 recipientes de tamanho fixo ou 50 contentores ilimitados, ou qualquer combinação dos dois que não exceda 250 recipientes de tamanho fixo (onde um recipiente ilimitado = 5 recipientes de tamanho fixo). No entanto, não é aconselhável configurar o emulador para funcionar com mais de 200 recipientes de tamanho fixo. Devido à sobrecarga que adiciona às operações de IO do disco, que resultam em intervalos de tempo imprevisíveis ao utilizar as APIs do ponto final.

Se tentar criar um recipiente após a exceção da contagem atual de divisórias, o emulador lança uma exceção ServiceUn disponível, com a seguinte mensagem.

"Desculpe, estamos a ter uma elevada procura nesta região, e não podemos cumprir o seu pedido neste momento. Trabalhamos continuamente para trazer cada vez mais capacidade on-line, e encorajá-lo a tentar novamente.
AtividadeSId: 12345678-1234-1234-1234-123456789abc"

Para alterar o número de contentores disponíveis no Emulador Azure Cosmos, executar os seguintes passos:

1. Elimine todos os dados do Emulador Azure Cosmos locais clicando à direita no ícone **emulador Azure Cosmos DB** na bandeja do sistema e, em seguida, clicando em **Dados de Reset...**.
2. Elimine todos os dados do emulador nesta pasta `%LOCALAPPDATA%\CosmosDBEmulator` .
3. Saia de todas as instâncias abertas, ao clicar com o botão direito do rato no ícone do**Emulador do Azure Cosmos DB** no tabuleiro do sistema e, em seguida, clique em **Sair**. Pode demorar um minuto para que todas as instâncias possam sair.
4. Instale a versão mais recente do [Emulador Azure Cosmos](https://aka.ms/cosmosdb-emulator).
5. Inicie o emulador com o sinalizador de PartitionCount, ao definir um valor <= 250. Por exemplo: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Controlar o emulador

O emulador vem com um módulo PowerShell para iniciar, parar, desinstalar e recuperar o estado do serviço. Executar o seguinte cmdlet para utilizar o módulo PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

ou colocar o `PSModules` diretório no seu `PSModulesPath` e importá-lo como mostrado no seguinte comando:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Eis um resumo dos comandos para controlar o emulador a partir do PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Syntax**

`Get-CosmosDbEmulatorStatus`

**Observações**

Devolve um dos seguintes valores de ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running ou ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntax**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Observações**

Inicia o emulador. Por predefinição, o comando aguarda até que o emulador esteja pronto para aceitar pedidos. Utilize a opção -NoWait, se quiser que o cmdlet seja devolvido assim que inicia o emulador.

### `Stop-CosmosDbEmulator`

**Syntax**

 `Stop-CosmosDbEmulator [-NoWait]`

**Observações**

Para o emulador. Por predefinição, este comando aguarda até que o emulador seja totalmente encerrado. Utilize a opção -NoWait, se quiser que o cmdlet seja devolvido assim que o emulador começar a encerrar.

### `Uninstall-CosmosDbEmulator`

**Syntax**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Observações**

Desinstala o emulador e, opcionalmente, remove os conteúdos de $env:LOCALAPPDATA\CosmosDbEmulator.
O cmdlet assegura que o emulador está parado antes de desinstalá-lo.

## <a name="running-on-docker"></a>Em execução no Docker

O Emulador Azure Cosmos pode ser executado em Docker para windows. O emulador não funciona no Docker para Oracle Linux.

Assim que tiver instalado o [Docker para Windows](https://www.docker.com/docker-windows), mude para os contentores do Windows, ao clicar com o botão direito do rato no ícone do Docker na barra de ferramentas e selecionar **Mudar para os contentores do Windows**.

Em seguida, extraia a imagem do emulador do Hub do Docker, executando o seguinte comando a partir da sua shell favorita.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Para iniciar a imagem, execute os seguintes comandos.

Na linha de comandos:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```

> [!NOTE]
> Se vir um erro de conflito na porta (a porta especificada já está a ser utilizada) quando executar o comando de execução do estivador, pode passar por uma porta personalizada alterando os números da porta. Por exemplo, pode alterar o "-p 8081:8081" para "-p 443:8081"

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

Agora use o ponto final e master key-in da resposta no seu cliente e importe o certificado TLS/SSL para o seu anfitrião. Para importar o certificado TLS/SSL, faça o seguinte a partir de um pedido de comando administrativo:

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

Fechar a concha interativa uma vez iniciado o emulador desligará o recipiente do emulador.

Para abrir o Data Explorer, navegue para o seguinte URL no browser. O ponto final do emulador é fornecido na mensagem de resposta mostrada acima.

**https \: // ** \<emulator endpoint provided in response> **/_explorer/index.html**

Se tiver uma aplicação de cliente .NET a funcionar num recipiente de estival Linux e se estiver a executar o emulador Azure Cosmos numa máquina hospedeira, siga a secção abaixo para linux importar o certificado para o recipiente linux docker.

## <a name="running-on-mac-or-linux"></a>Correndo em Mac ou Linux<a id="mac"></a>

Atualmente, o emulador Cosmos só pode ser executado no Windows. Os utilizadores que executam Mac ou Linux podem executar o emulador numa máquina virtual windows hospedado um hipervisor, como Paralelos ou VirtualBox. Abaixo estão os passos para permitir isto.

Dentro do Windows VM execute o comando abaixo e tome nota do endereço IPv4.

```cmd
ipconfig.exe
```

Dentro da sua aplicação, tem de alterar o URI utilizado como Ponto Final para utilizar o endereço IPv4 devolvido em `ipconfig.exe` vez de `localhost` .

O passo seguinte, a partir do interior do Windows VM, lança o emulador Cosmos a partir da linha de comando utilizando as seguintes opções.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

Por último, temos de importar o certificado emulador CA para o ambiente Linux ou Mac.

### <a name="linux"></a>Linux

Se estiver a trabalhar no Linux, relés .NET no OpenSSL para fazer a validação:

1. [Exporte o certificado em formato PFX](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (o PFX está disponível na escolha de exportar a chave privada). 

1. Copie o ficheiro PFX para o seu ambiente Linux.

1. Converter o ficheiro PFX num ficheiro CRT

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Copie o ficheiro CRT para a pasta que contém certificados personalizados na sua distribuição Linux. Comumente nas distribuições de Debian, está localizado em `/usr/local/share/ca-certificates/` .

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Atualize os certificados de CA, que atualizarão a `/etc/ssl/certs/` pasta.

   ```bash
   update-ca-certificates
   ```

### <a name="macos"></a>macOS

Utilize os seguintes passos se estiver a trabalhar no Mac:

1. [Exporte o certificado em formato PFX](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (o PFX está disponível na escolha de exportar a chave privada).

1. Copie o ficheiro PFX para o seu ambiente Mac.

1. Abra a aplicação *Keychain Access* e importe o ficheiro PFX.

1. Abra a lista de Certificados e identifique o com o `localhost` nome.

1. Abra o menu de contexto para esse item em particular, *selecione Get Item* e em *Trust*Ao utilizar  >  esta opção de*certificado,* selecione *Always Trust*. 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="Abra o menu de contexto para esse item em particular, selecione Get Item e em Trust - Quando utilizar esta opção de certificado, selecione Always Trust":::

Após seguir estes passos, o seu ambiente confiará no certificado utilizado pelo Emulador ao ligar-se ao endereço IP exposto por `/AllowNetworkAccess` .

## <a name="troubleshooting"></a>Resolução de problemas

Use as seguintes dicas para ajudar a resolver problemas que encontra com o Emulator Azure Cosmos:

- Se instalou uma nova versão do emulador e surgirem erros, certifique-se de que repõe os dados. Pode redefinir os seus dados clicando à direita no ícone emulador Azure Cosmos na bandeja do sistema e, em seguida, clicando em Dados de Reset.... Se isso não corrigir os erros, pode desinstalar o emulador e quaisquer versões mais antigas do emulador se for encontrado, remover o diretório "C:\Program files\Azure Cosmos DB Emulator" e reinstalar o emulador. Veja [Desinstalar o emulador local](#uninstall) para obter instruções.

- Se o Emulador Azure Cosmos se despenhar, recolher ficheiros de despejo da pasta '%LOCALAPPDATA%\CrashDumps', comprimi-los e abrir um bilhete de apoio a partir do [portal Azure](https://portal.azure.com).

- Se tiver `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` acidentes, este pode ser um sintoma em que os Contadores de Desempenho estão em estado de corrupção. Normalmente executando o seguinte comando a partir de um pedido de comando de administração corrige o problema:

  ```cmd
  lodctr /R
   ```

- Se encontrar um problema de conectividade, [recolha ficheiros](#trace-files)de rastreios, comprima-os e abra um bilhete de apoio no [portal Azure](https://portal.azure.com).

- Se receber uma mensagem de **serviço indisponível**, o emulador poderá estar a falhar ao inicializar a pilha de rede. Verifique se tem as redes de cliente seguro Pulse ou Juniper instaladas, dado que os respetivos controladores de filtro de rede poderão causar o problema. Desinstalar os controladores de filtro de rede de terceiros normalmente corrige o problema. Em alternativa, inicie o emulador com /DisableRIO, que irá mudar a comunicação da rede do emulador para winsock regular. 

- Enquanto o emulador estiver em execução, se o computador entrar no modo de suspensão ou executar quaisquer atualizações do SO, poderá ver a mensagem **O serviço está indisponível neste momento**. Repor os dados do emulador, clicando à direita no ícone que aparece no tabuleiro de notificação do Windows e selecione **Dados de Reset**.

### <a name="collect-trace-files"></a><a id="trace-files"></a>Recolher ficheiros de rastreio

Para recolher rastreios de depuração, execute os seguintes comandos a partir de uma linha de comandos administrativa:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `Microsoft.Azure.Cosmos.Emulator.exe /shutdown`. Observe o tabuleiro do sistema para certificar-se de que o programa foi encerrado; poderá demorar um minuto. Também pode clicar em **Sair** na interface de utilizador do Azure Cosmos Emulator.
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. Reproduza o problema. Se o Data Explorer não estiver a funcionar, apenas terá de aguardar que o browser abra por alguns segundos para apanhar o erro.
6. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
7. Navegue para `%ProgramFiles%\Azure Cosmos DB Emulator` e localize o ficheiro docdbemulator_000001.etl.
8. Abra um bilhete de apoio no [portal Azure](https://portal.azure.com) e inclua o ficheiro .etl juntamente com os passos de repro.

### <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>Desinstalar o emulador local

1. Saia de todas as instâncias abertas do emulador local clicando à direita no ícone do Emulador Azure Cosmos na bandeja do sistema e, em seguida, clicando em Saída. Pode demorar um minuto para que todas as instâncias possam sair.
2. Na caixa de pesquisa do Windows, escreva **Aplicações e funcionalidades** e clique no resultado **Aplicações e funcionalidades (Definições do sistema)**.
3. Na lista de aplicações, desloque para **Emulador do Azure Cosmos DB**, selecione-o, clique em **Desinstalar** e, em seguida, confirme e clique em **Desinstalar** novamente.
4. Quando a aplicação estiver desinstalada, navegue para `%LOCALAPPDATA%\CosmosDBEmulator` e elimine a pasta.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu a utilizar o emulador local para desenvolvimento local gratuito. Pode agora avançar para o próximo tutorial e aprender a exportar certificados emuladores TLS/SSL.

> [!div class="nextstepaction"]
> [Exportar os certificados emuladores Azure Cosmos](local-emulator-export-ssl-certificates.md)
