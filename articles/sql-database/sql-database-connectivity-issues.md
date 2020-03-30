---
title: Trabalhar com erros transitórios
description: Aprenda a resolver problemas, diagnosticar e evitar um erro de ligação SQL ou um erro transitório na Base de Dados Azure SQL.
keywords: conexão sql,cadeia de ligação,problemas de conectividade,erro transitório,erro de ligação
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: ninarn
ms.reviewer: carlrab, vanto
ms.date: 01/14/2020
ms.openlocfilehash: d2b56e259f551f7655936c975a7a864a27a1df79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269084"
---
# <a name="troubleshooting-transient-connection-errors-to-sql-database"></a>Erros de ligação transitórios de resolução de problemas à Base de Dados SQL

Este artigo descreve como prevenir, resolver problemas, diagnosticar e mitigar erros de ligação e erros transitórios que a sua aplicação de cliente encontra quando interage com a Base de Dados Azure SQL. Aprenda a configurar a lógica de retry, construa a cadeia de ligação e ajuste outras definições de ligação.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Erros transitórios (falhas transitórias)

Um erro transitório, também conhecido como uma falha transitória, tem uma causa subjacente que logo se resolve. Uma causa ocasional de erros transitórios é quando o sistema Azure rapidamente desloca os recursos de hardware para melhor equilibrar a carga de várias cargas de trabalho. A maioria destes eventos de reconfiguração termina em menos de 60 segundos. Durante este período de tempo de reconfiguração, poderá ter problemas de conectividade com a Base de Dados SQL. As aplicações que se ligam à Base de Dados SQL devem ser construídas para esperar estes erros transitórios. Para os manipular, implemente a lógica de retry no seu código em vez de os ver perante os utilizadores como erros de aplicação.

Se o seu programa de cliente utilizar ADO.NET, o seu programa é informado sobre o erro transitório pelo lançamento da **SqlException**. 

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Ligação vs comando

Volte a tentar a ligação SQL ou estabeleça-a novamente, dependendo do seguinte:

- **Um erro transitório ocorre durante uma tentativa de ligação**

Depois de um atraso de vários segundos, tente novamente a ligação.

- **Um erro transitório ocorre durante um comando de consulta SQL**

Não tente imediatamente o comando. Em vez disso, depois de um atraso, estabelece a ligação recentemente. Em seguida, tente novamente o comando.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Repita a lógica nos erros transitórios

Os programas de clientes que ocasionalmente encontram um erro transitório são mais robustos quando contêm lógica de retry. Quando o seu programa comunica com a Base de Dados SQL através de middleware de terceiros, pergunte ao fornecedor se o middleware contém lógica de retry para erros transitórios.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Princípios para a retentativa

- Se o erro for transitório, tente novamente a abertura de uma ligação.
- Não tente diretamente uma declaração `SELECT` SQL que falhou com um erro transitório. Em vez disso, estabeleça uma nova `SELECT`ligação e, em seguida, tente novamente o .
- Quando uma declaração `UPDATE` SQL falhar com um erro transitório, estabeleça uma nova ligação antes de voltar a tentar a Atualização. A lógica de retry deve garantir que toda a transação de base de dados terminada ou que toda a transação seja reposta.

### <a name="other-considerations-for-retry"></a>Outras considerações para a retentativa

- Um programa de lote que começa automaticamente após o horário de trabalho e termina antes da manhã pode dar-se ao luxo de ser muito paciente com intervalos de tempo longos entre as suas tentativas de repetição.
- Um programa de interface de utilizador deve explicar a tendência humana para desistir após uma espera demasiado longa. A solução não deve voltar a tentar de vez em quando, porque essa política pode inundar o sistema com pedidos.

### <a name="interval-increase-between-retries"></a>Aumento de intervalo entre tentativas

Recomendamos que espere 5 segundos antes da primeira tentativa. Tentar depois de um atraso de menos de 5 segundos corre o risco de sobrecarregar o serviço de nuvem. Para cada retentativa subsequente, o atraso deve crescer exponencialmente, até um máximo de 60 segundos.

Para uma discussão sobre o período de bloqueio para clientes que usam ADO.NET, consulte o [pool de ligação sQL Server (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

Também pode querer definir um número máximo de repetições antes do programa terminar.

### <a name="code-samples-with-retry-logic"></a>Amostras de código com lógica de retry

Exemplos de código com lógica de retry estão disponíveis em:

- [Ligue-se resilientemente ao SQL com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Ligue-se resilientemente à SQL com PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Teste a sua lógica de retry

Para testar a sua lógica de retry, deve simular ou causar um erro que possa ser corrigido enquanto o seu programa ainda está em execução.

#### <a name="test-by-disconnecting-from-the-network"></a>Teste desligando-se da rede

Uma das formas de testar a sua lógica de retry é desligar o computador cliente da rede enquanto o programa está em execução. O erro é:

- **SqlException.Number** = 11001
- Mensagem: "Nenhum hospedeiro é conhecido"

Como parte da primeira tentativa de retry, pode voltar a ligar o computador do cliente à rede e, em seguida, tentar ligar-se.

Para tornar este teste prático, desligue o computador da rede antes de iniciar o seu programa. Em seguida, o seu programa reconhece um parâmetro de tempo de execução que faz com que o programa:

- Adicione temporariamente 11001 à sua lista de erros a considerar como transitórios.
- Tente a sua primeira ligação como de costume.
- Depois de o erro ser apanhado, retire 11001 da lista.
- Exiba uma mensagem que diga ao utilizador para ligar o computador à rede.
- Pausa na execução utilizando o método **Console.ReadLine** ou um diálogo com um botão OK. O utilizador pressiona a tecla Enter depois de o computador estar ligado à rede.
- Tente ligar-se novamente, esperando sucesso.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Teste escrevendo mal o nome da base de dados ao ligar

O seu programa pode escrever mal o nome do utilizador antes da primeira tentativa de ligação. O erro é:

- **SqlException.Number** = 18456
- Mensagem: "O login falhou para o utilizador 'WRONG_MyUserName'."

Como parte da primeira tentativa de retry, o seu programa pode corrigir o erro ortográfico e, em seguida, tentar ligar.

Para tornar este teste prático, o seu programa reconhece um parâmetro de tempo de execução que faz com que o programa:

- Adicione temporariamente 18456 à sua lista de erros a considerar como transitórios.
- Adicione propositadamente 'WRONG_' ao nome do utilizador.
- Depois de o erro ser apanhado, retire 18456 da lista.
- Remova 'WRONG_' do nome do utilizador.
- Tente ligar-se novamente, esperando sucesso.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>.NET Parâmetros de Ligação Para nova tentativa de ligação

Se o seu programa de cliente se ligar à Base de Dados SQL utilizando o Sistema de Classe-Quadro **.NET.Data.SqlClient.SqlConnection,** utilize .NET 4.6.1 ou mais tarde (ou .NET Core) para que possa utilizar a sua função de retry de ligação. Para mais informações sobre a funcionalidade, consulte [esta página web](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Quando construir a cadeia de [ligação](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) para o seu objeto **SqlConnection,** coordene os valores entre os seguintes parâmetros:

- **ConecttryCount**:&nbsp;&nbsp;Predefinição é 1. O alcance é de 0 a 255.
- **ConnectRetryInterval**&nbsp;&nbsp;: Predefinição é de 10 segundos. O alcance é de 1 a 60.
- **Tempo de ligação**:&nbsp;&nbsp;Predefinição é de 15 segundos. O intervalo é de 0 a 2147483647.

Especificamente, os seus valores escolhidos devem tornar a seguinte igualdade verdadeira: Tempo de ligação = ConetritryCount * ConnectionRetryInterval

Por exemplo, se a contagem for igual a 3 e o intervalo for igual a 10 segundos, um tempo de apenas 29 segundos não dá tempo suficiente ao sistema para a sua terceira e última tentativa de ligação: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Ligação vs comando

Os **parâmetros ConnectRetryCount** e **ConnectRetryInterval** permitem que o seu objeto **SqlConnection** volte a tentar a operação de ligação sem avisar ou incomodar o seu programa, como o controlo de devolução do seu programa. As repetições podem ocorrer nas seguintes situações:

- mySqlConnection.Open chamada de método
- mySqlConnection.Executar chamada de método

Há uma subtileza. Se ocorrer um erro transitório durante a sua *consulta,* o seu objeto **SqlConnection** não volta a tentar a operação de ligação. Certamente não tenta a sua consulta. No entanto, a **SqlConnection** verifica rapidamente a ligação antes de enviar a sua consulta para execução. Se a verificação rápida detetar um problema de ligação, a **SqlConnection** volta a tentar o funcionamento da ligação. Se a reprovação for bem sucedida, a sua consulta será enviada para execução.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Se O ConetryCount ser combinado com a lógica de retry da aplicação

Suponha que a sua aplicação tem uma lógica de retry personalizada robusta. Pode voltar a tentar a operação de ligação quatro vezes. Se adicionar **O ConetryInterval** e **o ConnectRetryCount** =3 à sua cadeia de ligação, aumentará a contagem de retry para 4 * 3 = 12 repetições. Talvez não pretenda um número tão elevado de repetições.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Ligações à Base de Dados SQL

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Ligação: Fio de ligação

A cadeia de ligação necessária para ligar à Base de Dados SQL é ligeiramente diferente da cadeia utilizada para ligar ao Servidor SQL. Pode copiar o fio de ligação para a sua base de dados a partir do [portal Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Ligação: endereço IP

Tem de configurar o servidor de base de dados SQL para aceitar a comunicação a partir do endereço IP do computador que acolhe o seu programa de cliente. Para configurar esta configuração, edite as definições de firewall através do [portal Azure](https://portal.azure.com/).

Se se esquecer de configurar o endereço IP, o seu programa falha com uma mensagem de erro útil que indica o endereço IP necessário.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Para mais informações, consulte as definições de firewall configure na Base de [Dados SQL](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Ligação: Portas

Normalmente, você precisa garantir que apenas a porta 1433 está aberta para comunicação de saída no computador que acolhe o seu programa de cliente.

Por exemplo, quando o seu programa de cliente está hospedado num computador Windows, pode utilizar o Windows Firewall no anfitrião para abrir a porta 1433.

1. Abra o Painel de Controlo.
2. Selecione todos os itens do Painel de Controlo**Windows Firewall** > **Definições** >  **avançadas** > Regras de**saída** > **Ações** > Nova**Regra**.

Se o seu programa de cliente estiver hospedado numa máquina virtual Azure (VM), leia Portas para além de [1433 para ADO.NET base de dados 4.5 e SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

Para obter informações sobre a configuração de portas e endereços IP, consulte a firewall da Base de [Dados Azure SQL](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Ligação: ADO.NET 4.6.2 ou mais tarde

Se o seu programa utilizar ADO.NET classes como **System.Data.SqlClient.SqlConnection** para ligar à Base de Dados SQL, recomendamos que utilize a versão 4.6.2 ou mais tarde.

#### <a name="starting-with-adonet-462"></a>Começando com ADO.NET 4.6.2

- A tentativa aberta de ligação para ser reexperimentada imediatamente para as bases de dados Azure SQL, melhorando assim o desempenho de aplicações ativadas pela nuvem.

#### <a name="starting-with-adonet-461"></a>Começando com ADO.NET 4.6.1

- Para a Base de Dados SQL, a fiabilidade é melhorada quando abre uma ligação utilizando o método **SqlConnection.Open.** O método **Open** incorpora agora mecanismos de retry de melhor esforço em resposta a falhas transitórias para determinados erros no período de tempo de ligação.
- O pool de ligação é suportado, o que inclui uma verificação eficiente de que o objeto de ligação que dá ao seu programa está funcionando.

Quando utilizar um objeto de ligação a partir de uma piscina de ligação, recomendamos que o seu programa feche temporariamente a ligação quando não estiver a ser utilizada imediatamente. Não é caro reabrir uma ligação, mas é criar uma nova ligação.

Se utilizar ADO.NET 4.0 ou mais cedo, recomendamos que faça o upgrade para o mais recente ADO.NET. A partir de agosto de 2018, você pode [baixar ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnóstico

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnóstico: Teste se os utilitários podem ligar

Se o seu programa não conseguir ligar-se à Base de Dados SQL, uma opção de diagnóstico é tentar ligar-se a um programa de utilidade. Idealmente, o utilitário conecta-se utilizando a mesma biblioteca que o seu programa utiliza.

Em qualquer computador Windows, pode experimentar estes utilitários:

- SQL Server Management Studio (ssms.exe), que se conecta utilizando ADO.NET
- `sqlcmd.exe`, que se conecta utilizando a [ODBC](https://msdn.microsoft.com/library/jj730308.aspx)

Depois de o seu programa estar ligado, teste se funciona uma consulta sQL SELECT curta.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnósticos: Verifique as portas abertas

Se suspeitar que as tentativas de ligação falham devido a problemas de porta, pode executar uma utilidade no seu computador que reporta nas configurações da porta.

Em Linux, os seguintes utilitários podem ser úteis:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Alterar o valor do exemplo para ser o seu endereço IP.

No Windows, o utilitário [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) pode ser útil. Aqui está um exemplo de execução que questionou a situação da porta num servidor de base de dados SQL e que foi executado num computador portátil:

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

### <a name="diagnostics-log-your-errors"></a>Diagnósticos: Registar os seus erros

Um problema intermitente é por vezes melhor diagnosticado pela deteção de um padrão geral ao longo de dias ou semanas.

O seu cliente pode ajudar num diagnóstico registando todos os erros que encontra. Pode ser capaz de correlacionar as entradas de registo com dados de erro que a Base de Dados SQL regista internamente.

A Enterprise Library 6 (EntLib60) oferece aulas geridas .NET para ajudar na exploração madeireira. Para mais informações, consulte [5 - Tão fácil como cair de um registo: Utilize o bloco de aplicação de registo](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnósticos: Examinar registos do sistema para erros

Aqui estão algumas declarações do Transact-SQL SELECT que consultam registos de erros e outras informações.

| Consulta de registo | Descrição |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |A visão [sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) oferece informações sobre eventos individuais, que inclui alguns que podem causar erros transitórios ou falhas de conectividade.<br/><br/>Idealmente, pode correlacionar os **valores start_time** ou **end_time** com informações sobre quando o seu programa de clientes experimentou problemas.<br/><br/>Tem de se ligar à base de dados *principal* para fazer esta consulta. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |A vista [sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) oferece contagens agregadas de tipos de eventos para diagnósticos adicionais.<br/><br/>Tem de se ligar à base de dados *principal* para fazer esta consulta. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnósticos: Pesquisa de eventos problemáticos no registo da Base de Dados SQL

Pode pesquisar entradas sobre eventos problemáticos no registo da Base de Dados SQL. Experimente a seguinte declaração de SELEÇÃO Transact-SQL na base de dados *principal:*

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

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Algumas filas devolvidas de sys.fn_xe_telemetry_blob_target_read_file

O exemplo que se segue mostra como pode ser uma fila devolvida. Os valores nulos apresentados muitas vezes não são nulos noutras linhas.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Biblioteca Empresarial 6

A Enterprise Library 6 (EntLib60) é um quadro de classes .NET que o ajuda a implementar clientes robustos de serviços na nuvem, um dos quais é o serviço de base de dados SQL. Para localizar tópicos dedicados a cada área em que o EntLib60 pode ajudar, consulte a [Enterprise Library 6 - abril 2013](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

A lógica de retry para lidar com erros transitórios é uma área em que o EntLib60 pode ajudar. Para mais informações, consulte [4 - Perseverança, segredo de todos os triunfos: Utilize o Bloco](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)transitório de aplicação de manuseamento de falhas .

> [!NOTE]
> O código fonte para EntLib60 está disponível para download público a partir do [Centro de Descarregamento](https://go.microsoft.com/fwlink/p/?LinkID=290898). A Microsoft não tem planos para fazer mais atualizações de funcionalidades ou atualizações de manutenção para o EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Aulas entLib60 para erros transitórios e retentativa

As seguintes aulas EntLib60 são particularmente úteis para a lógica de retry. Todas estas aulas são encontradas dentro ou sob o nome **microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

No espaço de nome **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

- **Classe RetryPolicy**
  - **Método executeActionAction**
- Classe **ExponencialBackoff**
- **Classe SqlDatabaseTransientErrorDetectionStrategy**
- **Classe ReliableSqlConnection**
  - **Método executeCommand**

No espaço de nome **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **Classe AlwaysTransientErrorDetectionStrategy**
- **Classe NeverTransientErrorDetectionStrategy**

Aqui estão alguns links para informações sobre o EntLib60:

- Download gratuito de livro: [Guia do Desenvolvedor para a Microsoft Enterprise Library, 2ª edição](https://www.microsoft.com/download/details.aspx?id=41145).
- Boas práticas: [A orientação geral](../best-practices-retry-general.md) de retry tem uma excelente discussão aprofundada da lógica de retry.
- Download nuGet: [Enterprise Library - Transitório Fault Handling Application Block 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: O bloco de exploração madeireira

- O bloco de madeira é uma solução altamente flexível e configurável que pode utilizar para:
  - Crie e guarde mensagens de log em uma grande variedade de locais.
  - Categorize e filtrar mensagens.
  - Recolher informações contextuais que são úteis para depuração e rastreio, bem como para requisitos de auditoria e exploração madeireira geral.
- O bloco de registo resumi a funcionalidade de registo do destino de registo para que o código de aplicação seja consistente, independentemente da localização e do tipo da loja de registo saqueta do alvo.

Para mais informações, consulte [5 - Tão fácil como cair de um registo: Utilize o bloco de aplicação de registo](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Código fonte do método EntLib60 IsTransient

Em seguida, da classe **SqlDatabaseTransientErrorDetectionStrategy,** encontra-se o código fonte C# para o método **IsTransient.** O código fonte esclarece quais os erros considerados transitórios e dignos de repetição, a partir de abril de 2013.

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

- [Bibliotecas de ligação para Base de Dados SQL e Servidor SQL](sql-database-libraries.md)
- [Pooling de conexão SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [ *Retry* é uma biblioteca de retry de propósito geral Apache 2.0, escrita em Python,](https://pypi.python.org/pypi/retrying) para simplificar a tarefa de adicionar comportamento de retry a praticamente qualquer coisa.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
