---
title: Trabalhando com erros transitórios – banco de dados SQL do Azure
description: Saiba como solucionar problemas, diagnosticar e impedir um erro de conexão SQL ou um erro transitório no banco de dados SQL do Azure.
keywords: conexão SQL, Cadeia de conexão, problemas de conectividade, erro transitório, erro de conexão
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 06/14/2019
ms.openlocfilehash: 0191506cab9a54ad3978bfa7387c9ba1112ae815
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690817"
---
# <a name="working-with-sql-database-connection-issues-and-transient-errors"></a>Trabalhando com problemas de conexão do banco de dados SQL e erros transitórios

Este artigo descreve como evitar, solucionar problemas, diagnosticar e atenuar erros de conexão e erros transitórios que o aplicativo cliente encontra quando interage com o banco de dados SQL do Azure. Saiba como configurar a lógica de repetição, criar a cadeia de conexão e ajustar outras configurações de conexão.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Erros transitórios (falhas transitórias)

Um erro transitório, também conhecido como uma falha transitória, tem uma causa subjacente que é resolvida em breve. Uma causa ocasional de erros transitórios é quando o sistema do Azure muda rapidamente os recursos de hardware para melhor balancear a carga de várias cargas de trabalho. A maioria desses eventos de reconfiguração termina em menos de 60 segundos. Durante esse período de reconfiguração, você pode ter problemas de conectividade com o banco de dados SQL. Os aplicativos que se conectam ao banco de dados SQL devem ser criados para esperar esses erros transitórios. Para tratá-los, implemente a lógica de repetição em seu código em vez de identificando-los a usuários como erros de aplicativo.

Se seu programa cliente usar ADO.NET, seu programa será informado sobre o erro transitório pelo lançamento de **SqlException**. Compare a propriedade **Number** com a lista de erros transitórios encontrados na parte superior do artigo [códigos de erro do SQL para aplicativos cliente do banco de dados SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Conexão versus comando

Repita a conexão SQL ou estabeleça-a novamente, dependendo do seguinte:

- **Um erro transitório ocorre durante uma tentativa de conexão**

Após um atraso de vários segundos, repita a conexão.

- **Um erro transitório ocorre durante um comando de consulta SQL**

Não repita o comando imediatamente. Em vez disso, após um atraso, estabeleça a conexão de novo. Em seguida, repita o comando.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Repita a lógica nos erros transitórios

Os programas cliente que ocasionalmente encontram um erro transitório são mais robustos quando contêm lógica de repetição. Quando o programa se comunica com o banco de dados SQL por meio de middleware de terceiros, pergunte ao fornecedor se o middleware contém lógica de repetição para erros transitórios.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Princípios para repetição

- Se o erro for transitório, tente abrir uma conexão novamente.
- Não repita diretamente uma instrução SQL `SELECT` que falhou com um erro transitório. Em vez disso, estabeleça uma nova conexão e tente novamente a `SELECT`.
- Quando uma instrução SQL `UPDATE` falhar com um erro transitório, estabeleça uma nova conexão antes de tentar novamente a atualização. A lógica de repetição deve garantir que toda a transação do banco de dados seja concluída ou que toda a transação seja revertida.

### <a name="other-considerations-for-retry"></a>Outras considerações para repetição

- Um programa em lotes que é iniciado automaticamente após as horas de trabalho e termina antes que a manhã possa ser muito paciente com intervalos de tempo longos entre suas tentativas de repetição.
- Um programa de interface do usuário deve considerar a tendência humana de desistir após uma espera muito longa. A solução não deve tentar novamente a cada segundos, pois essa política pode inundar o sistema com solicitações.

### <a name="interval-increase-between-retries"></a>Aumento de intervalo entre novas tentativas

Recomendamos que você aguarde 5 segundos antes de sua primeira tentativa. Repetir após um atraso menor que 5 segundos riscos sobrecarregam o serviço de nuvem. Para cada nova tentativa subsequente, o atraso deve aumentar exponencialmente, até um máximo de 60 segundos.

Para obter uma discussão sobre o período de bloqueio para clientes que usam o ADO.NET, consulte [SQL Server pooling de conexão (ADO.net)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

Você também pode querer definir um número máximo de tentativas antes que o programa seja encerrado automaticamente.

### <a name="code-samples-with-retry-logic"></a>Exemplos de código com lógica de repetição

Exemplos de código com lógica de repetição estão disponíveis em:

- [Conectar-se de forma resiliente ao SQL com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Conectar-se de forma resiliente ao SQL com PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testar sua lógica de repetição

Para testar sua lógica de repetição, você deve simular ou causar um erro que pode ser corrigido enquanto o programa ainda está em execução.

#### <a name="test-by-disconnecting-from-the-network"></a>Testar desconectando da rede

Uma maneira de testar sua lógica de repetição é desconectar o computador cliente da rede enquanto o programa está em execução. O erro é:

- **SqlException. Number** = 11001
- Mensagem: "nenhum host desse tipo é conhecido"

Como parte da primeira tentativa de repetição, você pode reconectar o computador cliente à rede e, em seguida, tentar se conectar.

Para tornar esse teste prático, desconecte o computador da rede antes de iniciar o programa. Em seguida, o programa reconhece um parâmetro de tempo de execução que faz com que o programa:

- Adicione temporariamente 11001 à sua lista de erros a serem considerados como transitórios.
- Tente sua primeira conexão como de costume.
- Depois que o erro for detectado, remova 11001 da lista.
- Exibe uma mensagem que informa ao usuário para conectar o computador à rede.
- Pause a execução adicional usando o método **console. ReadLine** ou uma caixa de diálogo com um botão OK. O usuário pressiona a tecla ENTER depois que o computador é conectado à rede.
- Tente se conectar novamente, esperando êxito.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Teste digitando incorretamente o nome do banco de dados ao se conectar

Seu programa pode intencionalmente errar o nome de usuário antes da primeira tentativa de conexão. O erro é:

- **SqlException. Number** = 18456
- Mensagem: "falha de logon para o usuário ' WRONG_MyUserName '."

Como parte da primeira tentativa de repetição, o programa pode corrigir o erro de ortografia e, em seguida, tentar se conectar.

Para tornar esse teste prático, seu programa reconhece um parâmetro de tempo de execução que faz com que o programa:

- Adicione temporariamente 18456 à sua lista de erros a serem considerados como transitórios.
- Adicione intencionalmente ' WRONG_ ' ao nome de usuário.
- Depois que o erro for detectado, remova 18456 da lista.
- Remova ' WRONG_ ' do nome de usuário.
- Tente se conectar novamente, esperando êxito.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parâmetros de SqlConnection do .NET para repetição de conexão

Se o programa cliente se conectar ao banco de dados SQL usando o .NET Framework classe **System. Data. SqlClient. SqlConnection**, use o .NET 4.6.1 ou posterior (ou o .NET Core) para que você possa usar seu recurso de repetição de conexão. Para obter mais informações sobre o recurso, consulte [esta página da Web](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Ao criar a [cadeia de conexão](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) para o objeto **SqlConnection** , coordene os valores entre os seguintes parâmetros:

- **ConnectRetryCount**:&nbsp;&nbsp;o padrão é 1. O intervalo é de 0 a 255.
- **ConnectRetryInterval**:&nbsp;&nbsp;o padrão é 10 segundos. O intervalo é de 1 a 60.
- **Tempo limite de conexão**:&nbsp;&nbsp;o padrão é 15 segundos. O intervalo é de 0 a 2147483647.

Especificamente, os valores escolhidos devem fazer com que a seguinte igualdade seja verdadeira: tempo limite da conexão = ConnectRetryCount * ConnectionRetryInterval

Por exemplo, se a contagem for igual a 3 e o intervalo for igual a 10 segundos, um tempo limite de apenas 29 segundos não dará ao sistema tempo suficiente para sua terceira e última tentativa de conexão: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Conexão versus comando

Os parâmetros **ConnectRetryCount** e **ConnectRetryInterval** permitem que o objeto **SqlConnection** repita a operação de conexão sem informar ou incomodar seu programa, como retornar o controle ao seu programa. As repetições podem ocorrer nas seguintes situações:

- chamada do método mysqlconnection. Open
- chamada do método mysqlconnection. Execute

Há uma sutileza. Se ocorrer um erro transitório enquanto a *consulta* estiver sendo executada, o objeto **SqlConnection** não repetirá a operação de conexão. Certamente, não repete a consulta. No entanto, **SqlConnection** verifica rapidamente a conexão antes de enviar sua consulta para execução. Se a verificação rápida detectar um problema de conexão, **SqlConnection** tentará a operação de conexão novamente. Se a nova tentativa for realizada com sucesso, sua consulta será enviada para execução.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount deve ser combinado com a lógica de repetição do aplicativo

Suponha que seu aplicativo tenha uma lógica de repetição personalizada robusta. Ele pode repetir a operação de conexão quatro vezes. Se você adicionar **ConnectRetryInterval** e **ConnectRetryCount** = 3 à sua cadeia de conexão, você aumentará a contagem de repetições para 4 * 3 = 12 repetições. Talvez você não pretenda um número tão alto de tentativas.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Conexões com o banco de dados SQL

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Conexão: cadeia de conexão

A cadeia de conexão necessária para se conectar ao banco de dados SQL é um pouco diferente da cadeia de caracteres usada para se conectar ao SQL Server. Você pode copiar a cadeia de conexão do seu banco de dados do [portal do Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Conexão: endereço IP

Você deve configurar o servidor de banco de dados SQL para aceitar a comunicação do endereço IP do computador que hospeda o programa cliente. Para definir essa configuração, edite as configurações de firewall por meio do [portal do Azure](https://portal.azure.com/).

Se você se esquecer de configurar o endereço IP, o programa falhará com uma mensagem de erro útil que indica o endereço IP necessário.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Para obter mais informações, consulte [definir configurações de firewall no banco de dados SQL](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Conexão: portas

Normalmente, você precisa garantir que apenas a porta 1433 esteja aberta para comunicação de saída no computador que hospeda o programa cliente.

Por exemplo, quando o programa cliente está hospedado em um computador com Windows, você pode usar o Firewall do Windows no host para abrir a porta 1433.

1. Abra o painel de controle.
2. Selecione **todos os itens do painel de controle** > **Firewall do Windows** > **Configurações avançadas** > **regras de saída** > **ações** > **nova regra**.

Se o seu programa cliente estiver hospedado em uma VM (máquina virtual) do Azure, leia [portas além de 1433 para ADO.NET 4,5 e banco de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

Para obter informações básicas sobre a configuração de portas e endereços IP, consulte [Firewall do banco de dados SQL do Azure](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Conexão: ADO.NET 4.6.2 ou posterior

Se o seu programa usar classes ADO.NET como **System. Data. SqlClient. SqlConnection** para se conectar ao banco de dados SQL, recomendamos que você use .NET Framework versão 4.6.2 ou posterior.

#### <a name="starting-with-adonet-462"></a>Começando com ADO.NET 4.6.2

- A conexão aberta tenta ser repetida imediatamente para bancos de dados SQL do Azure, melhorando assim o desempenho de aplicativos habilitados para nuvem.

#### <a name="starting-with-adonet-461"></a>Começando com ADO.NET 4.6.1

- Para o banco de dados SQL, a confiabilidade é aprimorada quando você abre uma conexão usando o método **SqlConnection. Open** . O método **Open** agora incorpora mecanismos de repetição de melhor esforço em resposta a falhas transitórias para determinados erros dentro do período de tempo limite da conexão.
- Há suporte para o pooling de conexões, que inclui uma verificação eficiente de que o objeto de conexão que o programa dá está funcionando.

Quando você usa um objeto de conexão de um pool de conexões, recomendamos que o programa feche temporariamente a conexão quando ela não estiver em uso imediatamente. Não é caro reabrir uma conexão, mas é criar uma nova conexão.

Se você usar o ADO.NET 4,0 ou anterior, recomendamos que atualize para o ADO.NET mais recente. A partir de agosto de 2018, você pode [baixar o ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnóstico

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnóstico: testar se os utilitários podem se conectar

Se o seu programa não conseguir se conectar ao banco de dados SQL, uma opção de diagnóstico será tentar se conectar a um programa utilitário. O ideal é que o utilitário se conecte usando a mesma biblioteca que o programa usa.

Em qualquer computador com Windows, você pode experimentar estes utilitários:

- SQL Server Management Studio (SSMS. exe), que se conecta usando ADO.NET
- `sqlcmd.exe`, que se conecta usando o [ODBC](https://msdn.microsoft.com/library/jj730308.aspx)

Depois que o programa estiver conectado, teste se uma consulta SQL SELECT curta funciona.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnóstico: verificar as portas abertas

Se você suspeitar que as tentativas de conexão falham devido a problemas de porta, você pode executar um utilitário em seu computador que relata as configurações de porta.

No Linux, os seguintes utilitários podem ser úteis:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: altere o valor de exemplo para que seja seu endereço IP.

No Windows, o utilitário [Portqry. exe](https://www.microsoft.com/download/details.aspx?id=17148) pode ser útil. Aqui está um exemplo de execução que consultou a situação de porta em um servidor de banco de dados SQL e que foi executado em um computador laptop:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnóstico: registrar seus erros em log

Um problema intermitente, às vezes, é mais bem diagnosticado pela detecção de um padrão geral em dias ou semanas.

O cliente pode auxiliar em um diagnóstico registrando todos os erros encontrados. Você pode correlacionar as entradas de log com dados de erro que o banco de dados SQL registra em log internamente.

A Enterprise Library 6 (EntLib60) oferece classes gerenciadas do .NET para auxiliar no registro em log. Para obter mais informações, consulte [5-tão fácil como cair em um log: Use o Logging Application Block](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnóstico: examine os logs do sistema em busca de erros

Aqui estão algumas instruções SELECT do Transact-SQL que consultam logs de erros e outras informações.

| Consulta de log | Descrição |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |A exibição [Sys. event_log](https://msdn.microsoft.com/library/dn270018.aspx) oferece informações sobre eventos individuais, que incluem alguns que podem causar erros transitórios ou falhas de conectividade.<br/><br/>Idealmente, você pode correlacionar os valores de **start_time** ou **end_time** com informações sobre quando o programa cliente enfrentou problemas.<br/><br/>Você deve se conectar ao banco de dados *mestre* para executar esta consulta. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |A exibição [Sys. database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) oferece contagens agregadas de tipos de evento para diagnósticos adicionais.<br/><br/>Você deve se conectar ao banco de dados *mestre* para executar esta consulta. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnóstico: Pesquisar eventos de problema no log do banco de dados SQL

Você pode pesquisar entradas sobre eventos de problema no log do banco de dados SQL. Experimente a seguinte instrução Transact-SQL SELECT no banco de dados *mestre* :

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Algumas linhas retornadas de sys. fn_xe_telemetry_blob_target_read_file

O exemplo a seguir mostra como pode ser a aparência de uma linha retornada. Os valores nulos mostrados geralmente não são nulos em outras linhas.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6

A Enterprise Library 6 (EntLib60) é uma estrutura de classes .NET que ajuda a implementar clientes robustos de serviços de nuvem, um dos quais é o serviço de banco de dados SQL. Para localizar tópicos dedicados a cada área na qual o EntLib60 pode auxiliar, consulte [Enterprise Library 6 – abril de 2013](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

A lógica de repetição para lidar com erros transitórios é uma área na qual o EntLib60 pode auxiliar. Para obter mais informações, consulte [4-perseverity, Secret de todos os triunfos: Use o bloco de aplicativo de tratamento de falhas transitórias](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> O código-fonte para EntLib60 está disponível para download público no [centro de download](https://go.microsoft.com/fwlink/p/?LinkID=290898). A Microsoft não tem planos de fazer mais atualizações de manutenção ou de recursos para o EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Classes EntLib60 para erros transitórios e repetição

As classes EntLib60 a seguir são particularmente úteis para a lógica de repetição. Todas essas classes são encontradas no ou no namespace **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling**.

No namespace **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling**:

- Classe **RetryPolicy**
  - Método **ExecuteAction**
- Classe **ExponentialBackoff**
- Classe **SqlDatabaseTransientErrorDetectionStrategy**
- Classe **ReliableSqlConnection**
  - Método **ExecuteCommand**

No namespace **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling. TestSupport**:

- Classe **AlwaysTransientErrorDetectionStrategy**
- Classe **NeverTransientErrorDetectionStrategy**

Aqui estão alguns links para informações sobre EntLib60:

- Download gratuito do livro: [Guia do desenvolvedor para Microsoft Enterprise Library, 2ª edição](https://www.microsoft.com/download/details.aspx?id=41145).
- Práticas recomendadas: as [diretrizes gerais de repetição](../best-practices-retry-general.md) têm uma excelente discussão aprofundada sobre a lógica de repetição.
- Download do NuGet: [Enterprise Library-falha transitória ao manipular o bloco de aplicativos 6,0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: o bloco de log

- O bloqueio de log é uma solução altamente flexível e configurável que você pode usar para:
  - Crie e armazene mensagens de log em uma ampla variedade de locais.
  - Categorizar e filtrar mensagens.
  - Coletar informações contextuais que são úteis para depuração e rastreamento, bem como para requisitos de auditoria e de log geral.
- O bloqueio de log abstrai a funcionalidade de log do destino de log para que o código do aplicativo seja consistente, independentemente do local e do tipo de armazenamento de log de destino.

Para obter mais informações, consulte [5-tão fácil como cair em um log: Use o Logging Application Block](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Código-fonte do método istransitória EntLib60

Em seguida, na classe **SqlDatabaseTransientErrorDetectionStrategy** , é o C# código-fonte do método **istransitória** . O código-fonte esclarece quais erros foram considerados transitórios e digno de repetição, a partir de abril de 2013.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre como solucionar outros problemas comuns de conexão de banco de dados SQL, consulte [solucionar problemas de conexão com o banco de dados SQL do Azure](sql-database-troubleshoot-common-connection-issues.md).
- [Bibliotecas de conexão para o banco de dados SQL e SQL Server](sql-database-libraries.md)
- [ADO.NET (pooling de conexão de SQL Server)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [ *Tentar* novamente é uma biblioteca de repetição de uso geral licenciada do Apache 2,0, escrita em Python,](https://pypi.python.org/pypi/retrying) para simplificar a tarefa de adicionar comportamento de nova tentativa a praticamente qualquer coisa.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
