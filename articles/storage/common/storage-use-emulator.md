---
title: Utilize o emulador de armazenamento Azure para desenvolvimento e teste / Microsoft Docs
description: O emulador de armazenamento Azure proporciona um ambiente de desenvolvimento local gratuito para desenvolver e testar as suas aplicações de Armazenamento Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 997aa9d96f2f52331865fd15d97443d74bb8bc1f
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398003"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Utilize o emulador de armazenamento Azure para desenvolvimento e teste

O emulador de armazenamento do Microsoft Azure é uma ferramenta que imita os serviços Azure Blob, Queue e Table para fins de desenvolvimento local. Pode testar a sua aplicação contra os serviços de armazenamento localmente sem criar uma subscrição Azure ou incorrer em quaisquer custos. Quando estiver satisfeito com a forma como a sua aplicação está a funcionar no emulador, mude para a utilização de uma conta de armazenamento Azure na nuvem.

## <a name="get-the-storage-emulator"></a>Pegue o emulador de armazenamento

O emulador de armazenamento está disponível como parte do [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Também pode instalar o emulador de armazenamento utilizando o [instalador autónomo](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (download direto). Para instalar o emulador de armazenamento, deve ter privilégios administrativos no seu computador.

O emulador de armazenamento funciona atualmente apenas no Windows. Se você precisa de um emulador de armazenamento para Linux, uma opção é a comunidade mantida, emulador de armazenamento de código aberto [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Os dados criados numa versão do emulador de armazenamento não estão garantidos para serem acessíveis quando se utiliza uma versão diferente. Se precisar de persistir os seus dados a longo prazo, recomendamos que guarde esses dados numa conta de armazenamento Azure, em vez de no emulador de armazenamento.
> 
> O emulador de armazenamento depende de versões específicas das bibliotecas OData. A substituição dos DLLs OData utilizados pelo emulador de armazenamento por outras versões não é suportada e pode causar comportamentos inesperados. No entanto, qualquer versão do OData suportada pelo serviço de armazenamento pode ser utilizada para enviar pedidos ao emulador.

## <a name="how-the-storage-emulator-works"></a>Como funciona o emulador de armazenamento

O emulador de armazenamento utiliza uma instância local do Microsoft SQL Server 2012 Express LocalDB para imitar os serviços de armazenamento do Azure. Pode escolher configurar o emulador de armazenamento para aceder a uma instância local do SQL Server em vez da instância LocalDB. Consulte o [Início e inicie a](#start-and-initialize-the-storage-emulator) secção de emulador de armazenamento mais tarde neste artigo para saber mais.

O emulador de armazenamento liga-se ao SQL Server ou ao LocalDB utilizando a autenticação do Windows.

Existem algumas diferenças de funcionalidade entre o emulador de armazenamento e os serviços de armazenamento Azure. Para obter mais informações sobre estas diferenças, consulte as [Diferenças entre o emulador de armazenamento e](#differences-between-the-storage-emulator-and-azure-storage) a secção de Armazenamento Azure mais tarde neste artigo.

## <a name="start-and-initialize-the-storage-emulator"></a>Iniciar e inicializar o emulador de armazenamento

Para iniciar o emulador de armazenamento Azure:

1. Selecione o botão **Iniciar** ou prima a tecla **Windows.**
2. Comece `Azure Storage Emulator`a escrever.
3. Selecione o emulador da lista de aplicações apresentadas.

Quando o emulador de armazenamento começar, aparecerá uma janela De Comando Pronta. Pode utilizar esta janela da consola para iniciar e parar o emulador de armazenamento. Também pode limpar dados, obter o estado e inicializar o emulador a partir do pedido de comando. Para mais informações, consulte a secção de referência da linha de comando do [emulador de armazenamento](#storage-emulator-command-line-tool-reference) mais tarde neste artigo.

> [!NOTE]
> O emulador de armazenamento Azure pode não começar corretamente se outro emulador de armazenamento, como o Azurite, estiver a funcionar no sistema.

Quando o emulador estiver em execução, verá um ícone na área de notificação da barra de tarefas do Windows.

Quando fechar a janela de emulador de armazenamento Command Prompt, o emulador de armazenamento continuará a funcionar. Para voltar a elevar a janela da consola do Emmulador de Armazenamento, siga os passos anteriores como se estivesse a iniciar o emulador de armazenamento.

A primeira vez que executa o emulador de armazenamento, o ambiente de armazenamento local é inicializado para si. O processo de inicialização cria uma base de dados em LocalDB e reserva portas HTTP para cada serviço de armazenamento local.

O emulador de armazenamento é `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`instalado por defeito para .

> [!TIP]
> Pode utilizar o [Microsoft Azure Storage Explorer](https://storageexplorer.com) para trabalhar com recursos de emulador de armazenamento local. Procure "(Emulator - Portas Padrão) (Chave)" em "Local & Anexado" na árvore de recursos do Explorador de Armazenamento depois de ter instalado e iniciado o emulador de armazenamento.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicialize o emulador de armazenamento para usar uma base de dados SQL diferente

Pode utilizar a ferramenta de linha de comando do emulador de armazenamento para inicializar o emulador de armazenamento para apontar para uma instância de base de dados SQL que não seja a instância localDB padrão:

1. Abra a janela da consola de emulador de armazenamento como descrito no Início e inicialize a secção de [emulador](#start-and-initialize-the-storage-emulator) de armazenamento.
1. Na janela da consola, digite `<SQLServerInstance>` o seguinte comando, onde está o nome da instância SQL Server. Para utilizar o `(localdb)\MSSQLLocalDb` LocalDB, especifique como a instância Do Servidor SQL.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Também pode utilizar o seguinte comando, que direciona o emulador para utilizar a instância padrão do Servidor SQL:

   `AzureStorageEmulator.exe init /server .`

   Ou, pode utilizar o seguinte comando, que reininicia a base de dados para a instância localdb padrão:

   `AzureStorageEmulator.exe init /forceCreate`

Para obter mais informações sobre estes comandos, consulte a referência da ferramenta de linha de comando do [emulador de armazenamento](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Pode utilizar o [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para gerir as instâncias do SQL Server, incluindo a instalação LocalDB. No diálogo SMSS Connect to `(localdb)\MSSQLLocalDb` **Server,** especifique no nome **server:** campo para ligar à instância LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autenticação de pedidos contra o emulador de armazenamento

Depois de ter instalado e iniciado o emulador de armazenamento, pode testar o seu código contra ele. Todos os pedidos que fizer contra o emulador de armazenamento devem ser autorizados, a menos que seja um pedido anónimo. Pode autorizar pedidos contra o emulador de armazenamento utilizando a autenticação da Chave Partilhada ou com uma assinatura de acesso partilhado (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autorizar com credenciais de chave partilhada

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Para obter mais informações sobre as cordas de ligação, consulte as cordas de ligação de [armazenamento Configure Azure](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autorizar com uma assinatura de acesso partilhado

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Algumas bibliotecas de clientes de armazenamento Azure, como a biblioteca Xamarin, apenas suportam a autenticação com um símbolo de assinatura de acesso partilhado (SAS). Pode criar o token SAS utilizando o [Storage Explorer](https://storageexplorer.com/) ou outra aplicação que suporta a autenticação de Chave Partilhada.

Também pode gerar um token SAS utilizando o Azure PowerShell. O exemplo seguinte gera um símbolo SAS com permissões completas a um recipiente de bolhas:

1. Instale o Azure PowerShell se ainda não o fez (recomenda-se a utilização da versão mais recente dos cmdlets Azure PowerShell). Para instruções de instalação, consulte [Instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).
2. Abra a PowerShell Azure e execute `CONTAINER_NAME` os seguintes comandos, substituindo-o por um nome à sua escolha:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

A assinatura de acesso partilhado resultante URI para o novo recipiente deve ser semelhante a:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

A assinatura de acesso partilhado criada com este exemplo é válida por um dia. A assinatura concede acesso total (ler, escrever, excluir, listar) a bolhas dentro do recipiente.

Para obter mais informações sobre assinaturas de acesso partilhado, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Abordar os recursos no emulador de armazenamento

Os pontos finais de serviço para o emulador de armazenamento são diferentes dos pontos finais para uma conta de armazenamento Azure. O computador local não faz resolução de nomede domínio, exigindo que os pontos finais do emulador de armazenamento sejam endereços locais.

Quando se trata de um recurso numa conta de armazenamento Azure, utiliza-se o seguinte esquema. O nome da conta faz parte do nome de anfitrião uri, e o recurso que está a ser endereçado faz parte do caminho URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Por exemplo, o seguinte URI é um endereço válido para uma bolha numa conta de armazenamento Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Como o computador local não faz a resolução de nomes de domínio, o nome da conta faz parte do caminho URI em vez do nome do anfitrião. Utilize o seguinte formato URI para um recurso no emulador de armazenamento:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Por exemplo, pode ser utilizado o seguinte endereço para aceder a uma bolha no emulador de armazenamento:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Os pontos finais de serviço para o emulador de armazenamento são:

* Serviço blob:`http://127.0.0.1:10000/<account-name>/<resource-path>`
* Serviço de fila:`http://127.0.0.1:10001/<account-name>/<resource-path>`
* Serviço de mesa:`http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Endereçamento da conta secundária com RA-GRS

Começando com a versão 3.1, o emulador de armazenamento suporta a replicação georedundantde de acesso de leitura (RA-GRS). Pode aceder à localização secundária através de despesas secundárias ao nome da conta. Por exemplo, pode ser utilizado o seguinte endereço para aceder a uma bolha utilizando o secundário apenas de leitura no emulador de armazenamento:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Para acesso programático ao secundário com o emulador de armazenamento, utilize a Biblioteca do Cliente de Armazenamento para .NET versão 3.2 ou posterior. Consulte a Biblioteca de Clientes de [Armazenamento Microsoft Azure para obter](https://msdn.microsoft.com/library/azure/dn261237.aspx) mais detalhes.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Referência da linha de ferramenta de linha de comando do emulador de armazenamento

A partir da versão 3.0, é apresentada uma janela de consola quando iniciar o Emulador de Armazenamento. Utilize a linha de comando na janela da consola para iniciar e parar o emulador. Também pode consultar o estado e fazer outras operações a partir da linha de comando.

> [!NOTE]
> Se tiver instalado o emulador de cálculo microsoft Azure, aparece um ícone de bandeja de sistema quando lança o Emulador de Armazenamento. Clique no ícone para revelar um menu que fornece uma forma gráfica de iniciar e parar o Emulador de Armazenamento.
>
>

### <a name="command-line-syntax"></a>Sintaxe da linha de comandos

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opções

Para ver a lista de opções, escreva `/help` na linha de comandos.

| Opção | Descrição | Comando | Argumentos |
| --- | --- | --- | --- |
| **Iniciar** |Começa o emulador de armazenamento. |`AzureStorageEmulator.exe start [-inprocess]` |*-Reprocessar*: Inicie o emulador no processo atual em vez de criar um novo processo. |
| **Parar** |Detém o emulador de armazenamento. |`AzureStorageEmulator.exe stop` | |
| **Estado** |Imprime o estado do emulador de armazenamento. |`AzureStorageEmulator.exe status` | |
| **Limpar** |Limpa os dados em todos os serviços especificados na linha de comando. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*: Limpa os dados da bolha. <br/>*fila*: Limpa os dados da fila. <br/>*tabela*: Limpa os dados da tabela. <br/>*todos*os dados : Limpa todos os dados em todos os serviços. |
| **Init** |Faz uma única inicialização para configurar o emulador. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-servidor servidorNome\instânciaNome*: Especifica o servidor que acolhe a instância SQL. <br/>*-instância de instância de sqlinstanceNome*: Especifica o nome da instância SQL a ser utilizado na instância do servidor predefinido. <br/>*-forcecreate*: Force criação da base de dados SQL, mesmo que já exista. <br/>*-skipcreate*: Ignora a criação da base de dados SQL. Isto tem precedência sobre a criação de força.<br/>*-reserveports*: Tentativas de reserva dos portos HTTP associados aos serviços.<br/>*-desreservaports*: Tentativas de remover reservas para as portas HTTP associadas aos serviços. Isto tem precedência sobre os portos de reserva.<br/>*-inprocess*: Executa a inicialização no processo atual em vez de desovar um novo processo. O processo atual deve ser lançado com permissões elevadas se alterar as reservas portuárias. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Diferenças entre o emulador de armazenamento e o armazenamento azure

Como o emulador de armazenamento é um ambiente emulado localmente, existem diferenças entre usar o emulador e uma conta de armazenamento Azure na nuvem:

* O emulador de armazenamento suporta apenas uma única conta fixa e uma chave de autenticação bem conhecida.
* O emulador de armazenamento não é um serviço de armazenamento escalável e não suporta um grande número de clientes simultâneos.
* Tal como descrito na abordagem dos [recursos no emulador](#addressing-resources-in-the-storage-emulator)de armazenamento, os recursos são abordados de forma diferente no emulador de armazenamento versus uma conta de armazenamento Azure. A diferença é porque a resolução de nome de domínio está disponível na nuvem, mas não no computador local.
* Começando com a versão 3.1, a conta de emulador de armazenamento suporta replicação georedundantde de acesso de leitura (RA-GRS). No emulador, todas as contas têm RA-GRS ativado e nunca há qualquer desfasamento entre as réplicas primárias e secundárias. As estatísticas do serviço Get Blob, obtenha estatísticas de serviço de fila e as operações de `LastSyncTime` estatísticas do serviço de mesa são suportadas na conta secundária e sempre devolverão o valor do elemento de resposta como o tempo atual de acordo com a base de dados SQL subjacente.
* O serviço de ficheiros e os pontos finais do serviço de protocolo SMB não são suportados atualmente no emulador de armazenamento.
* Se utilizar uma versão dos serviços de armazenamento que não é suportado pelo emulador, o emulador devolve um erro de VersionNotSupportedByEmulator (código de estado HTTP 400 - Pedido Mau).

### <a name="differences-for-blob-storage"></a>Diferenças para o armazenamento blob

As seguintes diferenças aplicam-se ao armazenamento blob no emulador:

* O emulador de armazenamento suporta apenas tamanhos de bolhas até 2 GB.
* O comprimento máximo de um nome blob no emulador de armazenamento é de 256 caracteres, enquanto o comprimento máximo de um nome blob no Armazenamento Azure é de 1024 caracteres.
* A cópia incremental permite copiar instantâneos de bolhas sobreescritas, o que devolve uma falha no serviço.
* Obter Intervalos de página Diff não funciona entre instantâneos copiados usando Incremental Copy Blob.
* Uma operação Put Blob pode ter sucesso contra uma bolha que existe no emulador de armazenamento com um contrato de arrendamento ativo, mesmo que o ID de locação não tenha sido especificado no pedido.
* As operações do apêndice Blob não são apoiadas pelo emulador. A tentativa de uma operação numa bolha de apêndice devolve um erro de RecursoNotSupportedByEmulator (código de estado HTTP 400 - Pedido Mau).

### <a name="differences-for-table-storage"></a>Diferenças para armazenamento de mesa

As seguintes diferenças aplicam-se ao armazenamento de mesa no emulador:

* As propriedades da data no serviço de mesa no suporte do emulador de armazenamento apenas a gama suportada pelo SQL Server 2005 (são necessárias para serem mais tarde do que 1 de janeiro de 1753). Todas as datas antes de 1 de janeiro de 1753 são alteradas para este valor. A precisão das datas limita-se à precisão do SQL Server 2005, o que significa que as datas são precisas a 1/300 de segundo.
* O emulador de armazenamento suporta valores de propriedade chave de divisória e linha de menos de 512 bytes cada. O tamanho total do nome da conta, nome da tabela e nomes de propriedade chave juntos não podem exceder 900 bytes.
* O tamanho total de uma linha numa mesa no emulador de armazenamento está limitado a menos de 1 MB.
* No emulador de armazenamento, `Edm.Guid` as `Edm.Binary` propriedades `Equal (eq)` do `NotEqual (ne)` tipo de dados ou suporte apenas os operadores e comparadores nas cordas de filtro de consulta.

### <a name="differences-for-queue-storage"></a>Diferenças para armazenamento de fila

Não existem diferenças específicas para o armazenamento de fila no emulador.

## <a name="storage-emulator-release-notes"></a>Notas de lançamento do emulador de armazenamento

### <a name="version-510"></a>Versão 5.10

* O emulador de armazenamento não rejeitará a versão 2019-07-07 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table.

### <a name="version-59"></a>Versão 5.9

* O emulador de armazenamento não rejeitará a versão 2019-02-02 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table.

### <a name="version-58"></a>Versão 5.8

* O emulador de armazenamento não rejeitará a versão 2018-11-09 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table.

### <a name="version-57"></a>Versão 5.7

* Fixou um inseto que causaria um acidente se o registo fosse ativado.

### <a name="version-56"></a>Versão 5.6

* O emulador de armazenamento suporta agora a versão 2018-03-28 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table.

### <a name="version-55"></a>Versão 5.5

* O emulador de armazenamento suporta agora a versão 2017-11-09 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table.
* Foi adicionado apoio para a propriedade blob **Created,** que devolve o tempo de criação da bolha.

### <a name="version-54"></a>Versão 5.4

* Para melhorar a estabilidade da instalação, o emulador já não tenta reservar portas no momento da instalação. Se desejar reservas por porta, utilize a opção *-reserveports* do comando **init** para especificá-las.

### <a name="version-53"></a>Versão 5.3

* O emulador de armazenamento suporta agora a versão 2017-07-29 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table.

### <a name="version-52"></a>Versão 5.2

* O emulador de armazenamento suporta agora a versão 2017-04-17 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table.
* Fixou um bug onde os valores da propriedade da mesa não estavam a ser devidamente codificados.

### <a name="version-51"></a>Versão 5.1

* Fixou um bug onde o emulador de armazenamento estava devolvendo o `DataServiceVersion` cabeçalho em algumas respostas onde o serviço não estava.

### <a name="version-50"></a>Versão 5.0

* O instalador de emuladores de armazenamento já não verifica as instalações existentes do MSSQL e da .NET Framework.
* O instalador de emuladores de armazenamento já não cria a base de dados como parte da instalação. A base de dados continuará a ser criada se necessário como parte do arranque.
* A criação de bases de dados já não requer elevação.
* As reservas portuárias já não são necessárias para o arranque.
* Adiciona as seguintes opções `init`a: `-reserveports` (requer elevação), `-unreserveports` (requer elevação), `-skipcreate`.
* A opção De mulador de armazenamento UI no ícone da bandeja do sistema lança agora a interface da linha de comando. O velho GUI já não está disponível.
* Alguns DLLs foram removidos ou renomeados.

### <a name="version-46"></a>Versão 4.6

* O emulador de armazenamento suporta agora a versão 2016-05-31 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table.

### <a name="version-45"></a>Versão 4.5

* Fixou um bug que fez com que a instalação e a inicialização falhassem quando a base de dados de apoio é renomeada.

### <a name="version-44"></a>Versão 4.4

* O emulador de armazenamento suporta agora a versão 2015-12-11 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table.
* A recolha de dados de lixo do emulador de armazenamento é agora mais eficiente quando se lida com um grande número de bolhas.
* Fixou um bug que fez com que o recipiente ACL XML fosse validado de forma ligeiramente diferente da forma como o serviço de armazenamento o faz.
* Fixou um bug que por vezes fez com que os valores de data máxima e min fossem reportados no fuso horário incorreto.

### <a name="version-43"></a>Versão 4.3

* O emulador de armazenamento suporta agora a versão 2015-07-08 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table.

### <a name="version-42"></a>Versão 4.2

* O emulador de armazenamento suporta agora a versão 2015-04-05 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table.

### <a name="version-41"></a>Versão 4.1

* O emulador de armazenamento suporta agora a versão 2015-02-21 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table. Não suporta as novas funcionalidades do Append Blob.
* O emulador devolve agora uma mensagem de erro significativa para versões não suportadas de serviços de armazenamento. Recomendamos a utilização da versão mais recente do emulador. Se tiver um erro de VersionNotSupportedByEmulator (código de estado HTTP 400 - Bad Request), faça o download da versão mais recente do emulador.
* Fixou um bug em que uma condição de regata fez com que os dados da entidade da tabela fossem incorretos durante operações de fusão simultâneas.

### <a name="version-40"></a>Versão 4.0

* O emulador de armazenamento executável foi renomeado para *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versão 3.2

* O emulador de armazenamento suporta agora a versão 2014-02-14 dos serviços de armazenamento em pontos finais de serviço Blob, Queue e Table. Os pontos finais do serviço de ficheiros não são suportados atualmente no emulador de armazenamento. Consulte [a versão para os Serviços](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) de Armazenamento Azure para mais detalhes sobre a versão 2014-02-14.

### <a name="version-31"></a>Versão 3.1

* O armazenamento geo-redundante de acesso de leitura (RA-GRS) é agora suportado no emulador de armazenamento. As `Get Blob Service Stats` `Get Queue Service Stats`, `Get Table Service Stats` e APIs são suportadas para a conta secundária e sempre devolverão o valor do elemento de resposta LastSyncTime como o tempo atual de acordo com a base de dados SQL subjacente. Para acesso programático ao secundário com o emulador de armazenamento, utilize a Biblioteca do Cliente de Armazenamento para .NET versão 3.2 ou posterior. Consulte a Biblioteca de Clientes de Armazenamento Microsoft Azure para obter uma referência .NET para mais detalhes.

### <a name="version-30"></a>Versão 3.0

* O emulador de armazenamento Azure já não é enviado na mesma embalagem que o emulador de cálculo.
* A interface gráfica do utilizador do emulador de armazenamento é depreciada. Foi substituído por uma interface de linha de comando scriptable. Para mais detalhes sobre a interface da linha de comando, consulte a referência da ferramenta de linha de comando do Emulador de Armazenamento. A interface gráfica continuará presente na versão 3.0, mas só pode ser acedida quando o Emulador Compute for instalado clicando no ícone da bandeja do sistema e selecionando o Emulador de Armazenamento de Show Storage UI.
* A versão 2013-08-15 dos serviços de armazenamento Do Azure está agora totalmente suportada. (Anteriormente, esta versão só foi suportada pela versão 2.2.1 Preview do Emulador de Armazenamento.)

## <a name="next-steps"></a>Passos seguintes

* Avalie o emulador de armazenamento de código aberto [azurite](https://github.com/azure/azurite)de plataforma cruzada e mantido pela comunidade. 
* [As amostras de armazenamento azure que utilizam .NET](../storage-samples-dotnet.md) contém links para várias amostras de código que pode utilizar no desenvolvimento da sua aplicação.
* Pode utilizar o [Microsoft Azure Storage Explorer](https://storageexplorer.com) para trabalhar com recursos na sua conta de armazenamento na nuvem e no emulador de armazenamento.
