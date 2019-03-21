---
title: Utilizar o emulador de armazenamento do Azure para desenvolvimento e teste | Documentos da Microsoft
description: O emulador de armazenamento do Azure fornece um ambiente de desenvolvimento local gratuita para desenvolver e testar as suas aplicações de armazenamento do Azure. Saiba como os pedidos estão autorizados, como se pode ligar ao emulador do seu aplicativo e como usar a ferramenta da linha de comandos.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/10/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: fa5dfabeae829d52475d2e3cd6ccb123d8308c7c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013569"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Utilizar o emulador de armazenamento do Azure para desenvolvimento e teste

O emulador de armazenamento do Microsoft Azure fornece um ambiente local que emula os serviços de Blobs do Azure, filas e tabelas para fins de desenvolvimento. Utilizar o emulador de armazenamento, pode testar seu aplicativo contra os serviços de armazenamento localmente, sem criar uma subscrição do Azure ou incorrer em custos. Quando estiver satisfeito com o funcionamento da sua aplicação no emulador, pode mudar para utilizar uma conta de armazenamento do Azure na cloud.

## <a name="get-the-storage-emulator"></a>Obter o emulador de armazenamento
O emulador de armazenamento está disponível como parte dos [SDK do Microsoft Azure](https://azure.microsoft.com/downloads/). Também pode instalar o emulador de armazenamento utilizando o [instalador autônomo](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (download direto). Para instalar o emulador de armazenamento, tem de ter privilégios administrativos no seu computador.

O emulador de armazenamento atualmente é executado apenas no Windows. Para aqueles que estejam Considerando um emulador de armazenamento para Linux, uma opção é a Comunidade do emulador de armazenamento do código-fonte aberto, mantida [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Criado numa versão do emulador de armazenamento de dados não são garantidos a estar acessíveis ao utilizar uma versão diferente. Se precisar de manter os seus dados a longo prazo, recomendamos que armazene dados numa conta de armazenamento do Azure, em vez de no emulador do armazenamento.
> 
> O emulador de armazenamento depende de versões específicas das bibliotecas do OData. Substituir as DLLs de OData usado pelo emulador de armazenamento com outras versões não é suportado e pode causar comportamentos inesperados. No entanto, qualquer versão de OData suportada pelo serviço de armazenamento pode ser utilizada para enviar pedidos para o emulador.

## <a name="how-the-storage-emulator-works"></a>Como funciona o emulador de armazenamento
O emulador de armazenamento utiliza uma instância local do Microsoft SQL Server e o sistema de ficheiros local para emular a serviços de armazenamento do Azure. Por predefinição, o emulador de armazenamento utiliza uma base de dados no Microsoft SQL Server 2012 Express LocalDB. Pode optar por configurar o emulador do storage para aceder a uma instância local do SQL Server em vez da instância do LocalDB. Para obter mais informações, consulte a [início e de inicializar o emulador de armazenamento](#start-and-initialize-the-storage-emulator) seção mais adiante neste artigo.

O emulador de armazenamento se conecta ao SQL Server ou o LocalDB, utilizando a autenticação do Windows.

Existem algumas diferenças de funcionalidade entre os serviços de armazenamento do Azure e o emulador de armazenamento. Para obter mais informações sobre essas diferenças, veja a [diferenças entre o emulador de armazenamento e o armazenamento do Azure](#differences-between-the-storage-emulator-and-azure-storage) seção mais adiante neste artigo.

## <a name="start-and-initialize-the-storage-emulator"></a>Iniciar e inicializar o emulador de armazenamento

Para iniciar o emulador de armazenamento do Azure:
1. Selecione o **começar** botão ou prima o **Windows** chave.
2. Comece a escrever `Azure Storage Emulator`.
3. Selecione o emulador na lista de aplicações apresentadas.

Quando o emulador de armazenamento é iniciado, será apresentada uma janela de linha de comandos. Pode utilizar esta janela de consola para iniciar e parar o emulador de armazenamento, limpar os dados, obter o estado e inicializar o emulador. Para obter mais informações, consulte a [referência de ferramenta da linha de comandos do emulador de armazenamento](#storage-emulator-command-line-tool-reference) seção mais adiante neste artigo.

Quando o emulador estiver em execução, verá um ícone na área de notificação da barra de tarefas do Windows.

Quando fechar a janela de linha de comandos do emulador de armazenamento, o emulador de armazenamento continuará a ser executado. Para abrir novamente a janela da consola de emulador de armazenamento, siga os passos anteriores, como se a iniciar o emulador de armazenamento.

Na primeira vez que executa o emulador de armazenamento, o ambiente de armazenamento local é inicializado para. O processo de inicialização cria uma base de dados no LocalDB e reserva-se as portas HTTP para cada serviço de armazenamento local.

O emulador de armazenamento é instalado por predefinição para `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> Pode utilizar o [Explorador de armazenamento do Microsoft Azure](https://storageexplorer.com) para trabalhar com recursos de emulador de armazenamento local. Procure "(desenvolvimento)" em "Contas de armazenamento" na árvore de recursos do Explorador de armazenamento depois de ter instalado e iniciado o emulador de armazenamento.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicializar o emulador de armazenamento para utilizar uma base de dados diferente do SQL

Pode usar a ferramenta de linha de comandos de emulador de armazenamento para inicializar o emulador de armazenamento para apontar para uma instância de base de dados SQL que não seja a instância do LocalDB padrão:

1. Abra a janela de consola do emulador de armazenamento, conforme descrito no [início e de inicializar o emulador de armazenamento](#start-and-initialize-the-storage-emulator) secção.
1. Na janela da consola, escreva o seguinte comando, onde `<SQLServerInstance>` é o nome da instância do SQL Server. Para utilizar a LocalDB, especifique `(localdb)\MSSQLLocalDb` que a instância do SQL Server.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Também pode utilizar o seguinte comando, que direciona o emulador para utilizar a instância do SQL Server predefinida:

   `AzureStorageEmulator.exe init /server .`

   Em alternativa, pode utilizar o seguinte comando, que reinicializa a base de dados para a instância do LocalDB padrão:

   `AzureStorageEmulator.exe init /forceCreate`

Para obter mais informações sobre estes comandos, consulte [referência de ferramenta da linha de comandos do emulador de armazenamento](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Pode utilizar o [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para gerir as instâncias do SQL Server, incluindo a instalação do LocalDB. No SMSS **ligar ao servidor** caixa de diálogo, especifique `(localdb)\MSSQLLocalDb` no **nome do servidor:** campo para ligar à instância do LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autenticação de pedidos para o emulador de armazenamento
Depois de instalado e iniciado o emulador de armazenamento, pode testar seu código em relação a ele. Tal como acontece com o armazenamento do Azure na cloud, cada solicitação feita com o emulador de armazenamento tem de estar autorizada, a menos que seja um pedido anónimo. Pode autorizar pedidos contra o emulador de armazenamento a utilizar a autenticação de chave partilhada ou com uma assinatura de acesso partilhado (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autorizar com credenciais de chave partilhada
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Para obter mais informações sobre cadeias de ligação, veja [cadeias de ligação de configurar o armazenamento do Azure](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autorizar com uma assinatura de acesso partilhado

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Algumas bibliotecas de cliente de armazenamento do Azure, como a biblioteca do Xamarin, apenas suportam a autenticação com um token de assinatura (SAS) de acesso partilhado. É possível criar o token SAS usando uma ferramenta como o [Explorador de armazenamento](https://storageexplorer.com/) ou outra aplicação que suporta a autenticação de chave partilhada.

Também pode gerar um token SAS com o Azure PowerShell. O exemplo a seguir gera um token SAS com todas as permissões para um contentor de BLOBs:

1. Instalar o Azure PowerShell se ainda não o fez (usando a versão mais recente do Azure PowerShell cmdlets é recomendado). Para obter instruções de instalação, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).
2. Abra o Azure PowerShell e execute os seguintes comandos, substituindo `CONTAINER_NAME` com um nome à sua escolha:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

A assinatura de acesso partilhado resultante URI para o novo contentor deve ser semelhante a:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

A assinatura de acesso partilhado criada com este exemplo é válida durante um dia. A assinatura concede acesso total (leitura, escrita, eliminação, lista) aos blobs no contentor.

Para obter mais informações sobre assinaturas de acesso partilhado, consulte [Using partilhado assinaturas de acesso (SAS) no armazenamento do Azure](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Abordar os recursos no emulador do armazenamento
Os pontos de extremidade de serviço para o emulador de armazenamento são diferentes de uma conta de armazenamento do Azure. A diferença é porque o computador local não efetua a resolução de nomes de domínio, exigir que os pontos de extremidade de emulador de armazenamento para ser endereços locais.

Quando resolver um recurso numa conta de armazenamento do Azure, utilize o seguinte esquema. O nome da conta faz parte do nome de anfitrião do URI e o recurso a ser resolvido é parte do caminho do URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Por exemplo, o URI seguinte é um endereço válido para um blob numa conta de armazenamento do Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

No entanto, o emulador de armazenamento, uma vez que o computador local não efetua a resolução de nomes de domínio, o nome da conta é parte do caminho URI em vez do nome de anfitrião. Utilize o seguinte formato URI para um recurso no emulador do armazenamento:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Por exemplo, o seguinte endereço pode ser utilizado para aceder a um blob no emulador do armazenamento:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Os pontos de extremidade de serviço para o emulador de armazenamento são:

* Serviço blob: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Serviço de fila: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Serviço de tabela: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>A conta secundária com RA-GRS de endereçamento
A partir da versão 3.1, o emulador de armazenamento suporta a replicação georredundante de acesso de leitura (RA-GRS). Para os recursos de armazenamento na cloud e no emulador local, pode acessar a localização secundária, acrescentando - secundário para o nome da conta. Por exemplo, o seguinte endereço pode ser utilizado para aceder a um blob com a secundária só de leitura no emulador do armazenamento:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Para obter acesso programático para o secundário com o emulador de armazenamento, utilize a biblioteca de cliente de armazenamento para .NET versão 3.2 ou posterior. Consulte a [biblioteca do cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) para obter detalhes.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Referência de ferramenta da linha de comandos do emulador de armazenamento
A partir da versão 3.0, uma janela de console é exibida quando inicia o emulador de armazenamento. Utilize a linha de comandos na janela da consola para iniciar e parar o emulador, bem como a consulta para obter o estado e executar outras operações.

> [!NOTE]
> Se tiver o Microsoft Azure instalado do emulador de computação, é apresentado um ícone da bandeja do sistema quando inicia o emulador de armazenamento. Clique com o botão direito no ícone para revelar um menu que fornece uma maneira gráfica de iniciar e parar o emulador de armazenamento.
>
>

### <a name="command-line-syntax"></a>Sintaxe de linha de comandos
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opções
Para ver a lista de opções, escreva `/help` na linha de comandos.

| Opção | Descrição | Comando | Argumentos |
| --- | --- | --- | --- |
| **Começar** |Inicia o emulador de armazenamento. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: Inicie o emulador no processo atual em vez de criar um novo processo. |
| **Parar** |Interrompe o emulador de armazenamento. |`AzureStorageEmulator.exe stop` | |
| **Estado** |Imprime o estado do emulador de armazenamento. |`AzureStorageEmulator.exe status` | |
| **Clear** |Limpa os dados em todos os serviços especificados na linha de comando. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]                                                    ` |*blob*: Apaga dados de Blobs. <br/>*queue*: Limpa os dados de fila. <br/>*table*: Apaga dados da tabela. <br/>*all*: Limpa todos os dados em todos os serviços. |
| **Init** |Executa a inicialização única para configurar o emulador. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: Especifica o servidor que aloja a instância SQL. <br/>*-sqlinstance instanceName*: Especifica o nome da instância do SQL a ser utilizado na instância de servidor predefinida. <br/>*-forcecreate*: Força a criação da base de dados SQL, mesmo que já existe. <br/>*-skipcreate*: Ignora a criação da base de dados SQL. Isso tem precedência sobre - forcecreate.<br/>*-reserveports*: Tentativas para reservar as portas HTTP associadas aos serviços.<br/>*-unreserveports*: Tenta remover as reservas para as portas HTTP associadas aos serviços. Isso tem precedência sobre - reserveports.<br/>*-inprocess*: Executa a inicialização no processo atual em vez de gerar um novo processo. O processo atual deve ser iniciado com permissões elevadas, se alterar as reservas de porta. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Diferenças entre o emulador de armazenamento e o armazenamento do Azure
Uma vez que o emulador de armazenamento é um ambiente emulado em execução numa instância local do SQL, existem diferenças de funcionalidade entre o emulador e uma conta de armazenamento do Azure na cloud:

* O emulador de armazenamento suporta apenas uma única conta fixa e uma chave de autenticação bem conhecido.
* O emulador de armazenamento não é um serviço de armazenamento escalável e não suporta um grande número de clientes em simultâneo.
* Conforme descrito em [abordar os recursos no emulador do armazenamento](#addressing-resources-in-the-storage-emulator), recursos são abordados de forma diferente no emulador do armazenamento em comparação com uma conta de armazenamento do Azure. Essa diferença é porque a resolução de nomes de domínio está disponível na cloud, mas não no computador local.
* A partir da versão 3.1, a conta de emulador de armazenamento suporta a replicação georredundante de acesso de leitura (RA-GRS). No emulador, todas as contas de tem ativado o RA-GRS e nunca se qualquer atraso entre as réplicas primárias e secundárias. As operações de obter estatísticas do serviço de BLOBs, obter estatísticas do serviço de fila e obter estatísticas do serviço de tabela são suportadas na conta secundária e sempre retornará o valor da `LastSyncTime` elemento de resposta como a hora atual, de acordo com a base de dados do SQL subjacente.
* O serviço de ficheiros e os pontos finais de serviço do protocolo SMB não são atualmente suportados no emulador do armazenamento.
* Se utilizar uma versão dos serviços de armazenamento que ainda não é suportada pelo emulador do, o emulador de armazenamento devolve um erro de VersionNotSupportedByEmulator (código de estado HTTP 400 - pedido inválido).

### <a name="differences-for-blob-storage"></a>Diferenças do armazenamento de BLOBs
As seguintes diferenças aplicam-se ao armazenamento de BLOBs no emulador:

* O blob storage emulador apenas suporta tamanhos de até 2 GB.
* O comprimento máximo de um nome de blob no emulador do armazenamento é de 256 carateres, enquanto o comprimento máximo de um nome de blob no armazenamento do Azure é de 1024 caracteres.
* Cópia incremental permite que os instantâneos de blobs substituídos seja copiado, que retorna uma falha no serviço.
* Os intervalos obter páginas diferenciais não funcionam entre os instantâneos copiados através do Blob de cópia Incremental.
* Uma operação de colocar o Blob poderá ter sucesso em relação a um blob existente no emulador do armazenamento com uma concessão ativa, mesmo que o ID da concessão não foi especificado no pedido.
* Operações não são suportadas pelo emulador de Blob de acréscimo. Repetir uma operação num blob de acréscimo devolve um erro de FeatureNotSupportedByEmulator (código de estado HTTP 400 - pedido inválido).

### <a name="differences-for-table-storage"></a>Diferenças do armazenamento de tabelas
As seguintes diferenças aplicam-se ao armazenamento de tabelas no emulador:

* Propriedades de data no serviço tabela no emulador de armazenamento suportam apenas o intervalo suportado pelo SQL Server 2005 (eles têm de ser posterior a 1 de Janeiro de 1753). Todas as datas antes de 1 de Janeiro de 1753 são alteradas para este valor. A precisão de datas está limitada à precisão do SQL Server 2005, que significa que as datas são precisas para 1/300th de um segundo.
* O emulador de armazenamento suporta valores de propriedade de chave de linha e de chave de partição de menos de 512 bytes cada. Além disso, o tamanho total do nome da conta, o nome da tabela e nomes de propriedade de chave em conjunto não pode exceder 900 bytes.
* O tamanho total de uma linha numa tabela no emulador de armazenamento está limitado a inferior a 1 MB.
* No emulador do armazenamento, escreva as propriedades dos dados `Edm.Guid` ou `Edm.Binary` suportam apenas a `Equal (eq)` e `NotEqual (ne)` cadeias de caracteres de filtro de operadores de comparação na consulta.

### <a name="differences-for-queue-storage"></a>Diferenças do armazenamento de filas
Não existem diferenças específicas para o armazenamento de filas no emulador.

## <a name="storage-emulator-release-notes"></a>Notas de versão do emulador de armazenamento

### <a name="version-57"></a>Versão 5.7
Foi corrigido um erro que podem causar uma falha se o registo foi ativado.

### <a name="version-56"></a>Versão 5.6
* O emulador de armazenamento suporta agora a versão de 2018-03-28 dos serviços de armazenamento em pontos finais de serviço de BLOBs, filas e tabelas.

### <a name="version-55"></a>Versão 5.5
* O emulador de armazenamento suporta agora a versão 2017-11-09 dos serviços de armazenamento em pontos finais de serviço de BLOBs, filas e tabelas.
* Foi adicionado suporte para o blob **criado** propriedade, que retorna a hora de criação do blob.

### <a name="version-54"></a>Versão 5.4
Para melhorar a estabilidade de instalação, o emulador deixará de tentar reservar portas no momento da instalação. Se forem pretendidas reservas de porta, utilize o *- reserveports* opção da **init** comando especificá-los.

### <a name="version-53"></a>Versão 5.3
O emulador de armazenamento suporta agora a versão 2017-07-29 dos serviços de armazenamento em pontos finais de serviço de BLOBs, filas e tabelas.

### <a name="version-52"></a>Versão 5.2
* O emulador de armazenamento suporta agora a versão 2017-04-17 dos serviços de armazenamento em pontos finais de serviço de BLOBs, filas e tabelas.
* Foi corrigido um erro em que os valores de propriedade de tabela não foram sendo corretamente codificados.

### <a name="version-51"></a>Versão 5.1
Foi corrigido um erro em que o emulador de armazenamento foi retornando o `DataServiceVersion` cabeçalho em algumas respostas onde o serviço não era.

### <a name="version-50"></a>Versão 5.0
* O instalador do emulador de armazenamento já não verifica a existência de MSSQL existente e instala o .NET Framework.
* O instalador do emulador de armazenamento já não cria a base de dados como parte da instalação. Base de dados ainda será criado se for necessário como parte da inicialização.
* Criação de base de dados já não requer elevação.
* Reservas de porta já não são necessários para o arranque.
* Adiciona as seguintes opções para `init`: `-reserveports` (requer elevação), `-unreserveports` (requer elevação), `-skipcreate`.
* A opção de IU do emulador de armazenamento no ícone de tabuleiro de sistema agora inicia a interface de linha de comando. A GUI do antiga já não está disponível.
* Algumas DLLs foram removidas ou mudar o nome.

### <a name="version-46"></a>Versão 4.6
* O emulador de armazenamento suporta agora a versão 2016-05-31 dos serviços de armazenamento em pontos finais de serviço de BLOBs, filas e tabelas.

### <a name="version-45"></a>Versão 4.5
* Foi corrigido um erro que provocou a inicialização e instalação do emulador de armazenamento a falhar quando a base de dados de backup foi mudado.

### <a name="version-44"></a>Versão 4.4
* O emulador de armazenamento suporta agora a versão 2015-12-11 dos serviços de armazenamento em pontos finais de serviço de BLOBs, filas e tabelas.
* Coleta de lixo o emulador de armazenamento de dados de BLOBs, agora é mais eficiente ao lidar com um grande número de blobs.
* Foi corrigido um erro que provocou o contentor XML de ACL para ser validado de forma ligeiramente diferente de como o serviço de armazenamento faz isso.
* Foi corrigido um erro que às vezes causou máximos e mínimos valores de DateTime sejam comunicados no fuso horário incorreto.

### <a name="version-43"></a>Versão 4.3
* O emulador de armazenamento suporta agora a versão 2015-07-08 dos serviços de armazenamento em pontos finais de serviço de BLOBs, filas e tabelas.

### <a name="version-42"></a>Versão 4.2
* O emulador de armazenamento suporta agora 2015-04-05 de versão dos serviços de armazenamento em pontos finais de serviço de BLOBs, filas e tabelas.

### <a name="version-41"></a>Versão 4.1
* O emulador de armazenamento suporta agora a versão 2015-02-21 dos serviços de armazenamento em BLOBs, filas e tabela de pontos finais de serviço, exceto para os novos recursos de Blob de acréscimo.
* Se utilizar uma versão dos serviços de armazenamento que ainda não é suportada pelo emulador do, o emulador devolve uma mensagem de erro que faz sentido. Recomendamos que utilize a versão mais recente do emulador. Se ocorrer um erro de VersionNotSupportedByEmulator (código de estado HTTP 400 - pedido inválido), baixe a versão mais recente do emulador de armazenamento.
* Foi corrigido um erro em que uma corrida condição que causou tabela entidade de dados incorreto durante as operações de intercalação em simultâneo.

### <a name="version-40"></a>Versão 4.0
* O executável de emulador de armazenamento foi mudado para *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versão 3.2
* O emulador de armazenamento suporta agora a versão 2014-02-14 dos serviços de armazenamento em pontos finais de serviço de BLOBs, filas e tabelas. Pontos finais de serviço de arquivo não são atualmente suportados no emulador do armazenamento. Ver [controle de versão dos serviços de armazenamento do Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) para obter detalhes sobre a versão 2014-02-14.

### <a name="version-31"></a>Versão 3.1
* Armazenamento georredundante com acesso de leitura (RA-GRS) é agora suportado o emulador de armazenamento. A obter estatísticas do serviço de BLOBs, obter estatísticas do serviço de fila e obter APIs de estatísticas de serviço de tabela são suportadas para a conta secundária e sempre retornará o valor do elemento de resposta LastSyncTime como a hora atual, de acordo com a base de dados do SQL subjacente. Para obter acesso programático para o secundário com o emulador de armazenamento, utilize a biblioteca de cliente de armazenamento para .NET versão 3.2 ou posterior. Consulte a biblioteca de cliente do armazenamento do Microsoft Azure para referência de .NET para obter detalhes.

### <a name="version-30"></a>Versão 3.0
* O emulador de armazenamento do Azure já não é fornecido no mesmo pacote como o emulador de computação.
* A interface de utilizador gráfica de emulador de armazenamento foi preterida em favor de uma interface de linha de comando programável por scripts. Para obter detalhes sobre a interface de linha de comandos, consulte a referência de ferramenta de linha de comandos de emulador de armazenamento. A interface gráfica vai continuar a ser presentes na versão 3.0, mas só pode ser acedido quando o emulador de computação é instalado ao clicar com o botão direito no ícone de tabuleiro de sistema e selecionar mostrar IU do emulador de armazenamento.
* Versão 2013-08-15 dos serviços de armazenamento do Azure agora é totalmente suportada. (Anteriormente esta versão apenas era suportada pela versão 2.2.1 do emulador de armazenamento pré-visualização.)

## <a name="next-steps"></a>Passos Seguintes

* Avaliar o emulador de armazenamento do código-fonte aberto para várias plataformas, mantidos de Comunidade [Azurite](https://github.com/arafato/azurite). 
* [Exemplos de armazenamento do Azure com o .NET](../storage-samples-dotnet.md) contém links para vários exemplos de código, pode usar ao desenvolver a sua aplicação.
* Pode utilizar o [Explorador de armazenamento do Microsoft Azure](https://storageexplorer.com) para trabalhar com recursos na sua conta de armazenamento da cloud e no emulador do armazenamento.
