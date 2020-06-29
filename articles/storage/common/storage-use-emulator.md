---
title: Utilize o emulador de armazenamento Azure para desenvolvimento e testes Microsoft Docs
description: O emulador de armazenamento Azure proporciona um ambiente de desenvolvimento local gratuito para desenvolver e testar as suas aplicações de Armazenamento Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/21/2019
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: eb13dbb7e4cfbbb1b2ea42ea1753e7615df03a7d
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85512177"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Utilize o emulador de armazenamento Azure para desenvolvimento e teste

O emulador de armazenamento Microsoft Azure é uma ferramenta que imita os serviços de Azure Blob, Queue e Table para fins de desenvolvimento local. Pode testar a sua aplicação contra os serviços de armazenamento localmente sem criar uma subscrição Azure ou incorrer em quaisquer custos. Quando estiver satisfeito com o funcionamento da sua aplicação no emulador, mude para a utilização de uma conta de armazenamento Azure na nuvem.

## <a name="get-the-storage-emulator"></a>Pegue o emulador de armazenamento

O emulador de armazenamento está disponível como parte do [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Também pode instalar o emulador de armazenamento utilizando o [instalador autónomo](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (download direto). Para instalar o emulador de armazenamento, tem de ter privilégios administrativos no seu computador.

O emulador de armazenamento funciona atualmente apenas no Windows. Se precisar de um emulador de armazenamento para o Linux, uma opção é a comunidade mantida, emulador de armazenamento de código aberto [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Os dados criados numa versão do emulador de armazenamento não são garantidos para serem acessíveis quando se utiliza uma versão diferente. Se precisar de persistir os seus dados a longo prazo, recomendamos que guarde esses dados numa conta de armazenamento Azure, em vez de no emulador de armazenamento.
> 
> O emulador de armazenamento depende de versões específicas das bibliotecas OData. A substituição dos DLLs OData utilizados pelo emulador de armazenamento por outras versões não é suportada e pode causar comportamentos inesperados. No entanto, qualquer versão do OData suportada pelo serviço de armazenamento pode ser utilizada para enviar pedidos ao emulador.

## <a name="how-the-storage-emulator-works"></a>Como funciona o emulador de armazenamento

O emulador de armazenamento utiliza uma instância local do Microsoft SQL Server 2012 Express LocalDB para imitar os serviços de armazenamento Azure. Pode optar por configurar o emulador de armazenamento para aceder a uma instância local do SQL Server em vez da instância LocalDB. Consulte o Início e inicialize a secção [de emulador de armazenamento](#start-and-initialize-the-storage-emulator) mais tarde neste artigo para saber mais.

O emulador de armazenamento liga-se ao SQL Server ou ao LocalDB utilizando a autenticação do Windows.

Existem algumas diferenças de funcionalidade entre o emulador de armazenamento e os serviços de armazenamento Azure. Para obter mais informações sobre estas diferenças, consulte as [diferenças entre o emulador de armazenamento e a secção de armazenamento Azure](#differences-between-the-storage-emulator-and-azure-storage) mais tarde neste artigo.

## <a name="start-and-initialize-the-storage-emulator"></a>Iniciar e rubricar o emulador de armazenamento

Para iniciar o emulador de armazenamento Azure:

1. Selecione o botão **Iniciar** ou prima a tecla **Windows.**
2. Comece a `Azure Storage Emulator` escrever.
3. Selecione o emulador na lista de aplicações apresentadas.

Quando o emulador de armazenamento começar, aparecerá uma janela de aviso de comando. Pode utilizar esta janela da consola para iniciar e parar o emulador de armazenamento. Também pode limpar dados, obter o estado e inicializar o emulador a partir da solicitação de comando. Para obter mais informações, consulte a secção de referência da ferramenta da linha de comando do [emulador de armazenamento](#storage-emulator-command-line-tool-reference) mais tarde neste artigo.

> [!NOTE]
> O emulador de armazenamento Azure pode não começar corretamente se outro emulador de armazenamento, como o Azurite, estiver a funcionar no sistema.

Quando o emulador estiver em execução, verá um ícone na área de notificação da barra de tarefas do Windows.

Quando fechar a janela de pedido de emulador de armazenamento, o emulador de armazenamento continuará a funcionar. Para voltar a colocar a janela da consola do Emulador de Armazenamento, siga os passos anteriores como se estivesse a iniciar o emulador de armazenamento.

A primeira vez que você executar o emulador de armazenamento, o ambiente de armazenamento local é inicializado para você. O processo de inicialização cria uma base de dados no LocalDB e reserva portas HTTP para cada serviço de armazenamento local.

O emulador de armazenamento é instalado por defeito para `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` .

> [!TIP]
> Pode utilizar o [Microsoft Azure Storage Explorer](https://storageexplorer.com) para trabalhar com os recursos do emulador de armazenamento local. Procure "(Emulador - Portas Predefinidas) (Chave)" em "Local & Anexados" na árvore de recursos do Explorador de Armazenamento depois de ter instalado e iniciado o emulador de armazenamento.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicialize o emulador de armazenamento para usar uma base de dados SQL diferente

Pode utilizar a ferramenta de linha de comando do emulador de armazenamento para inicializar o emulador de armazenamento para indicar uma instância de base de dados SQL que não seja a instância localDB padrão:

1. Abra a janela da consola do emulador de armazenamento, conforme descrito no Arranque e inicialize a secção [do emulador de armazenamento.](#start-and-initialize-the-storage-emulator)
1. Na janela da consola, escreva o seguinte comando, onde `<SQLServerInstance>` está o nome da instância SQL Server. Para utilizar o LocalDB, especifique `(localdb)\MSSQLLocalDb` como a instância do SQL Server.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Também pode utilizar o seguinte comando, que direciona o emulador para utilizar a instância padrão do SqL Server:

   `AzureStorageEmulator.exe init /server .`

   Ou, pode utilizar o seguinte comando, que reinitializa a base de dados para a instância localDB padrão:

   `AzureStorageEmulator.exe init /forceCreate`

Para obter mais informações sobre estes comandos, consulte [a referência da ferramenta da linha de comando do emulador de armazenamento](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Pode utilizar o [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para gerir as suas instâncias do SQL Server, incluindo a instalação LocalDB. No diálogo SMSS **Connect to Server,** especifique `(localdb)\MSSQLLocalDb` no nome do **Servidor:** campo para ligar à instância LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autenticação de pedidos contra o emulador de armazenamento

Uma vez instalado e iniciado o emulador de armazenamento, pode testar o seu código contra ele. Todos os pedidos que fizer contra o emulador de armazenamento devem ser autorizados, a menos que seja um pedido anónimo. Pode autorizar pedidos contra o emulador de armazenamento utilizando a autenticação da Chave Partilhada ou com uma assinatura de acesso partilhado (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autorizar com credenciais de chave partilhada

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Para obter mais informações sobre as cordas de ligação, consulte as cordas de [conexão Configure Azure Storage](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autorizar com uma assinatura de acesso partilhado

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Algumas bibliotecas de clientes de armazenamento Azure, como a biblioteca Xamarin, apenas suportam a autenticação com uma assinatura de acesso partilhado (SAS). Pode criar o token SAS utilizando [o Storage Explorer](https://storageexplorer.com/) ou outra aplicação que suporte a autenticação da Chave Partilhada.

Também pode gerar um token SAS utilizando a Azure PowerShell. O exemplo a seguir gera um token SAS com permissões completas para um recipiente blob:

1. Instale a Azure PowerShell se ainda não o fez (recomenda-se a utilização da versão mais recente dos cmdlets Azure PowerShell). Para obter instruções de instalação, consulte [instalar e configurar a Azure PowerShell](/powershell/azure/install-Az-ps).
2. Abra o Azure PowerShell e execute os seguintes comandos, `CONTAINER_NAME` substituindo-os por um nome à sua escolha:

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

Para obter mais informações sobre assinaturas de acesso partilhado, consulte [Grant acesso limitado aos recursos de Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Endereçar recursos no emulador de armazenamento

Os pontos finais de serviço para o emulador de armazenamento são diferentes dos pontos finais de uma conta de armazenamento Azure. O computador local não faz a resolução do nome de domínio, exigindo que os pontos finais do emulador de armazenamento sejam endereços locais.

Quando se trata de um recurso numa conta de armazenamento Azure, utiliza o seguinte esquema. O nome da conta faz parte do nome do hospedeiro URI, e o recurso a ser endereçado faz parte do caminho URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Por exemplo, o seguinte URI é um endereço válido para uma bolha numa conta de armazenamento Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Como o computador local não faz a resolução do nome de domínio, o nome da conta faz parte do caminho URI em vez do nome de anfitrião. Utilize o seguinte formato URI para um recurso no emulador de armazenamento:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Por exemplo, o seguinte endereço pode ser utilizado para aceder a uma bolha no emulador de armazenamento:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Os pontos finais de serviço para o emulador de armazenamento são:

* Serviço blob:`http://127.0.0.1:10000/<account-name>/<resource-path>`
* Serviço de fila:`http://127.0.0.1:10001/<account-name>/<resource-path>`
* Serviço de mesa:`http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Endereçar a conta secundária com RA-GRS

Começando pela versão 3.1, o emulador de armazenamento suporta a replicação geo-redundante de acesso à leitura (RA-GRS). Pode aceder à localização secundária através da anexação -secundária ao nome da conta. Por exemplo, o seguinte endereço pode ser utilizado para aceder a uma bolha utilizando o secundário apenas de leitura no emulador de armazenamento:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Para acesso programático ao secundário com o emulador de armazenamento, utilize a Biblioteca do Cliente de Armazenamento para a versão .NET 3.2 ou posterior. Consulte a Biblioteca do [Cliente de Armazenamento microsoft Azure para obter](https://msdn.microsoft.com/library/azure/dn261237.aspx) mais informações.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Referência da ferramenta de linha de comando do emulador de armazenamento

A partir da versão 3.0, é apresentada uma janela da consola quando inicia o Emulador de Armazenamento. Utilize a linha de comando na janela da consola para iniciar e parar o emulador. Também pode consultar o estado e fazer outras operações a partir da linha de comando.

> [!NOTE]
> Se tiver o emulador de computação Microsoft Azure instalado, aparece um ícone de bandeja de sistema quando lança o Emulador de Armazenamento. Clique com o botão direito no ícone para revelar um menu que fornece uma forma gráfica de iniciar e parar o Emulador de Armazenamento.
>
>

### <a name="command-line-syntax"></a>Sintaxe da linha de comandos

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opções

Para ver a lista de opções, escreva `/help` na linha de comandos.

| Opção | Descrição | Comando | Argumentos |
| --- | --- | --- | --- |
| **Iniciar** |Começa o emulador de armazenamento. |`AzureStorageEmulator.exe start [-inprocess]` |*-Reprocessamento:* Inicie o emulador no processo atual em vez de criar um novo processo. |
| **Parar** |Para o emulador de armazenamento. |`AzureStorageEmulator.exe stop` | |
| **Estado** |Imprime o estado do emulador de armazenamento. |`AzureStorageEmulator.exe status` | |
| **Limpar** |Limpa os dados em todos os serviços especificados na linha de comando. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*: Limpa os dados da bolha. <br/>*fila*: Limpa os dados da fila. <br/>*tabela*: Limpa os dados da tabela. <br/>*tudo:* Limpa todos os dados em todos os serviços. |
| **Init** |Faz uma in inicialização única para configurar o emulador. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-servidor servidorName\instância Nome*: Especifica o servidor que hospeda a instância SQL. <br/>*-sqlinstance instanceName*: Especifica o nome da instância SQL a ser usada na instância do servidor predefinido. <br/>*-forcecreate*: Força a criação da base de dados SQL, mesmo que já exista. <br/>*-skipcreate*: Ignora a criação da base de dados SQL. Isto tem precedência sobre -forcecreate.<br/>*-reservas:* Tentativas de reserva das portas HTTP associadas aos serviços.<br/>*-sem reservas*: Tentativas de remover reservas para as portas HTTP associadas aos serviços. Isto tem precedência sobre os portos de reserva.<br/>*-inprocesso*: Executa a inicialização no processo atual em vez de desovar um novo processo. O processo atual deve ser lançado com permissões elevadas se alterar as reservas portuárias. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Diferenças entre o emulador de armazenamento e o armazenamento Azure

Como o emulador de armazenamento é um ambiente emulado local, existem diferenças entre a utilização do emulador e uma conta de armazenamento Azure na nuvem:

* O emulador de armazenamento suporta apenas uma única conta fixa e uma chave de autenticação bem conhecida.
* O emulador de armazenamento não é um serviço de armazenamento escalável e não suporta um grande número de clientes simultâneos.
* Conforme descrito no [Endereço de Recursos no emulador de armazenamento, os](#addressing-resources-in-the-storage-emulator)recursos são tratados de forma diferente no emulador de armazenamento versus uma conta de armazenamento Azure. A diferença é porque a resolução do nome de domínio está disponível na nuvem, mas não no computador local.
* Começando pela versão 3.1, a conta do emulador de armazenamento suporta a replicação geo-redundante de acesso à leitura (RA-GRS). No emulador, todas as contas têm RA-GRS ativado e nunca há qualquer desfasamento entre as réplicas primárias e secundárias. As operações Get Blob Service Stats, Get Queue Service Stats e Get Table Service Stats são suportadas na conta secundária e devolvem sempre o valor do elemento de `LastSyncTime` resposta como o tempo atual de acordo com a base de dados SQL subjacente.
* O serviço de ficheiros e os pontos finais do serviço de protocolo SMB não são suportados atualmente no emulador de armazenamento.
* Se utilizar uma versão dos serviços de armazenamento que não seja suportada pelo emulador, o emulador devolve um erro doByEmulator (código de estado HTTP 400 - Mau Pedido).

### <a name="differences-for-blob-storage"></a>Diferenças para o armazenamento blob

As seguintes diferenças aplicam-se ao armazenamento blob no emulador:

* O emulador de armazenamento só suporta tamanhos de bolhas até 2 GB.
* O comprimento máximo de um nome blob no emulador de armazenamento é de 256 caracteres, enquanto o comprimento máximo de um nome blob no Azure Storage é de 1024 caracteres.
* A cópia incremental permite que as imagens de bolhas substituídas sejam copiadas, o que devolve uma falha no serviço.
* Obter Gamas de Página O Diff não funciona entre instantâneos copiados usando o Incho de Cópia Incremental.
* Uma operação Put Blob pode ter sucesso contra uma bolha que existe no emulador de armazenamento com um arrendamento ativo, mesmo que o ID do arrendamento não tenha sido especificado no pedido.
* As operações do Apêndio Blob não são suportadas pelo emulador. Tentar uma operação num apêndice blob devolve um erro do FeatureNotSupportedByEmulator (código de estado HTTP 400 - Mau Pedido).

### <a name="differences-for-table-storage"></a>Diferenças para o armazenamento de mesa

As seguintes diferenças aplicam-se ao armazenamento de mesa no emulador:

* Data propriedades no serviço Tabela no suporte do emulador de armazenamento apenas a gama suportada pelo SQL Server 2005 (são necessárias para ser mais tarde que 1 de janeiro de 1753). Todas as datas antes de 1 de janeiro de 1753 são alteradas para este valor. A precisão das datas limita-se à precisão do SQL Server 2005, o que significa que as datas são precisas para 1/300 de segundo.
* O emulador de armazenamento suporta valores de propriedade chave de partição e linha inferior a 512 bytes cada. O tamanho total do nome da conta, nome de mesa e nomes de propriedade principais juntos não pode exceder 900 bytes.
* O tamanho total de uma linha numa mesa no emulador de armazenamento está limitado a menos de 1 MB.
* No emulador de armazenamento, as propriedades do tipo de dados `Edm.Guid` ou `Edm.Binary` suportam apenas os `Equal (eq)` operadores e `NotEqual (ne)` de comparação em cadeias de filtro de consulta.

### <a name="differences-for-queue-storage"></a>Diferenças para o armazenamento da fila

Não existem diferenças específicas para o armazenamento da fila no emulador.

## <a name="storage-emulator-release-notes"></a>Notas de libertação de emuladores de armazenamento

### <a name="version-510"></a>Versão 5.10

* O emulador de armazenamento não rejeitará a versão 2019-07-07 dos serviços de armazenamento nos pontos finais do serviço Blob, Queue e Table.

### <a name="version-59"></a>Versão 5.9

* O emulador de armazenamento não rejeitará a versão 2019-02-02 dos serviços de armazenamento nos pontos finais do serviço Blob, Queue e Table.

### <a name="version-58"></a>Versão 5.8

* O emulador de armazenamento não rejeitará a versão 2018-11-09 dos serviços de armazenamento nos pontos finais do serviço Blob, Queue e Table.

### <a name="version-57"></a>Versão 5.7

* Corrigiu um erro que causaria uma falha se o registo de madeira estivesse ativado.

### <a name="version-56"></a>Versão 5.6

* O emulador de armazenamento suporta agora a versão 2018-03-28 dos serviços de armazenamento nos pontos finais do serviço Blob, Queue e Table.

### <a name="version-55"></a>Versão 5.5

* O emulador de armazenamento suporta agora a versão 2017-11-09 dos serviços de armazenamento nos pontos finais blob, queue e table.
* Foi adicionado suporte para a propriedade blob **Created,** que devolve o tempo de criação do blob.

### <a name="version-54"></a>Versão 5.4

* Para melhorar a estabilidade da instalação, o emulador já não tenta reservar portas no momento de instalação. Se quiser reservas portuárias, utilize a opção de reserva do comando **init** para especibilizá-las. *-reserveports*

### <a name="version-53"></a>Versão 5.3

* O emulador de armazenamento suporta agora a versão 2017-07-29 dos serviços de armazenamento nos pontos finais blob, queue e table.

### <a name="version-52"></a>Versão 5.2

* O emulador de armazenamento suporta agora a versão 2017-04-17 dos serviços de armazenamento nos pontos finais blob, queue e table.
* Corrigiu um bug onde os valores de propriedade de mesa não estavam a ser devidamente codificados.

### <a name="version-51"></a>Versão 5.1

* Corrigiu um erro onde o emulador de armazenamento estava a devolver o `DataServiceVersion` cabeçalho em algumas respostas onde o serviço não estava.

### <a name="version-50"></a>Versão 5.0

* O instalador de emulador de armazenamento já não verifica as instalações existentes do MSSQL e do Quadro .NET.
* O instalador de emulador de armazenamento já não cria a base de dados como parte da instalação. A base de dados ainda será criada se necessário como parte do arranque.
* A criação de bases de dados já não requer elevação.
* As reservas portuárias já não são necessárias para o arranque.
* Acrescenta as seguintes opções `init` a: `-reserveports` (requer elevação), `-unreserveports` (requer elevação), `-skipcreate` .
* A opção UI do emulador de armazenamento no ícone do tabuleiro do sistema lança agora a interface da linha de comando. O antigo GUI já não está disponível.
* Alguns DLLs foram removidos ou renomeados.

### <a name="version-46"></a>Versão 4.6

* O emulador de armazenamento suporta agora a versão 2016-05-31 dos serviços de armazenamento nos pontos finais blob, queue e table.

### <a name="version-45"></a>Versão 4.5

* Corrigiu um erro que fez com que a instalação e a inicialização falhassem quando a base de dados de suporte é renomeada.

### <a name="version-44"></a>Versão 4.4

* O emulador de armazenamento suporta agora a versão 2015-12-11 dos serviços de armazenamento nos pontos finais blob, queue e table.
* A recolha de dados de lixo do emulador de armazenamento é agora mais eficiente quando se lida com um grande número de bolhas.
* Corrigiu um erro que fez com que o contentor ACL XML fosse validado de forma ligeiramente diferente da forma como o serviço de armazenamento o faz.
* Corrigiu um erro que, por vezes, fez com que os valores máximo e min DateTime fossem reportados no fuso horário incorreto.

### <a name="version-43"></a>Versão 4.3

* O emulador de armazenamento suporta agora a versão 2015-07-08 dos serviços de armazenamento nos pontos finais blob, queue e table.

### <a name="version-42"></a>Versão 4.2

* O emulador de armazenamento suporta agora a versão 2015-04-05 dos serviços de armazenamento nos pontos finais blob, queue e table.

### <a name="version-41"></a>Versão 4.1

* O emulador de armazenamento suporta agora a versão 2015-02-21 dos serviços de armazenamento nos pontos finais blob, queue e table. Não suporta as novas funcionalidades da Append Blob.
* O emulador devolve agora uma mensagem de erro significativa para versões não suportadas de serviços de armazenamento. Recomendamos a utilização da versão mais recente do emulador. Se tiver um erro do VersionNotSupportedByEmulator (código de estado HTTP 400 - Mau Pedido), descarregue a versão mais recente do emulador.
* Corrigiu um erro em que uma condição de regata fez com que os dados da entidade da tabela fossem incorretos durante operações de fusão simultânea.

### <a name="version-40"></a>Versão 4.0

* O emulador de armazenamento executável foi renomeado para *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versão 3.2

* O emulador de armazenamento suporta agora a versão 2014-02-14 dos serviços de armazenamento nos pontos finais blob, queue e table. Os pontos finais do serviço de ficheiros não são suportados atualmente no emulador de armazenamento. Consulte [a versão para os Serviços de Armazenamento Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) para obter mais detalhes sobre a versão 2014-02-14.

### <a name="version-31"></a>Versão 3.1

* O armazenamento geo-redundante de acesso à leitura (RA-GRS) é agora suportado no emulador de armazenamento. Os `Get Blob Service Stats` `Get Queue Service Stats` , e `Get Table Service Stats` APIs são suportados para a conta secundária e sempre devolverão o valor do elemento de resposta LastSyncTime como o tempo atual de acordo com a base de dados SQL subjacente. Para acesso programático ao secundário com o emulador de armazenamento, utilize a Biblioteca do Cliente de Armazenamento para a versão .NET 3.2 ou posterior. Consulte a Biblioteca do Cliente de Armazenamento microsoft Azure para obter referências .NET para obter mais informações.

### <a name="version-30"></a>Versão 3.0

* O emulador de armazenamento Azure já não é enviado no mesmo pacote que o emulador computacional.
* A interface gráfica do utilizador do emulador de armazenamento está depreciada. Foi substituído por uma interface de linha de comando scriptável. Para obter mais informações sobre a interface da linha de comando, consulte a referência da ferramenta da linha de comando do emulador de armazenamento. A interface gráfica continuará presente na versão 3.0, mas só pode ser acedida quando o Emulador Compute estiver instalado clicando à direita no ícone da bandeja do sistema e selecionando a UI do Emulator de Armazenamento de Espetáculos.
* A versão 2013-08-15 dos serviços de armazenamento Azure está agora totalmente suportada. (Anteriormente, esta versão só foi suportada pela versão 2.2.1 preview do Emulador de Armazenamento.

## <a name="next-steps"></a>Passos seguintes

* Avaliar o emulador de armazenamento de código aberto, mantido pela [comunidade, Azurite.](https://github.com/azure/azurite) 
* [As amostras de armazenamento Azure que utilizam .NET](../storage-samples-dotnet.md) contêm ligações a várias amostras de código que pode utilizar ao desenvolver a sua aplicação.
* Pode utilizar o [Microsoft Azure Storage Explorer](https://storageexplorer.com) para trabalhar com recursos na sua conta de Armazenamento em nuvem e no emulador de armazenamento.
