---
title: Linha de comando e referência PowerShell para Emulador Azure Cosmos DB
description: Aprenda os parâmetros da linha de comando para O Emulador Azure Cosmos DB, como controlar o emulador com PowerShell e como alterar o número de recipientes que pode criar dentro do emulador.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: e0c795484bf860402d05c1dc5779633962a44ec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979509"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Linha de comando e referência PowerShell para Emulador Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB Emulator fornece um ambiente local que imita o serviço DB Azure Cosmos para fins de desenvolvimento local. Depois [de instalar o emulador,](local-emulator.md)pode controlar o emulador com linha de comando e comandos PowerShell. Este artigo descreve como usar a linha de comando e os comandos PowerShell para iniciar e parar o emulador, configurar opções e executar outras operações. Tem que executar os comandos a partir do local de instalação.

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>Gerir o emulador com sintaxe de linha de comando

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

Para ver a lista de opções, escreva `Microsoft.Azure.Cosmos.Emulator.exe /?` na linha de comandos.

|**Opção** | **Descrição** | **Comando**| **Argumentos**|
|---|---|---|---|
|[Sem argumentos] | Inicia o Emulador do Azure Cosmos DB com as predefinições. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Ajuda] |Mostra a lista dos argumentos da linha de comandos suportados.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Obtém o estado do Emulador do Azure Cosmos DB. O estado é indicado pelo código de saída: 1 = A iniciar, 2 = Em execução, 3 = Parado. Um código de saída negativo indica que ocorreu um erro. Não é produzido outro resultado. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Encerrar| Encerra o Emulador do Azure Cosmos DB.| Microsoft.Azure.Cosmos.Emulator.exe /Encerramento | |
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
|FailOnSslCertificateNameMismatch | Por predefinição, o emulador regenera o seu certificado TLS/SSL auto-assinado, se o certificado san não incluir o nome de domínio do hospedeiro emulador, o endereço IPv4 local, "localhost" e "127.0.0.1". Com esta opção, o emulador falhará no arranque. Em seguida, deve utilizar a opção /GenCert para criar e instalar um novo certificado TLS/SSL auto-assinado. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Gere e instale um novo certificado TLS/SSL auto-assinado. opcionalmente incluindo uma lista separada por vírgula de nomes DNS adicionais para aceder ao emulador sobre a rede. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<dns-names\>: Lista opcional separada de vírgulas de nomes dns adicionais  |
| DirectPorts |Especifica as portas a utilizar para conectividade direta. As predefinições são 10251,10252,10253,10254. | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: Lista delimitada por vírgula de 4 portos |
| Chave |Chave de autorização para o emulador. A chave tem de ser a codificação base 64 de um vetor de 64 bytes. | Microsoft.Azure.Cosmos.Emulator.exe /Chave:\<key\> | \<key\>: A chave deve ser a codificação base-64 de um vetor de 64 bytes|
| EnableRateLimiting | Especifica que o comportamento de limitação da taxa de pedidos está ativado. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Especifica que o comportamento de limitação da taxa de pedidos está desativado. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | Não mostrar a interface de utilizador do emulador. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Não mostrar o explorador de dados no arranque. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Especifica o número máximo de recipientes divididos. Consulte [alterar o número de recipientes](#set-partitioncount) para obter mais informações. | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>: Número máximo de recipientes de partição simples permitidos. O valor predefinido é 25. O máximo permitido é 250.|
| DefaultPartitionCount| Especifica o número predefinido de divisórias para um recipiente dividido. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> O valor predefinido é de 25.|
| AllowNetworkAccess | Permite o acesso ao emulador através de uma rede. Também tem de passar /Key= \<key_string\> ou /KeyFile= \<file_name\> para permitir o acesso à rede. | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key= \<key_string\> ou Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Não ajuste as regras de firewall quando /Permitir a opção Desajuste de Internet. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Gerar uma nova chave de autorização e guardá-la no ficheiro especificado. A chave gerada pode ser utilizada com as opções /Key ou /KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<path to key file\> | |
| Consistência | Defina o nível de consistência predefinida para a conta. | Microsoft.Azure.Cosmos.Emulator.exe /Consistência=\<consistency\> | \<consistency\>: O valor deve ser um dos [seguintes níveis de consistência](consistency-levels.md): Sessão, Forte, Eventual ou LimitadaStaleness. O valor predefinido é Session. |
| ? | Mostrar a mensagem de ajuda.| | |

## <a name="manage-the-emulator-with-powershell"></a>Gerir o emulador com PowerShell

O emulador vem com um módulo PowerShell para iniciar, parar, desinstalar e recuperar o estado do serviço. Executar o seguinte cmdlet para utilizar o módulo PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

ou colocar o `PSModules` diretório no seu `PSModulePath` e importá-lo como mostrado no seguinte comando:

```powershell
$env:PSModulePath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
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

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>Alterar o número de recipientes predefinidos

Por padrão, pode criar até 25 recipientes de tamanho fixo (apenas suportados com Azure Cosmos DB SDKs), ou 5 contentores ilimitados utilizando o Emulador Azure Cosmos DB. Ao modificar o valor **PartitionCount,** pode criar até 250 recipientes de tamanho fixo ou 50 contentores ilimitados, ou qualquer combinação dos dois que não exceda 250 recipientes de tamanho fixo (onde um recipiente ilimitado = 5 recipientes de tamanho fixo). No entanto, não é aconselhável configurar o emulador para funcionar com mais de 200 recipientes de tamanho fixo. Devido à sobrecarga que adiciona às operações de IO do disco, que resultam em intervalos de tempo imprevisíveis ao utilizar as APIs do ponto final.

Se tentar criar um recipiente após a exceção da contagem atual de divisórias, o emulador lança uma exceção ServiceUn disponível, com a seguinte mensagem.

> Desculpe, estamos atualmente a ter uma grande procura nesta região, e não podemos satisfazer o seu pedido neste momento. Trabalhamos continuamente para trazer cada vez mais capacidade on-line, e encorajá-lo a tentar novamente.
> AtividadeSId: 12345678-1234-1234-1234-123456789abc

Para alterar o número de contentores disponíveis no Emulador Azure Cosmos DB, executar os seguintes passos:

1. Elimine todos os dados locais do Emulador do Azure Cosmos DB, ao clicar com o botão direito do rato no ícone do **Emulador do Azure Cosmos DB** no tabuleiro do sistema e, em seguida, clique em **Repor Dados...**.

1. Elimine todos os dados do emulador nesta pasta `%LOCALAPPDATA%\CosmosDBEmulator` .

1. Saia de todas as instâncias abertas, ao clicar com o botão direito do rato no ícone do **Emulador do Azure Cosmos DB** no tabuleiro do sistema e, em seguida, clique em **Sair**. Pode demorar um minuto para que todas as instâncias possam sair.

1. Instale a versão mais recente do [Emulador do Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

1. Inicie o emulador com o sinalizador de PartitionCount, ao definir um valor <= 250. Por exemplo: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.
 
## <a name="next-steps"></a>Passos seguintes

* [Exporte os certificados emuladores Azure Cosmos DB para uso com aplicativos Java, Python e Node.js](local-emulator-export-ssl-certificates.md)
* [Problemas de depurar com o emulador](troubleshoot-local-emulator.md)
